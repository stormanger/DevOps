##关于grub恢复的23事

事情起因比较曲折，总之造成的现象就是系统引导不起来，开始是可以进入引导不过无法正确进入系统，找不到/dev/disk，找不到引导的UUID什么的，很可能是/etc/fstab的问题，果断接入系统救援盘... ...

结果看到

![grub1](https://github.com/stormanger/DevOps/blob/master/image/grub1.jpg)

那么自然是修复分区表啦，gdisk走起，进入恢复模式r，再使用b，使用backup重建分区表，p打印一看，一切正常，w保存退出，挂载各磁盘，表示读写文件均正常。重启后依然无法引导 囧

继续救援盘发现系统依然只能发现/dev/sda，继续各种折腾2小时无果，协商重装吧，系统引导之后发现所有的分区信息都能识别，除了1M的/dev/sda1，没错，是它是它就是它——grub发生了问题。

![grub1](https://github.com/stormanger/DevOps/blob/master/image/grub2.jpg)

`# mkfs.ext4 /dev/sda1`之后，就需要重建grub啦

重新写一个rhel7的引导U盘，Troubleshooting——Rescue a CentOS Linux system——进入命令：

`# chroot /mnt/sysimage `

`# grub2-install /dev/sda`之后exit，重新引导系统，结束，收工





