
#shadowsocks+digitalOcean科学上网体验教程

----

最近风声越来越紧，民众们纷纷表示科学上网的途径受到严格的限制，我尝试了很多的途径试图找到最好的科学上网方式，最终让发现了最好的方式：那就是神器shadowsocks，本篇文章记录下了shadowsocks+digitalOcean服务器部署的每一步，期间遇到过不少的困难，也一并记录，希望能够给后面的人减少不必要的麻烦。

##服务器部署

-----
###为什么选择digitalOcean呢？

可能有很多其他的原因：比如ssd。但是我最想说的是便宜好么？而且注册就送10美元，以下是详细的价格表，我选择的是5美元每月的服务器，对于科学上网来说已经是非常足够了。所以如果选择了5美元每月的服务器，送的10美元足以支撑两个月了。经过几天的使用发现速率非常稳定，在U2B上看720P的视频一点儿都不卡，在线直播都没有任何问题。

###为什么要自己租个服务器？

之前有用过https://www.shadowsocks.net/上面别人分享的账号，但是很多服务器要不是连不上，要不就是被改密码，服务得不到保障，后来就每月花20块钱买vpn，虽然密码不会被改掉，但是网速很不稳定，每到上网的高峰期体验会非常差，后来发现了digitalOcean，只要部署好了，电脑手机都能一键break wall，十分方便，而且速率非常稳定。



###注册digitalOcean账号

这是digitalOcean的地址：digitalOcean，点击右上角sign up输入邮箱，密码就能注册了。注册之后会收到两封邮件：一封是确认注册的，另一封是奖励10美元的邮件，点击确认注册后，会出现填写信用卡的页面：



填写信用卡信息，注意这里是不会扣费的。

填写完成后，点击面板左侧的按钮开始创建服务器。

1：填写服务器的名称

2：选择服务器的类型，我选择的是5美元每月的服务器。

3：选择地区，我选择的是san francisco，ping的结果是100ms的延迟左右，非常不错，如果大家有选择其他的地区可以分享下测试的时延。

4：选择image，我选择的是centos 6.5 X64

5：最后点击create droplet，大概只需要等待60秒的时间就完成了，速度是不是飞一般的赶脚？



##建立shadowsocks服务端：

下面是droplet的控制面板，点击acces，再点击console access进入远程终端的连接。



初次进入需要输入原始的账号密码，账号是root，密码会在注册邮箱中找到（在创建完droplet后发送至邮箱），输入完原始的账号密码后，系统会让你再次输入原始的密码来改变密码，所以又要再次输入三次密码，第一次是原始密码，第二，三次是修改后密码。



然后就是配置服务端了，大家可以查看github上的官方教程：https://github.com/clowwindy/shadowsocks

首先是安装一些必要的组件：在服务端输入以下指令：

yum install m2crypto python-setuptools
easy_install pip
pip install shadowsocks
部分安装需要输入y以确认。
安装完毕之后，需要创建一个文件用来储存服务器的参数配置，命令如下： vi  /etc/shadowsocks.json

然后根据以下的模式编辑这个文件：

{
    "server":"0.0.0.0",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false,
    "workers": 1
}
NameExplanation
server_portthe address your server listens
server_porttheserver port
local_addressthe address your local listens
local_portlocal port
passwordpassword used for encryption
timeoutin seconds
methoddefault: "aes-256-cfb", see Encryption
fast_openuse TCP_FASTOPEN, true / false
workersnumber of workers, available on Unix/Linux vi的基本使用可以在这里找到：http://www.cnblogs.com/sunormoon/archive/2012/02/10/2345326.html
这里可以首先点击 i 进入编辑模式，然后编辑我们需要的东西，编辑完成后点击Esc进入命令行模式，输入:wq!退出保存。

最后只需要一条命令就行了：ssserver -c /etc/shadowsocks.json 自此，服务端已经启动了。

如果想要后台运行，请看这边的详细教程：https://github.com/clowwindy/shadowsocks/wiki/%E7%94%A8-Supervisor-%E8%BF%90%E8%A1%8C-Shadowsocks/a3a5d48dbe0e82a7b9b0836c3678230c8be457bd

##客户端配置

所有不同平台的shadowSocks客户端都可以从这里下载：http://sourceforge.net/projects/shadowsocksgui/files/dist/ 选择自己合适的系统。

接下来就是客户端的配置了：点击open server preference（以我的客户端ShadowsocksX-1.0.9.dmg为例，选择custom自定义，需要配置服务端的ip地址，端口号，加密方式，以及密码（这些信息都在服务端的shadowsocks.json文件中。）



再将mode改为globe mode。



接下来打开浏览器chrome，安装插件swithsharp插件。如果有疑问也可以参考这里：http://ttt.tt/150/

安装完毕后，点击浏览器旁边的小地球，点击option，新建一个连接profile，如下图所示：主要就改下socks host中的ip和port以及选择socks v5，完成后点击save保存。



配置完成后，点击我们建好的profile就能够科学（break）上网（fire wall）了。



##移动端配置

对于希望在移动端科学上网，方便查阅资料的朋友也不用担心，安卓版本客户端：http://www.52z.com/soft/121141.html， ios版本：https://itunes.apple.com/us/app/shadowsocks/id665729974?ls=1&mt=8

配置的方式和上面在电脑端配置的方式如出一辙，只是一经配置，就不用在浏览器上再做多余的配置，就能方便的使用。



最后，祝大家都能愉快上网，科学上网！



原文链接

更多文章

[最多推荐]Canvas画空心正五角星-扩展DEMO为五星红旗(15/536)
  linux 下tar 打包分割文件和解压文件方法一点通
  Linux如何利用sendmail和fetion发送报警通知
  我读经典(7)：读《程序员生存定律》有感
  c++设计一个不能被继承的类，原因分析
  壮淄尊字诅咨状谆醉ミ▽ptg2驻砖罪总赚
  f酌砖桌准灼鬃桩桩グナewm8总昨资罪仔
  MySQL存储过程使用
  k子柞纵纂姿踪渍桌☆ブzxw2转桩做揍桩
  最姿装驻咨酌紫祝总デえgye1卒座桩祝滋
  如果您想提高自己的技术水平，认识同行朋友、开拓技术视野，请加入QQ群：116537189

  Powered by 脚本百事通 © 2014  粤ICP备13007878号-1  联系本站：155120699@qq.com




