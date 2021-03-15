# **iWork8 U80GT 平板安装Ubuntu Desktop 20.04 64位教程(理论适用所有Linux64位发行版)**
>## **为什么会有这个教程**  
>iWork8的配置在当下使用Windows系统已经卡的不行了，所以本人决定为自己的iWork8安装linux操作系统。  
期间本人测试了debian、archlinux、kali、ubuntu，只有kali、ubuntu两个发行版包含了iWork8的驱动(电池、Wifi、Bluetooth、重力感应)。  
目前[Ubuntu官网](https://ubuntu.com/#download)仅提供了x64的安装镜像。  
而64位的iso镜像写入到U盘插入iWork8并不能正常引导。这个时候就需要修改一下U盘中的EFI引导了，将32位引导程序替换掉原来的64位引导程序。  
当安装完毕后还需要对硬盘中的系统efi进行修改。  
--------------------------
>## **Step1:准备工作**  
> **需要下载的文件:**  
[Ubuntu-amd64的镜像](https://ubuntu.com/#download)、[U盘引导EFI](https://github.com/GuokeNo1/iWork8-Install-Ubuntu-amd64/tree/master/EFI/U)、[硬盘引导EFI](https://github.com/GuokeNo1/iWork8-Install-Ubuntu-amd64/tree/master/EFI/Local)、[GRUB文件夹](https://github.com/GuokeNo1/iWork8-Install-Ubuntu-amd64/tree/master/GRUB)  
**替换引导程序方式:**  
默认的Ubuntu64位安装镜像写入U盘后会弹出一个磁盘其结构如下  
```
EFI
└── BOOT
    ├── BOOTx64.EFI
    ├── grubx64.efi
    └── mmx64.efi
```
> 直接将BOOT文件夹删除将上文所下载的U盘引导EFI所有文件夹复制进来，然后目录结构应该是这样的  
```
EFI
├── boot
│   ├── bootia32.efi
│   └── grubia32.efi
└── debian
    └── grub.cfg
```
> 这样一个32位EFI引导的64位Ubuntu安装盘就准备好了，然后就是安装步骤了。
--------------------------
>## **Step2:安装系统**
> 和正常的安装方式一样将系统安装到硬盘当中，这边推荐将所有目录安装到一个分区即/目录分区(方便后面引导配置),安装到最后会提示一个Grub2-i386xxx错误不要惊慌。点确定然后会提示安装成功重启。这个时候重启是没有办法进入系统的。  
需要重新进入到U盘的系统中选择试用Ubuntu。进入了以后需要挂载一下硬盘中第一块分区详情步骤如下
```
sudo su                         #获取root权限
mkdir temp                      #创建临时目录
mount /dev/mmcblk1p1 temp       #挂载第一块分区到temp目录
#然后将下载的硬盘引导EFI文件夹内的东西全部复制到temp文件夹内的EFI目录中(EFI文件夹如果不存在直接新建一个文件夹重命名EFI即可)
```
> 这个时候可以重启系统然后会进入到一个grub的命令行界面命令行界面亿次输入以下代码(如果安装时所有目录不在一个分区则以下代码需要更改)
```
set root=(hd0,gpt2)
linux /boot/vmlinuz-x.x-x-xx-generic root=/dev/mmclkb1p2
#其中x.x.为版本号，输入/boot/vmlinuz-再按tab键即可补全，如果安装时是全部安装到一个分区则root目录一般是/dev/mmclkb1p2具体看个人的pad配置
initrd /boot/initrd.img-x.x-x-xx-generic
#其中x.x.为版本号，输入/boot/initrd.img-再按tab键即可补全
boot
```
> 输入完上方代码一般开始跑代码等待系统开机即可  
--------------------------
> ## **Step4:安装后修复自动引导(重力感应旋转屏幕横向如果颠倒修复)** 
> 修复引导在终端中输入以下代码即可  
如果输入以下代码重启无效重复上面启动步骤将提供的grub文件夹复制到/boot/grub文件夹内再执行一遍以下代码即可

```
sudo update-grub2
```
> 修复重力感应：  
创建文件/etc/udev/hwdb.d/61-sensor-local.hwdb  
将以下内容写入文件  
```
sensor:modalias:acpi:SMO8500*:dmi:*:*
 ACCEL_MOUNT_MATRIX=-1, 0, 0; 0, 1, 0; 0, 0, -1
```
然后命令行执行以下代码重启即可。
```
sudo systemd-hwdb update
```
