# KVM虚拟机动态分配vCPU和内存

在虚拟机offline模式下先设置好最大vCPU数量和最大内存大小之后，KVM虚拟机可以动态分配vCPU和内存，动态增加虚拟机的vCPU数量和内存大小不能超过虚拟机配置文件的限制数量，但是配置VM的vCPU和内存最大限制是需要在offline的状态下操作的。

在创建虚拟机的时候，可以预先设计VM最大的cpu和内存作为限制，当前分配的vCPU和内存可以无需使用最大资源，这样可以避免停机。

##配置vCPU和内存的最大值

使用 `virsh shutdown` 虚拟机后，通过 `virsh dumpxml` 查看VM的当前配置

```xml
  <uuid>834040f8-32dc-0db5-8ad3-b0d9590881f7</uuid>
  <memory unit='KiB'>2048000</memory>
  <currentMemory unit='KiB'>2048000</currentMemory>
  <vcpu placement='static'>2</vcpu>
```

通过 `virsh setmaxmem ` 设置虚拟机的最大内存数量

```
virsh setmaxmem 8G
```

或者直接用使用 `virsh edit` 编辑虚拟机的配置文件，不过得计算内存的大小，XML文件里面的单位是kB（编辑XML的配置是永久生效的）文件的修改完后可以看到里面的配置信息：

```xml
  <memory unit='KiB'>8388608</memory>
  <currentMemory unit='KiB'>2048000</currentMemory>
  <vcpu placement='static'>2</vcpu>
```

通过 `virsh edit` 来配置VM的最大CPU限制，这里是把VM的最大CPU配置成4，当前为2

```
<vcpu placement='static' current='2'>4</vcpu>
```

## 动态分配计算资源

当前虚拟机的配置依然是2C2G，现在测试给VM动态添加vCPU

```
virsh setvcpus ubuntu 4
```

在虚拟机里面可以通过 `cat /proc/cpuinfo` 查看到VM已经拥有了4C

```bash
~# cat /proc/cpuinfo |grep processor
processor       : 0
processor       : 1
processor       : 2
processor       : 3
```

动态分配内存，将内存配置成4G

```
virsh setmem ubuntu 4G
```

通过 `free -m` 验证VM的内存

```bash
~# free -m
              total        used        free      shared  buff/cache   available
Mem:           3887         838        2135           9         913        2786
Swap:          2045           0        2045
```

注意：以上命令都是立即生效的，但是无法永久保存配置，如果需要永久保存配置，都必须直接编辑XML文件，并且要VM下次重启才能生效。

## docs

https://huataihuang.gitbooks.io/cloud-atlas/