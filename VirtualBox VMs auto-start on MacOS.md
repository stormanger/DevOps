Docs 文档

​	https://www.virtualbox.org/manual/ch09.html#autostart-osx

​	https://developer.apple.com/library/content/navigation/#section=Platforms&topic=macOS

Version information 版本信息

macOS Sierra : 10.12.6

VirtualBox : 5.1.26 r117224 (Qt5.6.2)

Config MacOS LaunchDaemons 配置启动服务

$ sudo touch /Library/LaunchDaemons/org.virtualbox.vboxautostart.plist
Then edit /Library/LaunchDaemons/org.virtualbox.vboxautostart.plist, Set KeepAlive to false so that VMs only start once after system boot, Set LaunchOnlyOnce to false to make sure this service keep in the system.

接下来编辑 /Library/LaunchDaemons/org.virtualbox.vboxautostart.plist，设置 KeepAlive 的值为 false 以保证虚拟机只会在系统启动后启动一次, 设置 LaunchOnlyOnce 的值为 false 确保服务会持续存在。

The file should look like this 以下是文件配置 :

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Disabled</key>
  <false/>
  <key>KeepAlive</key>
  <false/>
  <key>Label</key>
  <string>org.virtualbox.vboxautostart</string>
  <key>ProgramArguments</key>
  <array>
    <string>/Applications/VirtualBox.app/Contents/MacOS/VBoxAutostartDarwin.sh</string>
    <string>/etc/vbox/autostart.cfg</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
  <key>LaunchOnlyOnce</key>
  <false/>
</dict>
</plist>
Config VirtualBox and VMs 配置VirtualBox和虚拟机

$ sudo mkdir /etc/vbox
$ sudo touch/etc/vbox/autostart.cfg
/etc/vbox/autostart.cfg with the following content 配置如下：

default_policy = deny
UserName = {
allow = true
}
Make sure to change UserName to the username on your system that the VMs are under.

将UserName更换成虚拟机的所有者的用户名。

Configure the VMs that should auto-start 配置需要自启动的虚拟机 :

$ VBoxManage list vms		#List VMs 列出虚拟机
$ VBoxManage modifyvm VmName --autostart-enabled on
$ VBoxManage modifyvm VmName --autostop-type acpishutdown
VmName must change to your VM name.

VmName 是虚拟机的名字。

Load the configuration 加载服务配置

$ sudo launchctl load /Library/LaunchDaemons/org.virtualbox.vboxautostart.plist
Use follow command check auto-start service 用这条命令检查自启动服务配置:

$ sudo launchctl list |grep -i org.virtualbox.vboxautostart
Other 其他

If you want stop auto-start, just unload the service use this command 用下列命令关闭自启动服务 :

sudo launchctl unload /Library/LaunchDaemons/org.virtualbox.vboxautostart.plist
Or change VM to manual by 或者关闭虚拟机的自启动 :

$ VBoxManage modifyvm VmName --autostart-enabled off
VmName must change to your VM name.

VmName 是虚拟机的名字。
