# rEFInd实用攻略

> rEFInd是Github的一个著名的boot manager。在众多boot manager中，rEFInd不仅功能强大，能够对电脑的启动项进行方便的管理，还有着美观的GUI界面，并且支持自行DIY。对于一些想要美化自己电脑的用户，rEFInd绝对是一个不错的选择。

[TOC]

## Boot Manager（Boot Loader）



在正式谈rEFInd的安装之前，我们先简要的了解一下Boot Manager这个东西。对于大多数的（只使用Windows 10）用户来说，开机过程可能只是按下电源键——稍待片刻，就会出现熟悉的Win10登录界面。对少部分Linux用户，情况就变得有些不一样。如果是Ubuntu用户，很多时候在安装Ubuntu之后，你的开机界面会变成形如下图的形式：

![](gnu-grub2-boot-loader-menu.png)

还有少部分用户，可能会看到以下界面：

![](windows-boot-manager-5a2ae361e258f80036c1ecb9.png)





这些界面其实都是不同的Boot Manager。简单地说，现代个人计算机中，开机时首先需要进行硬件的自检、初始化，如果在这一步就出现问题，通常电脑会直接由特定的蜂鸣报警器或是电脑上的灯等进行听觉/视觉上的提示，甚至是直接“黑屏”，对用户的操作没有反馈。这些底层操作都是通过电脑自带的底层硬件以及特殊的一类程序完成的，并不依赖于操作系统（OS）。在上述硬件初始化完成之后，电脑才会准备启动OS。这时，就遇到了一个问题，如何使得底层程序与OS进行“交接”，使电脑过渡到OS的启动；对于有多个OS的用户，又如何进入用户需要的OS。Boot Manager就是为了解决这两大问题而产生的。

比较经典的Boot Manager就包括了上图中的GRUB2、Windows Boot Manager。而今天我们要介绍的rEFInd则是近年里GUI非常友好的一个Boot Manager。



----



## 安装篇



[rEFInd的Github传送门](https://github.com/agners/rEFInd)



### Linux



#### Debian系——以Ubuntu为代表



在terminal中直接使用apt安装即可：

`sudo apt install refind`

![](2019-07-04 09-46-19屏幕截图.png)

安装后可以直接使用。





#### Arch系——以Arch Linux为代表



同样可以在terminal中使用pacman安装：

`sudo pacman -S refind-efi`

![](深度截图_20190704095756.png)

同样是开袋即食。



> 在不同的包管理中，同一软件的包名可能是不同的。这时需要善用包管理的搜索功能。





#### 手动安装



对于一般的情况来说，利用包管理安装rEFInd是最为便利的选择。然而也有少数情况包管理无法正常安装；也有可能你是Mac OS用户。这时就只能手动进行安装。

一般的，Linux和Mac OS用户都可以通过从Github上Clone的项目文件夹中的`install.sh`进行安装。然而不排除有极少数安装失败。这时就要使用更原始的手动安装

手动安装适用于所有OS。之后要说的Windows下安装rEFInd事实上也是利用这个原理。

一般的思路是 挂载ESP分区->将rEFInd目录放到ESP分区的EFI目录下->修改Boot Loader顺序

一般来说，手动安装使用`./install.sh`都能解决，所以这里就不赘述从头开始的refind手动安装。



----



### Windows



虽然说Linux下，rEFInd的安装与配置都很方便，但大多数用户还是在用Windows，所以让我们康康Windows下rEFInd的安装。



> 在安装rEFInd之前，还有一件事要注意。一般来说，当前大多数使用Windows 10的用户的启动方式都是UEFI，但是不排除少数特殊的情况。所以如果不确定自己的启动方式，可以使用以下方式确定：
>
> 同时按下键盘的Win+R
>
> 输入**msinfo32**
>
> 在弹出的列表中找到BIOS模式一栏
>
> 如果是UEFI，就说明没有问题



下载对应的rEFInd文件：

> [下载传送门](https://sourceforge.net/projects/refind)
>
> *(相应的文件也和文章附在一起了，要不开个下载链接也好)*
>
> 点击**Download**下载（废话！）
>
> 下载后将文件解压到自己熟悉的目录。这里我推荐将文件目录名改为refind，以便下一步操作。
>
> 以笔者的文件目录为例，笔者最终改好目录名后，目录层理是这样的：
>
> I：
>
> ​	/temp
>
> ​		/refind-bin-0.11.4
>
> ​			/refind
>
> ​				/banners
>
> ​				/docs
>
> ​				/fonts
>
> ​				……			



下面我们使用Diskpart来挂载ESP分区：



> 先同时按下键盘上的**Win+X**，然后按**A**，弹出的窗口选择**是**。然后就可以进入管理员的Powershell。
>
> 输入`diskpart`
>
> 不出意外你可以看到类似的提示：
>
> ```powershell
> Microsoft DiskPart version 10.0.18362.1
> 
> Copyright (C) Microsoft Corporation.
> On computer: NYA-NYA
> 
> DISKPART>
> ```
>
> 继续键入`list volume`
>
> 可以看到如下信息：
>
> ```powershell
> DISKPART> list volume
> 
>   Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
>   ----------  ---  -----------  -----  ----------  -------  ---------  --------
>   Volume 0                      NTFS   Partition    499 MB  Healthy
>   Volume 1     C   OS           NTFS   Partition    100 GB  Healthy    Boot
>   Volume 2     D   Files        NTFS   Partition    180 GB  Healthy
>   Volume 3                      FAT32  Partition    100 MB  Healthy    System
>   Volume 4         PortableBas  NTFS   Partition   8189 MB  Healthy
>     C:\ProgramData\Microsoft\Windows\Containers\BaseImages\fa238d5f-e5a3-48bf-b4e0-94c994186aa6\BaseLayer\
> ```
>
> 哪一个是ESP分区呢？其中，Fs一栏为**FAT32**，Size大小为**100MB**的，通常就是ESP分区。（这里不排除有用户手动建过其他FAT分区的可能。但建立一个100MB大小的FAT分区概率实在太低，而且这样的用户也不会分不出ESP分区。）记住对应的Volume号码。在笔者的电脑上，对应号码就是Volume 3。
>
> 继续输入`select volume 3`。这里对应的号码就是刚刚需要你记住的Volume号码。
>
> 输入`assign letter=S`。不出意外现在你可以看到以下信息：
>
> ```powershell
> DISKPART> select volume 3
> 
> Volume 3 is the selected volume.
> 
> DISKPART> assign letter=s
> 
> DiskPart successfully assigned the drive letter or mount point.
> ```
>
> 让我们继续。现在可以到你的**此电脑**下查看。其中多了一个“本地磁盘（S:）”，这就是我们刚刚挂载的ESP分区。至此挂载完成。



接下来要做的就是将refind目录拷贝到ESP分区的EFI目录下：



> 我们将使用xcopy。xcopy接受至少两个参数，即from和to。总之，应确认下载refind所在的目录。以笔者的目录为例，下载好的refind所在的目录为
>
> `I:\temp\refind-bin-0.11.4\refind`
>
> 那么笔者输入的命令就是
>
> `xcopy I:\temp\refind-bin-0.11.4\refind S:\EFI\refind\`

至此refind就拷贝到ESP分区中了。如果不放心，可以使用ls查看：

`ls S:\EFI\refind`

用这种方式拷贝的用户应该可以看到

```
COPYING.txt
CREDITS.txt
LICENSE.txt
...
```



最后需要做的就是修改BCD文件，使系统优先引导refind。

这里为了便于操作，我们直接更改Windows Boot Manager的引导点为refind的引导点：

`bcdedit /set “{bootmgr}” path \EFI\refind\refind\refind_x64.efi`

当然，熟悉bcdedit或者手头有BOOTICE等BCD管理工具的也可以选择增加ENTRY而不改变原本的Windows Boot Manager。

至此，refind的安装就完成了。现在重启，就可以看到refind的启动了。



---



## 配置篇



嘛，安装好了refind，自然不能简简单单就完事了，看着初始的refind界面，虽然比之前的Windows Boot Loader好的太多，但还是有不少优化空间的~



为了访问refind目录，首先需要挂载ESP分区。对于Windows，上文在安装过程中已经给出了挂载方法。而Linux下想要挂载ESP分区的方法就很多了。最常见的如下（以Arch为例）：



![](深度截图_20190704201420.png)



如上图，笔者使用fdisk工具列出了分区及类型。找到ESP分区后，使用mount命令挂载ESP分区到/boot/efi下。你可能需要像笔者一样，使用sudo来运行上述命令。

> ESP分区使用FAT FileSystem（一般为FAT32），大小至少为100MB，这是UEFI标准中规定的内容。不管你在哪个OS下用何种工具查看，一般满足这两个条件的，准是ESP分区没错。



挂载之后，进入到refind目录。如果你是像笔者一样，在Linux下进行了rEFInd的安装，那么大概率你看到的目录结构和笔者是一样的。然而，如果你是Windows安装用户，那么你的目录一般只会有一个**refind.conf-sample**。使用`mv refind.conf-sample refind.conf`来重命名这个文件为refind.conf。然后就可以愉快的编辑了。



> 关于文本编辑器的选择，Windows下可以使用
>
> `notepad refind.conf`	（使用记事本打开）
>
> `code refind.conf`	  （使用Visual Studio Code打开）
>
> Linux选择则更多，nano、vim…挑一个就好。



下面是配置的重头戏。笔者将直接拷贝一份refind.conf并说明其中一些主要选项的含义。

（由于笔者没有Mac OS，所以没有翻译其中有关Mac OS的选项。如果是黑苹果玩家，对于这种程度的配置文件想必是手到擒来了。）



```

# refind.conf
# Configuration file for the rEFInd boot menu
#

# 在选择界面停留的时间。将time out设置为0将禁止自动启动。
# Timeout in seconds for the main menu screen. Setting the timeout to 0
# disables automatic booting (i.e., no timeout).
#
timeout 20

# 隐藏启动界面的一些选项。
# Hide user interface elements for personal preference or to increase
# security:
#  banner	   - 就是启动界面上方的那个LOGO
#  banner      - the rEFInd title banner (built-in or loaded via "banner")
#  label 	   - 每个启动项的文字说明
#  label       - boot option text label in the menu
#  singleuser  - remove the submenu options to boot Mac OS X in single-user
#                or verbose modes; affects ONLY MacOS X
#  safemode    - remove the submenu option to boot Mac OS X in "safe mode"
#  hwtest      - the submenu option to run Apple's hardware test
#  arrows	   - 在启动界面选择系统时显示箭头	
#  arrows      - scroll arrows on the OS selection tag line
#  hints	   - 启动界面下方命令的简要描述
#  hints       - brief command summary in the menu
#  editor	   - 允许用户使用+、F2或者Insert键进行BOOT选项的编辑（建议关闭）
#  editor      - the options editor (+, F2, or Insert on boot options menu)
#  all         - all of the above
# Default is none of these (all elements active)
#
#hideui singleuser
#hideui all
#默认情况下，以上选项是全部打开的。为了关闭上述的选项，只需在下一行
#写上语句[hideui xxx]即可。

# Set the name of a subdirectory in which icons are stored. Icons must
# have the same names they have in the standard directory. The directory
# name is specified relative to the main rEFInd binary's directory. If
# an icon can't be found in the specified directory, an attempt is made
# to load it from the default directory; thus, you can replace just some
# icons in your own directory and rely on the default for others.
# Default is "icons".
# 更改读取启动项图标的目录。不建议修改。
#icons_dir myicons

# Use a custom title banner instead of the rEFInd icon and name. The file
# path is relative to the directory where refind.efi is located. The color
# in the top left corner of the image is used as the background color
# for the menu screens. Currently uncompressed BMP images with color
# depths of 24, 8, 4 or 1 bits are supported, as well as PNG images.
# 更改启动界面的背景图片。支持PNG和BMP图片（推荐PNG）。
# 为了使用自定义的背景图，只需要将需要的背景图放到与refind.conf相同的目录下，
# 然后在下面一行加上语句[banner xxxx.png]，xxxx为图片名。
# 记住，图片大小不要过大。笔者目前使用的PNG图是8MB的，应该已经是极限。
#banner hostname.bmp
#banner mybanner.png

# Custom images for the selection background. There is a big one (144 x 144)
# for the OS icons, and a small one (64 x 64) for the function icons in the
# second row. If only a small image is given, that one is also used for
# the big icons by stretching it in the middle. If only a big one is given,
# the built-in default will be used for the small icons.
#
# Like the banner option above, these options take a filename of an
# uncompressed BMP image file with a color depth of 24, 8, 4, or 1 bits,
# or a PNG image. The PNG format is required if you need transparency
# support (to let you "see through" to a full-screen banner).
#
# 修改图标。不建议自行修改。相关的Github项目中会有配套的图标和配置文件。
#selection_big   selection-big.bmp
#selection_small selection-small.bmp

# Set the font to be used for all textual displays in graphics mode.
# The font must be a PNG file with alpha channel transparency. It must
# contain ASCII characters 32-126 (space through tilde), inclusive, plus
# a glyph to be displayed in place of characters outside of this range,
# for a total of 96 glyphs. Only monospaced fonts are supported. Fonts
# may be of any size, although large fonts can produce display
# irregularities.
# The default is rEFInd's built-in font, Luxi Mono Regular 12 point.
# 修改字体。不建议修改。
#font myfont.png

# Use text mode only. When enabled, this option forces rEFInd into text mode.
# Passing this option a "0" value causes graphics mode to be used. Pasing
# it no value or any non-0 value causes text mode to be used.
# Default is to use graphics mode.
# 以文本模式启动rEFInd。（应该不会有人装了rEFInd然后选择文本模式启动吧？？？）
#textonly

# Set the EFI text mode to be used for textual displays. This option
# takes a single digit that refers to a mode number. Mode 0 is normally
# 80x25, 1 is sometimes 80x50, and higher numbers are system-specific
# modes. Mode 1024 is a special code that tells rEFInd to not set the
# text mode; it uses whatever was in use when the program was launched.
# If you specify an invalid mode, rEFInd pauses during boot to inform
# you of valid modes.
# CAUTION: On VirtualBox, and perhaps on some real computers, specifying
# a text mode and uncommenting the "textonly" option while NOT specifying
# a resolution can result in an unusable display in the booted OS.
# Default is 1024 (no change)
# 设置文本模式的显示大小。
#textmode 2

# Set the screen's video resolution. Pass this option either:
#  * two values, corresponding to the X and Y resolutions
#  * one value, corresponding to a GOP (UEFI) video mode
# Note that not all resolutions are supported. On UEFI systems, passing
# an incorrect value results in a message being shown on the screen to
# that effect, along with a list of supported modes. On EFI 1.x systems
# (e.g., Macintoshes), setting an incorrect mode silently fails. On both
# types of systems, setting an incorrect resolution results in the default
# resolution being used. A resolution of 1024x768 usually works, but higher
# values often don't.
# Default is "0 0" (use the system default resolution, usually 800x600).
# 设置屏幕的分辨率显示。不建议修改。有兴趣可以自行阅读原版注释修改。
#resolution 1024 768
#resolution 3

# Launch specified OSes in graphics mode. By default, rEFInd switches
# to text mode and displays basic pre-launch information when launching
# all OSes except OS X. Using graphics mode can produce a more seamless
# transition, but displays no information, which can make matters
# difficult if you must debug a problem. Also, on at least one known
# computer, using graphics mode prevents a crash when using the Linux
# kernel's EFI stub loader. You can specify an empty list to boot all
# OSes in text mode.
# Valid options:
#   osx     - Mac OS X
#   linux   - A Linux kernel with EFI stub loader
#   elilo   - The ELILO boot loader
#   grub    - The GRUB (Legacy or 2) boot loader
#   windows - Microsoft Windows
# Default value: osx
# 不显示文本而以GUI形式启动系统。通常这个选项只需要按照默认选项启动即可
# 也就是去掉下面一行的注释符“#”，Linux启动时就不会弹出那些文本
#use_graphics_for osx,linux

# 在启动界面显示特定的工具，大多数情况下用户只需要shutdown和reboot。
# Which non-bootloader tools to show on the tools line, and in what
# order to display them:
#  shell           - the EFI shell (requires external program; see rEFInd
#                    documentation for details)
# 				   - EFI SHELL。一般不会用到。
#  gptsync         - the (dangerous) gptsync.efi utility (requires external
#                    program; see rEFInd documentation for details)
#  apple_recovery  - boots the Apple Recovery HD partition, if present
#  mok_tool        - makes available the Machine Owner Key (MOK) maintenance
#                    tool, MokManager.efi, used on Secure Boot systems
#				   - 与UEFI安全策略有关，有兴趣自行查阅，不建议使用
#  about           - an "about this program" option
#  exit            - a tag to exit from rEFInd
#  shutdown        - shuts down the computer (a bug causes this to reboot
#                    EFI systems)
# 				   - 关机
#  reboot          - a tag to reboot the computer
#				   - 重启
# Default is shell,apple_recovery,mok_tool,about,shutdown,reboot
#
#showtools shell, mok_tool, about, reboot, exit

# Directories in which to search for EFI drivers. These drivers can
# provide filesystem support, give access to hard disks on plug-in
# controllers, etc. In most cases none are needed, but if you add
# EFI drivers and you want rEFInd to automatically load them, you
# should specify one or more paths here. rEFInd always scans the
# "drivers" and "drivers_{arch}" subdirectories of its own installation
# directory (where "{arch}" is your architecture code); this option
# specifies ADDITIONAL directories to scan.
# Default is to scan no additional directories for EFI drivers
# 扫描额外的EFI应用。不建议自行添加。
#scan_driver_dirs EFI/tools/drivers,drivers

# Which types of boot loaders to search, and in what order to display them:
#  internal      - internal EFI disk-based boot loaders
#  external      - external EFI disk-based boot loaders
#  optical       - EFI optical discs (CD, DVD, etc.)
#  hdbios        - BIOS disk-based boot loaders
#  biosexternal  - BIOS external boot loaders (USB, eSATA, etc.)
#  cd            - BIOS optical-disc boot loaders
#  manual        - use stanzas later in this configuration file
# Note that the legacy BIOS options require firmware support, which is
# not present on all computers.
# On UEFI PCs, default is internal,external,optical,manual
# On Macs, default is internal,hdbios,external,biosexternal,optical,cd,manual
# 有关Boot Loader扫描的设置。一般建议保持默认设置，不需要更改。
#scanfor internal,external,optical,manual

# Delay for the specified number of seconds before scanning disks.
# This can help some users who find that some of their disks
# (usually external or optical discs) aren't detected initially,
# but are detected after pressing Esc.
# The default is 0.
# 扫描延迟。建议默认的0。
#scan_delay 5

# When scanning volumes for EFI boot loaders, rEFInd always looks for
# Mac OS X's and Microsoft Windows' boot loaders in their normal locations,
# and scans the root directory and every subdirectory of the /EFI directory
# for additional boot loaders, but it doesn't recurse into these directories.
# The also_scan_dirs token adds more directories to the scan list.
# Directories are specified relative to the volume's root directory. This
# option applies to ALL the volumes that rEFInd scans UNLESS you include
# a volume name and colon before the directory name, as in "myvol:/somedir"
# to scan the somedir directory only on the filesystem named myvol. If a
# specified directory doesn't exist, it's ignored (no error condition
# results). The default is to scan the "boot" directory in addition to
# various hard-coded directories.
# 扫描其它的BOOT LOADER目录。不建议自行更改。
#also_scan_dirs boot,ESP2:EFI/linux/kernels

# Partitions to omit from scans. You must specify a volume by its
# label, which you can obtain in an EFI shell by typing "vol", from
# Linux by typing "blkid /dev/{devicename}", or by examining the
# disk's label in various OSes' file browsers.
# The default is an empty list (all volumes are scanned).
# 跳过扫描的BOOT扇区。不建议自行更改。
#dont_scan_volumes "Recovery HD"

# Directories that should NOT be scanned for boot loaders. By default,
# rEFInd doesn't scan its own directory or the EFI/tools directory.
# You can "blacklist" additional directories with this option, which
# takes a list of directory names as options. You might do this to
# keep EFI/boot/bootx64.efi out of the menu if that's a duplicate of
# another boot loader or to exclude a directory that holds drivers
# or non-bootloader utilities provided by a hardware manufacturer. If
# a directory is listed both here and in also_scan_dirs, dont_scan_dirs
# takes precedence. Note that this blacklist applies to ALL the
# filesystems that rEFInd scans, not just the ESP, unless you precede
# the directory name by a filesystem name, as in "myvol:EFI/somedir"
# to exclude EFI/somedir from the scan on the myvol volume but not on
# other volumes.
# 跳过扫描的目录。正常情况下不建议自行更改。但对一些OEM的电脑确实有用。
#dont_scan_dirs ESP:/EFI/boot,EFI/Dell

# Files that should NOT be included as EFI boot loaders (on the
# first line of the display). If you're using a boot loader that
# relies on support programs or drivers that are installed alongside
# the main binary or if you want to "blacklist" certain loaders by
# name rather than location, use this option. Note that this will
# NOT prevent certain binaries from showing up in the second-row
# set of tools. Most notably, MokManager.efi is in this blacklist,
# but will show up as a tool if present in certain directories. You
# can control the tools row with the showtools token.
# The default is shim.efi,MokManager.efi,TextMode.efi,ebounce.efi,GraphicsConsole.efi
# 跳过扫描的EFI引导。同上。
#dont_scan_files shim.efi,MokManager.efi

# Scan for Linux kernels that lack a ".efi" filename extension. This is
# useful for better integration with Linux distributions that provide
# kernels with EFI stub loaders but that don't give those kernels filenames
# that end in ".efi", particularly if the kernels are stored on a
# filesystem that the EFI can read. When uncommented, this option causes
# all files in scanned directories with names that begin with "vmlinuz"
# or "bzImage" to be included as loaders, even if they lack ".efi"
# extensions. The drawback to this option is that it can pick up kernels
# that lack EFI stub loader support and other files. Passing this option
# a "0" value causes kernels without ".efi" extensions to NOT be scanned;
# passing it alone or with any other value causes all kernels to be scanned.
# Default is to NOT scan for kernels without ".efi" extensions.
# 是否扫描所有的Linux Kernel，默认值不同版本不同，自行查看英文注释
scan_all_linux_kernels

# Set the maximum number of tags that can be displayed on the screen at
# any time. If more loaders are discovered than this value, rEFInd shows
# a subset in a scrolling list. If this value is set too high for the
# screen to handle, it's reduced to the value that the screen can manage.
# If this value is set to 0 (the default), it's adjusted to the number
# that the screen can handle.
# 设置一次屏幕上展示的OS最大数目。
#max_tags 0

# Set the default menu selection.  The available arguments match the
# keyboard accelerators available within rEFInd.  You may select the
# default loader using:
#  - A digit between 1 and 9, in which case the Nth loader in the menu
#    will be the default. 
#  - Any substring that corresponds to a portion of the loader's title
#    (usually the OS's name or boot loader's path).
# 设置默认的启动OS
#default_selection 1

# Include a secondary configuration file within this one. This secondary
# file is loaded as if its options appeared at the point of the "include"
# token itself, so if you want to override a setting in the main file,
# the secondary file must be referenced AFTER the setting you want to
# override. Note that the secondary file may NOT load a tertiary file.
#
#include manual.conf

# Sample manual configuration stanzas. Each begins with the "menuentry"
# keyword followed by a name that's to appear in the menu (use quotes
# if you want the name to contain a space) and an open curly brace
# ("{"). Each entry ends with a close curly brace ("}"). Common
# keywords within each stanza include:
#
#  volume    - identifies the filesystem from which subsequent files
#              are loaded. You can specify the volume by label or by
#              a number followed by a colon (as in "0:" for the first
#              filesystem or "1:" for the second).
#  loader    - identifies the boot loader file
#  initrd    - Specifies an initial RAM disk file
#  icon      - specifies a custom boot loader icon
#  ostype    - OS type code to determine boot options available by
#              pressing Insert. Valid values are "MacOS", "Linux",
#              "Windows", and "XOM". Case-sensitive.
#  graphics  - set to "on" to enable graphics-mode boot (useful
#              mainly for MacOS) or "off" for text-mode boot.
#              Default is auto-detected from loader filename.
#  options   - sets options to be passed to the boot loader; use
#              quotes if more than one option should be passed or
#              if any options use characters that might be changed
#              by rEFInd parsing procedures (=, /, #, or tab).
#  disabled  - use alone or set to "yes" to disable this entry.
#
# Note that you can use either DOS/Windows/EFI-style backslashes (\)
# or Unix-style forward slashes (/) as directory separators. Either
# way, all file references are on the ESP from which rEFInd was
# launched.
# Use of quotes around parameters causes them to be interpreted as
# one keyword, and for parsing of special characters (spaces, =, /,
# and #) to be disabled. This is useful mainly with the "options"
# keyword. Use of quotes around parameters that specify filenames is
# permissible, but you must then use backslashes instead of slashes,
# except when you must pass a forward slash to the loader, as when
# passing a root= option to a Linux kernel.

# Below are several sample boot stanzas. All are disabled by default.
# Find one similar to what you need, copy it, remove the "disabled" line,
# and adjust the entries to suit your needs.

# A sample entry for a Linux 3.3 kernel with its new EFI boot stub
# support on a filesystem called "KERNELS". This entry includes
# Linux-specific boot options and specification of an initial RAM disk.
# Note uses of Linux-style forward slashes, even in the initrd
# specification. Also note that a leading slash is optional in file
# specifications.
menuentry Linux {
	icon EFI/refind/icons/os_linux.icns
	volume KERNELS
	loader bzImage-3.3.0-rc7
	initrd initrd-3.3.0.img
	options "ro root=UUID=5f96cafa-e0a7-4057-b18f-fa709db5b837"
	disabled
}

# A sample entry for loading Ubuntu using its standard name for
# its GRUB 2 boot loader. Note uses of Linux-style forward slashes
menuentry Ubuntu {
	loader /EFI/ubuntu/grubx64.efi
	icon /EFI/refined/icons/os_linux.icns
	disabled
}

# A minimal ELILO entry, which probably offers nothing that
# auto-detection can't accomplish.
menuentry "ELILO" {
	loader \EFI\elilo\elilo.efi
	disabled
}

# Like the ELILO entry, this one offers nothing that auto-detection
# can't do; but you might use it if you want to disable auto-detection
# but still boot Windows....
menuentry "Windows 7" {
	loader \EFI\Microsoft\Boot\bootmgfw.efi
	disabled
}

# EFI shells are programs just like boot loaders, and can be
# launched in the same way. You can pass a shell the name of a
# script that it's to run on the "options" line. The script
# could initialize hardware and then launch an OS, or it could
# do something entirely different.
menuentry "Windows via shell script" {
	icon \EFI\refind\icons\os_win.icns
	loader \EFI\tools\shell.efi
	options "fs0:\EFI\tools\launch_windows.nsh"
	disabled
}

# Mac OS is normally detected and run automatically; however,
# if you want to do something unusual, a manual boot stanza may
# be the way to do it. This one does nothing very unusual, but
# it may serve as a starting point. Note that you'll almost
# certainly need to change the "volume" line for this example
# to work.
menuentry "My Mac OS X" {
	icon \EFI\refind\icons\os_mac.icns
	volume "OS X boot"
	loader \System\Library\CoreServices\boot.efi
	disabled
}
```



> Q：怎么我的conf文件和上面的画风不大一样？
>
> A：上面的文件是笔者截取的老版conf文件，新版里又增加了一些新功能。然而对于一般用户来说，上面提供的提示内容完全足够。如果有兴趣可以自己阅读注释。毕竟笔者也是读着注释一点一点写的。
>
> Q：我的电脑上同时有Windows和Linux，为什么使用rEFInd后出现了两个相同的Linux图标？
>
> A：如果你仔细观察，就会发现两个Linux图标对应的引导点并不同。这是由于rEFInd同时扫描了efi文件和Linux Kernel的后果。如果出现这种问题，笔者建议首先将上方配置中的
>
> `scan_all_linux_kernels`关闭（如果默认是打开的话）。
>
> 如果默认就是打开，那么笔者建议在配置中的“忽略目录”对应的配置中将相关的启动目录屏蔽。如GRUB、Ubuntu等等。
>
> 当然，如果你还无法解决，欢迎来电脑诊所线上咨询群提问。