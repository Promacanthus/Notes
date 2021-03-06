在X中，X Server非常重要，负责整个界面的描绘，如果X Server 没有成功启动，那么即使启动了X Client也无法将图形显示出来。

- X Server的配置文件默认在`/etc/X11`目录下
- 显示模块在`/usr/lib/xorg/modules`
- 提供的屏幕字体`/usr/share/X11/fonts`
- 显卡的芯片组`/usr/lib/xorg/modules/drivers`

在Centos下，可以通过chkfontpath来获取当前系统的字体文件目录。

这些配置文件都通过一个容易的配置文件来规范，即X Server的配置文件`/etc/X11/xorg.conf`。

```bash
# 查看当前系统的X版本
sugoi@thinkpad:~$ sudo X -version

X.Org X Server 1.19.6
Release Date: 2017-12-20
X Protocol Version 11, Revision 0
Build Operating System: Linux 4.4.0-138-generic x86_64 Ubuntu
Current Operating System: Linux thinkpad 4.15.0-43-generic #46-Ubuntu SMP Thu Dec 6 14:45:28 UTC 2018 x86_64
Kernel command line: BOOT_IMAGE=/boot/vmlinuz-4.15.0-43-generic root=UUID=59c35426-bfba-4145-990b-9747663eac12 ro quiet splash vt.handoff=1
Build Date: 25 October 2018  04:11:27PM
xorg-server 2:1.19.6-1ubuntu4.2 (For technical support please see http://www.ubuntu.com/support)
Current version of pixman: 0.34.0
	Before reporting problems, check http://wiki.x.org
	to make sure that you have the latest version.
```
## /etc/X11/xorg.conf
该配置文件由多个段落组成，每个段落以Section开始，EndSection结束，中间包含相关配置，如下：
``` bash
Section "Section name"
# 相关配置参数
EndSection
```
常见的Section name主要有：
1. Module：被加载到X Server中的模块（某些功能的驱动程序）
2. InputDevice：包括输入的键盘的个格式，鼠标的格式，以及其他相关输入设备
3. Files：设置字体所在的目录位置等
4. Monitor：监视器格式，主要是设置水平、垂直的更新频率，与硬件有关
5. Device：这个重要，就是显卡芯片组的相关设置
6. Screen:在屏幕上显示的相关分辨率与色彩的深度的设置项目，与显示的行为有关
7. ServerLayout：上述的每个选项都可以重复设置，这里是设置此X Server要选用哪个选项值的设置
