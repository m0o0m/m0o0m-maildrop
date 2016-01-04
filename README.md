MailDrop
========

See [MailDrop in action](http://maildrop.cc).

MailDrop is an open-source, scalable, high-performance version of Mailinator,
a "temporary inbox" that you can give out when you don't want to give out
your real e-mail address. MailDrop inboxes are designed to be quick and
disposable.

The design goals are to be roughly 90% of the speed of Mailinator, while
adding additional functionality and the ability to horizontally scale
quickly and easily.

Where Mailinator runs within one JVM, MailDrop splits the SMTP and web 
applications into separate JVMs, while using Redis as its main data store.
This allows for a more fluid application architecture, while still retaining
the speed expected for a high-speed mail and web app.

MailDrop is written in Scala, with heavy use of Akka actors and the Play
Framework. Functionality includes:

* Antispam modules contributed from [Heluna](https://heluna.com/) for
senders and data
* 90% of all spam attempts rejected
* Network blacklists
* IP connection and message subject limiting
* Reputation-based blocking
* SPF checking
* Greylisting
* Alternate inbox aliases
* Strip message attachments
* Message size limits
* SMTP configuration done in one file
* Easy-to-modify website, written in LESS and CoffeeScript


Requirements
------------

* [SBT 0.13+](http://www.scala-sbt.org/)
* [PlayFramework 2.3.4+](http://www.playframework.com/)
* [Redis 2.6+](http://redis.io/)

For hardware, an Amazon EC2 small instance should be good to begin.
MailDrop should take 512M of RAM for the SMTP module, 512M of RAM for
the web module, and 512M of RAM for the Redis datastore. In practice,
CPU is not an issue; MailDrop spends most of its time waiting on disk
or network IO.


Installation
------------

### Clone the repository into a local directory

This will give you three subdirectories, "common" is the shared set of utility
classes and models, "smtp" is the mail transfer agent, and "web" is the
website.

### Create the SMTP server

Go into "smtp" and run "sbt compile". This will create a jar of the MailDrop
SMTP server. If you like, you can create a single jar with all dependencies
included by running "sbt assembly".

To run the SMTP server, use "java -jar (jarfile) MailDrop". If you want to use a
custom configuration, use "java -Dconfig.file=/path/to/your/application.conf
-jar (jarfile) MailDrop".

### Create the web server

Go into "web" and run "play dist". This will create a zipfile of the MailDrop
website. (More information about how to run a Play web app is located on the
[Play Framework site](http://www.playframework.com/))

To run the web server, simply use the "start" command inside the zipfile, or
again to specify a custom configuration use start 
-Dconfig.file=/path/to/your/application.conf


Changelog
---------

Version 2.0 is a rewrite of the internals of MailDrop. The site itself has
the same look and feel, but the way messages are dealt with is slightly
different.

The primary change is a switch to
[rediscala](https://github.com/etaty/rediscala) to connect to the Redis
instance. rediscala is a Reactive driver and performs much better than
the old Redis client.

Next, many of the actors and supervisors have been tossed in favor of
simple Scala futures. This has simplified the codebase while still
allowing for high performance-- MailDrop just won't be able to be as
finely tuned (custom dispatchers, guaranteed message delivery, etc).

Messages are now stored in Redis in json format instead of as serialized
ByteStrings. The overhead of json parsing on the website is minimal and
allows for a better overall view of the data inside Redis.

Last, Play has been updated to the 2.3 branch, along with automatic
asset versioning and a much cleaner way to cache assets. Website
performance should be increased vs. the previous version of MailDrop.

信箱
见信箱中的作用。

信箱是一个开源的，可扩展的，高性能的Mailinator，“临时收件箱”，你可以给出来的时候你不希望给您的真实电子邮件地址的版本。信箱收件箱被设计成快速，一次性的。

设计目标是要Mailinator的速度的90％左右，同时添加额外的功能性和快速，方便地横向扩展的能力。

凡Mailinator在一个JVM上运行，信箱拆分SMTP和Web应用程序转换为独立的JVM，同时使用Redis的作为其主要的数据存储。这允许更流体应用架构，同时仍保留预期高速邮件和Web应用程序的速度。

信箱是用Scala编写，大量使用阿卡演员和戏剧框架。功能包括：

从反垃圾邮件模块贡献Heluna的发送者和数据
90％的垃圾邮件试图拒绝
网络黑名单
IP连接，邮件主题限制
基于信誉的拦截
SPF检查
灰名单
备用邮箱别名
条消息附件
邮件大小限制
在一个文件中的SMTP配置完成
易于修改的网站，写在LESS和CoffeeScript的
要求

SBT 0.13+
PlayFramework 2.3.4+
Redis的2.6+
对于硬件，一个Amazon EC2实例小应该是不错的开始。信箱应采取的RAM 512M的SMTP模块，512M的RAM用于Web模块，以及512M RAM的Redis的数据存储。在实践中，CPU不是一个问题; 信箱花费大量的时间等待磁盘或网络IO。

安装

克隆库到本地目录

这会给你三个子目录，“共同”，是实用工具类和模型的共享设置，“SMTP”是邮件传输代理，和“网络”的网站。

创建SMTP服务器

进入“SMTP”并运行“SBT编译”。这将创建信箱SMTP服务器的罐子。如果你喜欢，你可以创建包括运行“SBT集结号”所有依赖一个JAR。

要运行SMTP服务器，使用“Java的罐子（jar文件）信箱”。如果你想使用自定义的配置，使用的“java -Dconfig.file = /路径/要/你/ application.conf罐子（jar文件）信箱”。

创建web服务器

进入“网络”并运行“戏DIST”。这将创建信箱网站的zip文件。（有关如何运行一个播放web应用程序的更多信息，位于上 播放框架的网站）

要运行Web服务器，只需使用“开始”命令压缩文件内，或再指定一个自定义的配置使用启动-Dconfig.file = /路径/要/你/ application.conf

更新日志

2.0版是信箱的内部的重写。该网站本身具有相同的外观和感觉，但消息被处理的方式略有不同。

主的变化是一个开关来 rediscala连接到Redis的实例。rediscala是一个反应性驱动，并执行比老Redis的客户要好得多。

其次，许多演员和监事都被扔在赞成简单的斯卡拉期货。这简化了代码库，同时仍然允许高performance--信箱只是将不能被作为微调（自定义调度，保证的消息传送等）。

消息现在存储在Redis的JSON格式，而不是作为序列化的字节串。JSON解析网站上的开销是最小，并允许里面的Redis的数据的更好的整体图。

最后，游戏已经更新到了2.3支，以及自动资产版本控制和更清洁的方式来缓存资产。网站性能应增加与以前版本的信箱中。
