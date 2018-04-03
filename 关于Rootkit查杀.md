# 关于Rootkit查杀

## 描述

接到客户的电话，在不断的向外网发包，客户将外网线拔掉作为紧急处理，第二天往客户现场处理。

## 分析及解决

ifconfig发现网卡TX的流量巨大。

查看/var/log/message日志，发现从3月31日00：40之后一直有root登陆的session，没有其他的日志记录仅仅能确定受感染的时间，预测是病毒清理了其他日志信息。

使用top查看进程信息，能观察到奇怪的进程信息，并且会不停的变化，进程号也会不停的变化，但是有2~3个进程是不行变化并且长久在系统进程之中的

![rootkit01](https://github.com/stormanger/DevOps/blob/master/image/rootkit01.jpg)

可以看到PID 26242这个进程名非常奇怪cjwihkvxoe，并且从81685~81697的进程会不断的删除并且建立新的进程，应该是病毒的子进程。

`#ll /proc/26242 `可以看到进程所在的程序位置：

![rootkit02](https://github.com/stormanger/DevOps/blob/master/image/rootkit02.jpg)

尝试使用命令`# killall -9 26242`结束进程，虽然26242被杀掉，但是又有新的奇怪进程出现，看来杀是杀不掉的，要是那么容易杀掉就不是木马了。多次杀掉奇怪的进程发现一直会有新的进程出现，那么病毒应该使用了别的机制进行复制，这个时候就想到查看/etc/crontab，果然有奇怪的脚本在里面。

![rootkit03](https://github.com/stormanger/DevOps/blob/master/image/rootkit03.jpg)

查看脚本的内容：

![rootkit04](https://github.com/stormanger/DevOps/blob/master/image/rootkit04.jpg)

以及这个古怪的驱动：

![rootkit05](https://github.com/stormanger/DevOps/blob/master/image/rootkit05.jpg)

那么首先要将/etc/crontab的内容清楚，并且将脚本和古怪的驱动都删除掉。使用`ll -t /usr/bin`查看文件，可以轻易发现近期写进去的文件：

![rootkit06](https://github.com/stormanger/DevOps/blob/master/image/rootkit06.jpg)

并且在/etc/init.d/目录也发现了同样奇怪的文件，那么在chkconfig里面也就有自启动的系统进程了：

![rootkit07](https://github.com/stormanger/DevOps/blob/master/image/rootkit07.jpg)

`chkconfig xxx off`之后，将可执行的文件`chmod 600`以阻止病毒再次运行。

![rootkit08](https://github.com/stormanger/DevOps/blob/master/image/rootkit08.jpg)

cat 这些文件查看，基本可以确定是恶意脚本：

![rootkit11](https://github.com/stormanger/DevOps/blob/master/image/rootkit11.jpg)

并且在/etc/init.d/rc#.d/目录下也发现了链接文件：

![rootkit09](https://github.com/stormanger/DevOps/blob/master/image/rootkit09.jpg)

同样的，也做修改权限处理；文件基本在找到了，下面则要进程杀毒，直接杀死进程会再生成新的进程，那么如果停止进程呢？`# kill -STOP 26242 `，停止进程之后top发现没有新的进程出现，这个时候对病毒文件进程删除，基本上分布上/usr/bin，/etc/init.d，/etc/init.d/rc#.d内。

最后在`killall -9 26242 `杀掉进程，ps -ef及top均没有发现新的奇怪进程出现，并且重启后也没有出现。

![rootkit10](https://github.com/stormanger/DevOps/blob/master/image/rootkit10.jpg)

## 清除木马流程

1 top或者pe -ef或ps -aux查看进程，找到病毒进程并查找/usr/bin，/etc/init.d，/etc/init.d/rc#.d内的文件

2 kill -STOP xxxx停止病毒的进程

3 检查/etc/crontab查看有没有奇怪的内容，删除内容并且删除掉病毒脚本及删除掉对应的病毒驱动

4 删除对用的病毒文件

5 再次查看是否还有病毒存在，并且重启测试

6 处理完后建议使用`chkrootkit`或者`RKHunter`检查一下

## 建议

建议root使用强密码，并且根据需求对系统进行安装加固（如更改ssh端口，建立普通用户给与sudo权限并且禁用root使用ssh登陆，配合防火墙使用fail2ban等），有条件尽量先更新补丁，

