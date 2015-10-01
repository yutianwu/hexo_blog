---
layout: post
title:  "Docker文件系统"
categories: docker
date:   2015-05-20 13:11:20
---

##Docker文件系统概述

典型的Linux文件系统由bootfs和rootfs两部分组成，bootfs(boot file system)主要包含 bootloader和kernel，bootloader主要是引导加载kernel，当kernel被加载到内存中后 bootfs就被umount了。 rootfs (root file system) 包含的就是典型 Linux 系统中的/dev，/proc，/bin，/etc等标准目录和文件。

![](/img/docker-filesystem-1.png)

联合文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。

联合文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。另外，不同 Docker 容器就可以共享一些基础的文件系统层，同时再加上自己独有的改动层，大大提高了存储的效率。
Aufs也是一种Union FS, 支持将不同的目录挂载到同一个虚拟文件系统下，并实现一种layer的概念。Aufs将挂载到同一虚拟文件系统下的多个目录分别设置成read-only，read-write以及whiteout-able权限，对read-only目录只能读，而写操作只能实施在read-write目录中。重点在于，写操作是在read-only上的一种增量操作，不影响read-only目录。当挂载目录的时候要严格按照各目录之间的这种增量关系，将被增量操作的目录优先于在它基础上增量操作的目录挂载，待所有目录挂载结束了，继续挂载一个read-write目录，如此便形成了一种层次结构。

Docker镜像的典型结构如下图。传统的Linux加载bootfs时会先将rootfs设为read-only，然后在系统自检之后将rootfs从read-only改为read-write，然后我们就可以在rootfs上进行写和读的操作了。但Docker的镜像却不是这样，它在bootfs自检完毕之后并不会把rootfs的read-only改为read-write。而是利用union mount（UnionFS的一种挂载机制）将一个或多个read-only的rootfs加载到之前的read-only的rootfs层之上。在加载了这么多层的rootfs之后，仍然让它看起来只像是一个文件系统，在Docker的体系里把union mount的这些read-only的rootfs叫做Docker的镜像。但是，此时的每一层rootfs都是read-only的，我们此时还不能对其进行操作。当我们创建一个容器，也就是将Docker镜像进行实例化，系统会在一层或是多层read-only的rootfs之上分配一层空的read-write的rootfs。

![](/img/docker-filesystem-2.png)

Docker 目前支持的联合文件系统种类包括 AUFS, btrfs, vfs 和 DeviceMapper。

##Docker的文件系统驱动

Docker定义了一个驱动原型的接口，`ProtoDriver`:

``` go
type ProtoDriver interface {
    String() string
    Create(id, parent string) error
    Remove(id string) error
    Get(id, mountLabel string) (dir string, err error)
    Put(id string) error
    Exists(id string) bool
    Status() [][2]string
    Cleanup() error
}
```

还定义了一个`Driver`的接口，该接口继承了`ProtoDriver`:
``` go
type Driver interface {
    ProtoDriver
    Diff(id, parent string) (archive.Archive, error)
    Changes(id, parent string) ([]archive.Change, error)
    ApplyDiff(id, parent string, diff archive.ArchiveReader) (size int64, err error)
    DiffSize(id, parent string) (size int64, err error)
}
```

ProtoDriver中定义了一些各个文件系统所需要的基本操作，比如Create,Remove等。Driver继承了ProtoDriver，并增加了几个函数，比如Diff,ApplyDiff等，这主要是为了向aufs这种有layer概念的文件系统，还有像devicemapper这种有snapshot概念的文件系统构建设备提供方便。
像aufs直接是支持Diff,ApplyDiff等操作的，所以aufs的驱动直接实现了这个Driver这个接口。但是devicemapper并不直接支持这种操作，所以这里又定义了一个naiveDiffDriver的struct，也继承了ProtoDriver,但是又添加了Driver中多出的几个函数。这样，devicemapper等不直接支持那些操作的文件系统也可以间接得通过naiveDiffDriver实现这些功能。

## Docker对文件系统的初始化

![](/img/docker-filesystem-3.png)

在initFunc中，会调用已经注册的文件系统驱动来进行初始化，或者如果没有注册过驱动，会按照文件系统的优先级来对文件系统进行初始化。
例如，在支持aufs的文件系统中，就会调用aufs文件系统提供的initFunc进行初始化；在支持devicemapper的文件系统中，就会调用devicemapper的initFunc进行初始化。

##关键流程

###下载镜像

![](/img/docker-filesystem-4.png)

从上图可以看出，在下载镜像时，docker先把镜像从hub上下载下来，然后注册该镜像。注册的流程就是以该镜像的parent的设备为基础，构建一个新的设备。然后将下载下来的image所做的改动应用到新建的设备上，这样就形成了一个以该image的ID为名的设备了。其实就是一个镜像。

###创建容器
![](/img/docker-filesystem-5.png)

从上图我们可以看到，在创建容器时，docker创建了两个设备，一个设备的名称为container.ID-init，简称为initID,另一个名称为container.ID。intid是根据imageId这个设备创建的，从前面下载镜像的流程中我们可以知道，只要存在image，那么imageID这个镜像也就会存在。创建完成initID后，会对该设备进行初始化操作，比如说导入一些跟容器相关的配置文件等。然后根据初始化完成的设备initID构建一个名为container.ID的设备，这个设备就是容器的设备了，将该设备挂载到相应的挂载点上，我们就可以看到创建的容器了。Docker本身也是这么做的。

###创建镜像

创建镜像的流程如下：
![](/img/docker-filesystem-6.png)
当我们使用commit命令创建一个镜像时，首先导出我们在容器中所做的修改，这个功能由函数Diff来实现。此时将要创建的image的ID为imgID,而容器所使用的镜像ID为parentId。我们首先根据parentID这个设备来新建一个名为imgID的设备。然后我们将得到的修改rwTar添加到imgID这个设备中。这样，我们就得到了一个新的镜像。

##Devicemapper
###背景介绍
#### Snapshot

Snapshot是Lvm提供的一种特性，它可以在不中断服务运行的情况下为the origin（original device）创建一个虚拟快照(Snapshot)，它具有以下几个特点：

 + 当the origin内容发生变化时，snapshot对变化的部分做一个拷贝以用来对the origin进行重构。
 + 因为只对变化的部分做拷贝，所以Lvm的Snapshot在读操作频繁而写操作不频繁的情况下占用很少的一部分空间便能完成特定任务。
 + 当Snapshot大小耗尽或者远大于实际需求时，我们可以对其大小进行调节。
 + 当对Snapshot的数据进行写操作的时候，Snapshot实施相应操作，并丢弃从the origin的拷贝，以后的操作以写操作之后Snapshot中的数据为准。
 + 在某些发行版的Linux系统下，可以使用lvconvert的--merge选项将Snapshot合并回the origin。


####Thin-Provisioning

Thin-Provisioning是一项利用虚拟化方法减少物理存储部署的技术，可最大限度提升存储空间利用率。下图中展示了某位用户向服务器管理员请求分配10TB的资源的情形。实际情况中这个数值往往是峰值，根据使用情况，分配2TB就已足够。因此，系统管理员准备2TB的物理存储，并给服务器分配10TB的虚拟卷。服务器即可基于仅占虚拟卷容量1/5的现有物理磁盘池开始运行。这样的“始于小”方案能够实现更高效地利用存储容量。

![](/img/docker-filesystem-7.png)

Thin-provisioning Snapshot结合Thin-Provisioning和Snapshot两种技术，允许多个虚拟设备同时挂载到一个数据卷以达到数据共享的目的。Thin-Provisioning Snapshot的特点如下：

+ 可以将不同的snaptshot挂载到同一个the origin上，节省了磁盘空间。
+ 当多个Snapshot挂载到了同一个the origin上，并在the origin上发生写操作时，将会触发COW操作。这样不会降低效率。
+ Thin-Provisioning Snapshot支持递归操作，即一个Snapshot可以作为另一个Snapshot的the origin，且没有深度限制。
+ 在Snapshot上可以创建一个逻辑卷，这个逻辑卷在实际写操作（COW，Snapshot写操作）发生之前是不占用磁盘空间的。

Thin-Provisioning Snapshot虽然有诸多优点，但是也有很多不足之处，例如大小固定等问题。
Thin-Provisioning Snapshot是作为device mapper的一个target在内核中实现的。Device mapper 是Linux 2.6内核中提供的一种从逻辑设备到物理设备的映射框架机制。在该机制下，用户可以很方便的根据自己的需要制定实现存储资源的管理策略，如条带化，镜像，快照等。
Device Mapper主要包含内核空间的映射和用户空间的device mapper库及dmsetup工具。Device Mapper库是对ioctl、用户空间创建删除Device Mapper逻辑设备所需必要操作的封装，dmsetup是一个提供给用户直接可用的创建删除device mapper设备的命令行工具

###devicemapper概述
Docker在初始化过程中，会在`/var/lib/docker/devicemapper/devicemapper`目录下创建一个100G的稀疏文件`data`，用于存储数据，和一个2G的稀疏文件`metadata`用于存储元数据然后分别附加到回环块设备`/dev/loop0`和`/dev/loop1`。然后基于回环块设备创建`thin pool`
``` bash
/dev/loop0: [64769]:135007216 (/var/lib/docker/devicemapper/devicemapper/data)
/dev/loop1: [64769]:135007217 (/var/lib/docker/devicemapper/devicemapper/metadata)
```

在创建容器时，devicemapper会在thin pool中基于一个基础镜像新建一个默认大小为10G的设备，然后将设备的信息写入到metadata中。我们可以通过修改docker的启动参数来调整data文件和metadata文件的大小，比如将data文件的大小修改为200G，metadata文件大小修改为4G，默认设备大小修改为20G：
```bash
docker -d --storage-opt dm.basesize=20G --storage-opt dm.loopdatasize=200G --storage-opt dm.loopmetadatasize=4G
```
当修改配置，重启docker服务时，所有的镜像，容器都会被删除，所以在重新设置时要注意做好备份。

###关键流程

####初始化devicemapper
![](/img/docker-filesystem-8.png)
初始化devicemapper时，docker会基于data文件创建一个thin pool，用于存储容器设备。然后建立一个基础镜像baseImage。如果创建设备时没有指定父设备的时候，就会以baseImage为父设备新建一个设备。

####创建设备
![](/img/docker-filesystem-9.png)
从上可以看出，devicemapper创建设备的流程为：先根据父设备创建一个快照，生成一个新的设备，然后注册该设备，即将该设备的元数据写入metadata文件中。

##AUFS

###AUFS概述
AUFS (AnotherUnionFS) 是一种 Union FS, 简单来说就是支持将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)的文件系统。 Aufs driver是docker 最早支持的driver，但是aufs只是linux内核的一个补丁集而且不太可以会被合并加入到linux内核中。但是由于aufs是唯一一个 storage driver可以实现容器间共享可执行及可共享的运行库, 所以当你跑成千上百个拥有相同程序代码或者运行库时时候，aufs是个相当不错的选择。

####镜像存储

在AUFS中，镜像存储的位置为`/var/lib/docker/aufs`。Aufs目录的结构如下：
```bash
aufs/
├── diff
├── layers
└── mnt
```

其中`diff`目录下存储的是镜像的具体信息；`layers`目录下存储的是镜像的继承结构,`mnt`是启动容器rootfs的挂载目录。下面分别介绍一下各个目录的具体信息。

+ Layers目录
`Layer`目录下存放的是每个镜像的继承关系文件。比如说`ubuntu:latest`镜像的imageid为`2103b00b3fdf1d26a86aded36ae73c1c425def0f779a6e69073b3b77377df348`，那么在这个目录下就会存在一个名为`2103b00b3fdf1d26a86aded36ae73c1c425def0f779a6e69073b3b77377df348`的文件，存储了镜像的继承结构，文件的内容为：
```bash
4faa69f72743ce3a18508e840ff84598952fc05bd1de5fd54c6bc0f8ca835884
76b658ecb5644a4aca23b35de695803ad2e223da087d4f8015016021bd970169
f0dde87450ec8236a64aebd3e8b499fe2772fca5e837ecbfa97bd8ae380c605e
511136ea3c5a64f264b78b5433614aec563103b4d4702f3ba7d4d2698e22c158
```

+ Diff目录
因为docker中，每一个镜像都是基于上一层镜像。所以在`diff`目录下，存储的是每一个镜像相对于上一层镜像所做的增量修改。如果只有`ubuntu:latest`这一个镜像，那么目录下就会存在以下几个目录：
```bash
2103b00b3fdf1d26a86aded36ae73c1c425def0f779a6e69073b3b77377df348
4faa69f72743ce3a18508e840ff84598952fc05bd1de5fd54c6bc0f8ca835884
76b658ecb5644a4aca23b35de695803ad2e223da087d4f8015016021bd970169
f0dde87450ec8236a64aebd3e8b499fe2772fca5e837ecbfa97bd8ae380c605e
511136ea3c5a64f264b78b5433614aec563103b4d4702f3ba7d4d2698e22c158
```
每一个目录下保存的即为该层镜像基于上层镜像所做的修改。
+ mnt目录
`mnt`是挂载文件系统的目录，比如说启动一个`ubuntu:lastest`的容器，在`mnt`下就能够看到容器id的目录，目录下是容器的rootfs.

####创建容器时的文件操作
创建一个基于`ubuntu:lastest`的容器并启动，假如容器id为`3eaa0e6ed18f949e0d0cf2ddfaa44c166063e8753676e2ec2c61ed2d2bd134f1`，我们就会发现在`diff`目录下会多出两个目录：
```bash
3eaa0e6ed18f949e0d0cf2ddfaa44c166063e8753676e2ec2c61ed2d2bd134f1
3eaa0e6ed18f949e0d0cf2ddfaa44c166063e8753676e2ec2c61ed2d2bd134f1-init
```

其中`3eaa0e6ed18f949e0d0cf2ddfaa44c166063e8753676e2ec2c61ed2d2bd134f1-init`存储的是容器的初始信息，比如说一些在启动容器时需要加载的一些配置文件，比如`hosts`, `resolv.conf`等。而`3eaa0e6ed18f949e0d0cf2ddfaa44c166063e8753676e2ec2c61ed2d2bd134f1`目录下存储的是在容器中所做的修改。如果我们在容器中新建一个名为`test`的大小为`10M`的文件，那么我们会在`ec872d7343abdcf14ec900a0667363e78eaf603fee61f09ba6e6b41773f14f85`中看到该`test`文件，而`3eaa0e6ed18f949e0d0cf2ddfaa44c166063e8753676e2ec2c61ed2d2bd134f1-init`目录中不会有什么修改。
此时，我们如果commit该容器，得到一个imageid为`ec872d7343abdcf14ec900a0667363e78eaf603fee61f09ba6e6b41773f14f85`的镜像。而diff目录下以该imageid为目录名的目录下存储的正是该`test`文件。

###关键流程
####aufs的初始化

Aufs在初始化Init函数中主要完成了以下几个操作：

+ 调用surportsAufs函数加载Aufs模块。
+ 调用MakePrivate在系统中为/var/lib/docker/aufs创建一个挂载点。这里的实现原理与mount --bind命令一样，只不过mount命令的源文件夹和目的文件夹一样，在系统中只创建了挂载点而已。并且这个挂载点的内容即不受源文件夹的影响也不影响源文件夹。
+ 最后，在/var/lib/docker/aufs创建mnt， diff， layers文件夹。mnt文件夹为容器的挂载点目录，每一个容器在mnt下都有一个长ID目录，对应为该容器的rootfs的挂载点。diff有着与mnt中对应的长ID目录，这里的每个目录对应Docker 镜像的一个layer层，里面存放的是该layer相比较于父layer变化的内容。注意： 这里才是存放我们在容器中看到的内容的地方，比如/usr, /bin等等。

####创建设备
![](/img/docker-filesystem-10.png)
从上可以看出，aufs创建设备的步骤很简单，首先是在mnt,diff目录下创建相应的目录，然后创建layers文件，里面记录的是image的层次关系。



##参考文献
http://www.infoq.com/cn/articles/analysis-of-docker-file-system-aufs-and-devicemapper/(大部分都是从这摘抄过来的，感谢作者写得这么细致)
