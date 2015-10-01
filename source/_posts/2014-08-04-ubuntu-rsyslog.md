---
layout: post
title:  "Ubuntu 12.04 配置两级Rsyslog"
category: linux
date:   2014-08-04
---

###升级Rsyslog
由于要使用Rsyslog的日志动态文件名的功能，必须将Rsyslog升级到高的版本（ubuntu上自带的版本为5.8.*）。升级的方式使用`apt-get install`
```
    1.修改/etc/apt/sources.list,添加如下两行：
    deb http://ubuntu.adiscon.com/v7-stable precise/
    deb-src http://ubuntu.adiscon.com/v7-stable precise/
    2.添加PGP Key
    sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com AEF0CF8E
    gpg --export --armor AEF0CF8E | sudo apt-key add -
    2.sudo apt-get update && apt-get upgrade
    如果出现：
    The following packages have been kept back:
    rsyslog
    使用apt-get install rsyslog安装
```
###修改配置文件/etc/rsyslog.conf

```
    1.由于升级版本后配置文件还是使用原来的，在服务器端和客户端都注释掉如下几行：
    #$FileOwner syslog                                       
    #$FileGroup adm
    #$FileCreateMode 0640                                    
    #$DirCreateMode 0755                                     
    #$Umask 0022
    #$PrivDropToUser syslog
    #$PrivDropToGroup syslog  
    （为什么要注释这几行，应该是rsyslog在ubuntu上的bug，特别是Ubuntu12.04，
    如果注释的话，没法创建动态文件名的日志，但是注释掉的话，文件夹和文件都是使
    用root创建的：
    drwx------  2 root      root   4096  8Ղ 21 09:27 ubuntu/
    -rw-r--r--  1 root      root  31605  8Ղ 21 14:40 appversion.log
    ）
    2.服务器端配置：
    #使用tcp协议接收日志
    module(load="imtcp") #加载模块imtcp
    input(type="imtcp" port="514")    #启动端口监听
    #根据不同的app_version（uuid，格式为xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx）
    #生成不同的文件
    $template DynFile,"/var/log/%rawmsg:1:36%.log"

    *.*     -?DynFile #以后可以改进，只是对其他机器的日志进行过滤
    3.客户端配置：
    module(load="imudp")    #加载模块imdup
    input(type="imudp" port="514") #启动端口监听，接收来自Log4j的日志，因为是本地，所以可以使用udp
    
    #在消息的前面添加app_version
    template (name="tpl" type="list") {
      constant(value="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx")
      property(name="rawmsg")
      constant(value="\n")
    }
 
    local1.* @@10.1.59.241:514;tpl #发送到服务端，使用tcp协议
```
###以Log4j为例，将Log4j的日志写入本地rsyslog，然后发送给日志服务器
```
    1.log4j.properties的配置

    log4j.rootLogger=DEBUG, SYSLOG 
    log4j.appender.SYSLOG=org.apache.log4j.net.SyslogAppender  
    log4j.appender.SYSLOG.syslogHost=127.0.0.1
    log4j.appender.syslog.Threshold=DEBUG  
    log4j.appender.SYSLOG.layout=org.apache.log4j.PatternLayout  
    log4j.appender.SYSLOG.layout.ConversionPattern=%-4r [%t] %-5p %c %x - %m%n  
    log4j.appender.SYSLOG.Header=true
    log4j.appender.SYSLOG.Facility=local1
    
    2.java代码示例：
    
    import org.apache.log4j.Logger;
    public class Log {
        private static Logger logger = Logger.getLogger(LogTest.class);
        public static void main(String[] args) {
            logger.debug("this is debug message");
            logger.info("this is info message");
        }
    }
```