## dubbo

- 概念：
	- Dubbo是阿里巴巴公司开源的一个高性能优秀的服务框架，使得应用可通过高性能的RPC实现服务的输出和输入功能，可以和Spring框架无缝集成。
	- 随着互联网的发展，网站应用的规模不断扩大，常规的垂直应用架构已无法应对，分布式服务架构以及流动计算架构势在必行，需要一个治理系统确保架构有条不紊的演进。
	- 我们先来看一张图：
	- ![](https://i.imgur.com/JbDCl7I.png)
	- Provider
	- 暴露服务方称之为“服务提供者”,(如果用spring可以理解为通过spring配置文件中配置服务提供者)
	- Consumer
	- 调用远程服务方称之为“服务消费者”。
	- Registry
	- 服务注册与发现的中心目录服务称之为“服务注册中心”(以zookeeper为例子，官方也推荐使用)，zookeeper会在最后的时候讲解说明安装；
	- Monitor
	- 统计服务的调用次数和调用时间的日志服务称之为“服务监控中心”(dubbo-admin-2.6.0.war) 。


- 通过上图我们可以很清晰的看到，我们先用通俗易懂的方式去解释：分布式项目在启动时，需要发布的服务模块（Provider）先从容器(spring)中配置了要发布服务的接口（服务提供者），接着在注册中心注册，随后表现层模块（Consumer）会在spring（表现出只能是spring-mvc.xml了吧）配置文件中引用dubbo暴露的服务。
- 接着去注册中心查找对应的服务（subscribe）有没有，如果有的话，就会执行调用服务（invoke）；
- 而Monitor，可以理解为监控服务之间的调用次数，后面我们会说明这个；

- 而在具体的项目中是如何配置dubbo的呢？
	- 首先你需要在你发布服务的spring配置文件中引入dubbo的约束：
		- `xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"` 
		- `http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd`
		- 关于约束的导入可以本地引入，这里就不再细说了
	- 其次，在发布服务的配置文件中配置以下参数：
		- ![](https://i.imgur.com/Djvt60i.png)
		- name：发布服务的工程名称
		- address：远程地址:端口号(zookeeper默认端口号就是2181)
		- interface：发布服务的接口；
		- ref：引用此接口的实现类；
		- timeout：600000毫秒=10分钟（可用于debug调式中不会出现报错）
	- 随后，在引用服务的spring配置文件中也要加入上述约束，且配置文件内配置如下：
		- ![](https://i.imgur.com/QMq6bRe.png)
- 随后，启动你的工程即可，启动顺序别搞乱了，先将zookeeper启动了，按照逻辑需要先启动发布服务的模块，随后再启动引用服务的模块；

- 最后，我们原本在上图的时候已经看到过，Monitor是可以监控到其调用次数的，我们可以使用Alibaba提供的一个dubbo监控的控件，对应的可以在github上下载源码运行生成war包，放到tomcat目录下的webapp文件夹，启动tomcat，输入对应的url地址即可看到dubbo监控的首页：
- ![](https://i.imgur.com/ip0pn8T.png)
- 点击搜索的按钮即可看到你所有发布服务的应用
- ![](https://i.imgur.com/fzBUcHd.png)
- ![](https://i.imgur.com/TS9MyRa.png)
- ![](https://i.imgur.com/tsmirPG.png)

#### 以上就是dubbo的简易介绍


## zookeeper
## zookeeper简单易懂的入门
- 概念：
	- ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务的组件，我们也可以理解为注册中心，一般搭配dubbo组件去使用；同时也是Hadoop和Hbase的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。
	- zookeeper负责服务地址的注册与查找，相当于目录服务，服务的提供者与消费者只在启动时与注册中心交互，通过在注册中心注册后发布或引用服务，而注册中心本身不转发请求，且压力比较小，如果使用的是dubbo-2.3.3版本以上，建议使用zookeeper。
	- zookeeper是Apacahe Hadoop的子项目，是一个树型的目录服务，支持变更推送，适合作为Dubbo服务的注册中心，工业强度较高，可用于生产环境。
- 安装：
	- **安装环境**：
	- **Linux：centos6.4**
	- **Jdk:1.7以上版本**

	- Zookeeper是java开发的可以运行在windows、linux环境。**需要先安装jdk**。
	- 安装步骤：
	- 第一步：安装jdk
	- 第二步：把zookeeper的压缩包上传到linux系统。
	- 第三步：解压缩压缩包
	- `tar -zxvf zookeeper-3.4.6.tar.gz -C /usr/local`
	- 第四步：进入zookeeper-3.4.6目录，创建data文件夹。
	- 第五步：把`zoo_sample.cfg`改名为`zoo.cfg`
	-` [root@localhost conf]# mv zoo_sample.cfg zoo.cfg`
	- 第六步：修改data属性：`dataDir=/root/zookeeper-3.4.6/data`
	- 第七步：启动zookeeper，在zookeeper安装目录的bin目录下：
	- `[root@localhost bin]# ./zkServer.sh start`
	- 关闭：`[root@localhost bin]# ./zkServer.sh stop`
	- 查看状态：`[root@localhost bin]# ./zkServer.sh status`
		- 如何判定zookeeper启动成功？
		- ![](https://i.imgur.com/WShnzvE.png)
		- 当看到：`Mode: standalone` 就代表zookeeper启动成功了！
	- zookeeper默认端口号是2181，需要在Linux开启此端口后外部才能访问：
	- `/sbin/iptables -I INPUT -p tcp --dport 2181 -j ACCEPT`
	- 保存规则到防火墙中：
	- `/etc/rc.d/init.d/iptables save`
	
	- 或者：
	- 注意：需要关闭防火墙。
	- `service iptables stop`
	- 永久关闭修改配置开机不启动防火墙：
	- `chkconfig iptables off`
	- 如果不能成功启动zookeeper，需要删除`data`目录下的`zookeeper_server.pid`文件。


- 每次启动项目的时候，先去Linux环境下启动zookeeper，否则你的demo发布服务与注册服务无法进行。

