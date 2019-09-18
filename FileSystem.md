# FileSystem && Partition

[TOC]

## File System

> In computing, a **file system** or **filesystem** controls how data is stored and retrieved. 
>
> Without a file system, information placed in a storage medium would be one large body of data with no way to tell where one piece of information stops and the next begins. By separating the data into pieces and giving each piece a name, the information is easily isolated and identified. Each group of data is called a "file". The structure and logic rules used to manage the groups of information and their names is called a "file system".

在PC中，常用的文件系统包括Windows下的NTFS、FAT、exFAT等。到了Linux下，个人用户最常用的要数ext文件系统。



### FAT

FAT得名于它的文件分配表（File Allocation Tables）方式，即在存储空间最开始有个大表，记录所有的可用块的使用链，并标记空闲块和坏块。它最早是由Bill Gates同Marc Mcdonald一起发明。FAT经历了FAT8、FAT12、FAT16、FAT32时代，目前FAT32还在使用中。FAT后面的数字就代表着分配表中，表示下个链的位数（即簇号）。

FAT在它问世的那个年代有着相当先进的一面。但同时，FAT的弊端是根本性的。首先，FAT需要对FAT表进行大量读写操作，导致物理部分极易损坏；同时，读写操作一旦经历意外（如断电）就会造成FAT表出错，从而在逻辑层面上也使得FAT的健壮性很差。更致命的是，FAT表的大小是有限的，从而FAT支持的空间大小也有限。后来的历史证明，FAT支持的空间大小根本跟不上存储设备的发展。使用过FAT32的人都会有这种困扰，就是FAT32格式的文件系统不支持4G以上的文件，而这个问题是FAT这一文件系统无法避免的。

FAT有过辉煌的一段时期。然而现在，FAT已经落后于现代文件系统。但它仍有用武之地。这就是UEFI标准中，规定的EFI分区(ESP)采用FAT格式。并且这一标准要求在移动介质上，同样以FAT作为ESP的File System。这也是为什么**在制作系统启动盘时，要将U盘首先格式化为FAT32格式。**

> 现在网上有一些教程，声称“格式化U盘为NTFS格式同样可以使用UEFI启动”。事实上，真正制作了就会发现，本质上这种制作，是划分U盘中的一部分区域作为ESP（FAT）用以引导，将剩下的部分格式化为exFAT或NTFS来存储需要的系统镜像。在启动时，通过ESP中的特殊引导程序来跳转到系统镜像并进行安装。



### exFAT

exFAT是Extended File Allocation Table的缩写。从它的名字就可以看出，exFAT和FAT有着千丝万缕的联系。exFAT是微软在2006年推出的，“专门为闪存优化的”，FAT的一种替代品。

相比于FAT，exFAT最大支持1EB的文件大小，目前来讲对于大容量文件的存储完全没有问题。相较于对于支持硬盘分区大小的提升、单目录下支持文件数目的提升，令用户最欣喜的，还是终于不用受FAT32的单文件4GB大小的限制了。在exFAT发布之后，许多闪存设备默认都会采用exFAT格式。并且Mac和Linux目前也都支持exFAT。



### NTFS

NTFS（New Technology File System）是Windows NT Family的默认文件系统。作为File System的集大成之作，NTFS集成了典型File System的绝大多数优点。它具有日志，没有文件大小的限制，有良好的安全性能，支持超长文件名，支持文件压缩，并且在服务器文件管理上独具特色，总之NTFS有着优越的性能，让它成为综合性上当前最优秀的文件系统之一。

NTFS由微软主持开发，目前在Linux上也有第三方驱动提供对NTFS的读写支持。然而Mac OS原生只提供对NTFS的读权限而没有写权限。想要更改只能借助第三方软件。



### 总结

FAT在这三种File System中是诞生最早的。一路走来，到今天FAT依然有其用武之地，靠的就是其强大的兼容性。在几乎所有的平台上，都能够使用FAT。但它的弊端也很明显，不支持4G以上的文件注定了它无法走的更远。exFAT作为FAT的一种替代品，在闪存设备上体现出了相当的优越性，并且Windows、Linux、Mac OS也都支持。然而它最大的软肋在于不支持日志。并且传统硬盘有的不能格式化为exFAT。这都使得很多时候exFAT不能成为理想的选择。而NTFS在各方面都很优秀。然而NTFS有一个特性是会详细记录磁盘的读写操作。对于目前应用越来越广的Flash存储而言，过多的读写操作给介质带来很大的负担。因而微软才会将exFAT称作“Optimized for Flash”。



---



## Disk Partition



### 简介



让我们先从HDD讲起。

在早期，磁盘的地址管理策略被称为Cylinder-Head-Sector，即柱面、磁头和扇区。通过这三个要素，来描述一个存储地址。

```Linux
> fdisk -l
Disk /dev/cciss/c0d0: 146.7 GB, 146778685440 bytes
255 heads, 63 sectors/track, 17844 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

```

可以看到，硬盘的容量就是17844（Cylinder）* 255（head）* 63（Sector）=146.7G。

这里，**Sector是硬盘的最小存储单位**。大小为512Bytes。Sector是物理层面上的一个概念，是硬盘生产出来时就定下的，无法更改。最早时，硬盘的Sector一般是512Bytes。随着硬盘存储空间的扩大，目前也有Sector大小为4096Bytes的硬盘，也就是常说的4K扇区。

有了Sector的概念，再来考虑块（Block）。对于OS，或者File System来说（以下统称OS），单独的Sector实在太小，一个个读取过于缓慢。这时自然就产生了同时操作多个Sector的想法。而**Block，实际上就是由整数个（通常是2的n次方）的块组成**。对于OS来说，**对于文件的操作是以块（Block）为单位进行的。**换句话说，在OS中，**Block是文件存取的最小单位。**

```Linux
> df -T
/dev/cciss/c0d0p5    ext3   112738028  81733116  25185772  77% /
> tune2fs -l /dev/cciss/c0d0p5 | grep "Block size"
Block size:		4096
```

可以看到，在这一个Linux OS下，使用的File System是ext3，其Block大小是4096，也就是说，一个Block由8个Sector组成。

同时，很显然的，Block是建立在OS/File System基础上的，也就是说它是一个逻辑概念。

> 通常，在Windows下Block称为簇，Linux等Unix-Like的OS中将Block称为块。二者其实指的都是Block这个概念。

由于OS存储文件的最小单位是Block。这意味着多小的文件，只要存储，必定占据一个Block。同时，不同OS、不同File System，对于Block的大小支持都不同。



回到地址管理策略上来。CHS策略的弊端是显然的。它只能够应用于HDD（或者说，当时意义上的硬盘，因为那时还没有SSD）而无法应用于磁带等其他存储介质。并且由于机械结构上的限制，CHS表示的硬盘空间大小也是有限的。后来就有了LBA（Logical Block Addressing，逻辑扇区地址）。LBA为每个扇区分配逻辑地址。同时，为了与CHS兼容，LBA会通过算法同样得到一个C-H-S式的地址。只是，当然，这个CHS Address也是逻辑上的而非物理上的。

具体的转换规则可以参考[Wiki-LBA](<https://en.wikipedia.org/wiki/Logical_block_addressing>)。



### 硬盘分区

硬盘分区，顾名思义，就是将硬盘划分成几个部分供不同用途，这同样是一个逻辑上的概念。在不同的分区表策略中（马上就会提到），有不同的划分分区方式。到了9102年，虽然还是会有人理所应当的认为不需要分区，但对于现代PC用户来说，想要使用一块硬盘，不分区是不可能的。最基本的事实就是——如果想让一个OS使用一块（部分）硬盘，就必须为这块硬盘（的某部分）分区并指定文件系统。这样之前提到的块才有实现基础。



### MBR

MBR是Master Boot Record的缩写，意为主分区引导记录。在MBR出现前，就已经有了LBA。因而MBR的实现也是在LBA基础上的。

在MBR分区下，每个分区的第一个Sector称为引导扇区，而整个硬盘的第一个扇区称为主引导扇区。主引导扇区也是主分区的第一个扇区。

主引导分区的512Bytes分配如下：

| 项目                 | 大小      |
| -------------------- | --------- |
| Boot Loader（Code）  | 446 Bytes |
| Disk Partition Table | 64 Bytes  |
| Magic Number         | 2 Bytes   |

其中，开始的Boot Loader完全是以代码形式存在的，负责引导进入OS。

第二部分的DPT对于理解MBR的分区比较关键。这一部分储存着分区信息。事实上，DPT存储的64Bytes中划分为4 * 16。一个典型的16Bytes信息为：

`80 01 01 00, 0B FE BF FC, 3F 00 00 00, 7E 86 BB 00`

这段16Bytes的信息中记录着分区开始、结束的CHS信息，使用的File System以及总扇区数等信息。像这样，在DPT中可以储存4个分区。这四个分区就是人们熟知的MBR下的四个主分区。

最后的相当于是一个Validation Check。只有BIOS读到了这个Magic Number才会继续进行引导，否则会报错，相当于没有找到MBR的主引导分区。

而扩展分区和逻辑分区是如何实现的呢？由于主引导记录的DPT中至多只能储存4个分区信息，这时，为了有更多的分区，就将4个分区划分成3P+E的形式，最后的这个E分区就是扩展分区。在这个分区里只记录一份磁盘分区信息（也就是说，扩展分区无法直接用于存储）。在这份磁盘分区信息中记录的分区，就是逻辑分区。

表面上，MBR只能有四个分区的问题得到了解决。事实上，还有一个更大的问题。之前提过，一个分区的信息只能由16Bytes来表示。在分区信息中表示分区大小，是通过描述总扇区数实现的。而描述总扇区数的信息，事实上在上面的实例中，就是最后的4个Byte：`7E 86 BB 00`。在这种情况下，分区大小就等于总扇区数 * 扇区大小（512Bytes)。然而，这种方式表达的最大扇区数就是`FF FF FF FF`，折合过来就是2TB。换言之，MBR无法支持2TB以上的分区。这在现代是非常致命的。

### GPT

GPT是在UEFI的规范下研制的。它的全称是Globally Unique Identifier Partition Table，也叫GUID分区表。

GPT的大概结构如下：

| 项目                   | 扇区         |
| ---------------------- | ------------ |
| PMBR                   | LBA0         |
| GPT HDR                | LBA1         |
| Partition Table        | LBA2~LBA34   |
| Partition              | …            |
| Partition Table Backup | LBA-2~LBA-34 |
| GPT HDR Backup         | LBA-1        |



第一项是PMBR，这里P为Protective之意。为了兼容不支持GPT的分区工具，PMBR可以像在Legacy BIOS下一样被操作并启动。而支持GPT的启动则会在读取到PMBR后跳过这部分，去到GPT分区表头读取信息。

第二部分就是GPT HDR，即GPT表头。在这部分包含了Partition Table中的各分区及其大小、硬盘的容量大小以及一系列信息。

> GPT HDR具体包括了：
>
> - Signature的ASCII码表示
> - UEFI及GPT的版本号
> - GPT HDR的大小
> - GPT的CRC校验。UEFI启动可以根据这个校验值判断GPT HDR是否出错。如果出错则从硬盘尾部的Backup中恢复。如果备份的CRC校验也表明有误，那么硬盘就不可使用（逻辑层面）。
> - GPT HDR的LBA
> - GPT HDR Backup的LBA
> - 可用分区（Partition）的首个LBA（就是分区表的最后一个LBA+1）
> - 可用分区的最后LBA
> - GPT Partition起始LBA
> - GPT Partition总项数
> - GPT Partition中单个分区表大小
> - GPT Partition的CRC校验
> - 硬盘的GUID

第三部分是分区表。每个分区表都包含分区类型的GUID、分区自身的GUID、分区起止LBA、分区属性（只读等）、分区名。

> 微软对分区属性做了划分。包括：
>
> - 系统分区
> - EFI隐藏分区
> - Legacy BIOS的可引导分区
> - 只读分区
> - （另一个分区的）副本
> - 隐藏分区
> - 不分配驱动器号



Partition就是用户实际使用的分区了。硬盘尾端的两个Backup前文已经有所介绍。



以上。