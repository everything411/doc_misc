# UEFI

[TOC]

## EFI 与 UEFI

之前提到，最早的BIOS是由汇编语言写成的。这种BIOS生动的诠释了“*山”的含义，其中充满了封闭、神秘的祖传代码。而当在为安腾处理器开发一套引导规范时，Intel决定改变这种局面。他们决定使用高级C语言接口为OS加载器提供平台固件的接口。而这种**接口的规范**就称作**EFI（Extensible Firmware Interface）**。之后，EFI投入应用并取得良好反响，同时老式BIOS日渐式微，这种情况下，2005年，BIOS供应商、OS供应商、芯片生产公司等共同建立了**统一的EFI论坛**，这就是**UEFI（Unified Extensible Firmware Interface）**。

在设计固件与操作系统间接口的同时，论坛又达成另一个一致意见：为固件内部构造符合UEFI规范的标准化接口，这就是**PI规范（Platform Initialization，平台初始化）**的由来。这项规范旨在使芯片驱动程序可以直接在各固件厂商开发的平台上直接运行，无需适配修改，从而简化开发部署过程。

> 总的来说，UEFI是一个接口规范，而具体涉及平台固件如何实现的是PI解决的问题。如果想看更多更详细的有关信息，可以参考[这篇文章](<https://zhuanlan.zhihu.com/p/25676417>)。
>
> 这篇文章不会讲解PI规范。
>
> UEFI虽然是一个接口规范，但由于它替代了传统的Legacy BIOS，所以很多时候，当我们称呼UEFI时，我们其实是在说“UEFI启动”。UEFI启动是在UEFI规范下执行的一种启动。而人们挂在嘴边的“UEFI BIOS”则可以理解为一种口胡。因为BIOS和UEFI严格来说完全是两种事物（启动程序和规范），UEFI BIOS，其实也就是UEFI规范下的启动。



## UEFI启动

UEFI启动遵循PI规范。与Legacy BIOS类似，分析UEFI启动也是从加电（Power-On）开始。

UEFI的启动分为七个阶段：

- SEC（Security Phase，安全验证阶段）
- PEI （Pre-EFI Initialization，EFI前初始化阶段）
- DXE （Driver Execution Environment，驱动执行环境）
- BDS （Boot Device Select，启动设备选择阶段）
- TSL （Transient System Load，操作系统加载前阶段）
- RT （Run-Time，运行阶段）
- AL （After-Life，灾难恢复阶段）



其中，PI规范包括了前三个阶段。PI代码和UEFI核心仅来自主板制造商，他们必须为符合PI规范并采用了UEFI服务的固件负责。更确切的说，主板制造商要保证这些产品交付后的真实性和安全性。因而对固件的更改必须处于主板制造商的控制监督之下。因而，在UEFI启动过程中，安全性也成为一个需要关注的点。当然，这里不会细讲。



这篇文章不会详细的讲解每个阶段具体发生了什么。只会挑选一些个人比较感兴趣的或是比较重要的来写（笑）。



## UEFI阶段的简介



首先要说明的是，**UEFI启动过程中各阶段的分界并不那么清楚。**重要的是理解某些关键节点发生在何时。



在SEC阶段，有一个比较有意思的事实。SEC阶段只有CPU及其内部资源被初始化。这种情况下，要想进行代码的运行和相关数据的存取，就只能从CPU内部开辟一块临时的RAM区域。而这个临时区域，在现代CPU中最主要的就是使用Cache。这种转换CPU的Cache为临时RAM的技术叫做CAR（Cache As RAM）。同时，SEC阶段会传递一些系统参数给下一阶段PEI。值得注意的是，在SEC阶段，就已经从16位实模式进入32位或64位平坦模式（包含模式）。

> 前面说过，UEFI的主要实现是相对高级的C语言。C程序的运行是需要堆栈的。由于一开始“两手空空”，直到CAR完成后，C程序才有了堆栈所需的空间，并能够执行并完成接下来的工作。



然后到了PEI阶段。在PEI阶段后期完成了内存的初始化。然后PEI会准备一个HOB（Hand-Off Block）列表来传递给下一个DXE阶段。事实上，SEC和PEI很重要的一个使命就是为DXE做好准备。

> PEI开始时C程序可用的堆栈空间也是少量的。PEI重要的任务之一就是找到永久内存（相对于CAR的临时内存而言）来支持C程序的运行。



到了DXE阶段，由于内存已经完成初始化，在这一阶段可以执行大部分的系统初始化（Initialization）工作。DXE阶段根据HOB列表初始化系统服务，然后遍历系统中的Driver并且执行满足要求的Driver初始化。这一步的具体实现逻辑与PEI阶段是十分相似的。

如果要确切一点说，在DXE阶段完成了CPU、芯片组的初始化，并且初始化必要的System Services（如Boot Services）。

> DXE阶段由于内存空间充足，核心芯片得以真正开始初始化。



然后是BDS阶段。顾名思义，这一步就可以选择Boot Device，或者说，启动项。另外，Drivers的加载（Load）是在这一阶段完成的。加载时，只会加载Boot OS所需的Drivers。如果Boot失败，那么会返回DXE阶段来初始化、加载更多的Driver来满足Boot要求。

UEFI与Legacy BIOS的一个差别就是UEFI可以提供丰富的图形化界面。这就得益于EFI Drivers的加载。由于显卡驱动得以加载，用户就可以看到图形化的界面。

> BDS初始化的硬件主要是输入、输出和存储设备。



在用户选择了启动项或者进入默认启动项后，系统进入TSL阶段。

TSL阶段主要是OS Loader（操作系统加载器）执行的过程。Linux下的GRUB就是最著名的OS Loader之一。确切的说，在UEFI规范下，这一步启动的未必是OS Loader，还可以是UEFI Applications。当然，在TSL中OS Loader实际扮演了UEFI Applications的角色。

OS Loader通过Boot Services等服务，逐渐将计算机资源的控制权移交到自己的手中，之后通过调用一个*ExitBootServices()*继续将控制权转交给OS Kernel。

TSL的强大之处在于其已经具备了操作系统的雏形。OS Loader一般会提供UEFI Shell，一个用于人机交互的临时界面。一般情况下系统不会进入UEFI Shell，只有用户干预或者OS Loader出现严重错误才会进入UEFI Shell。这里同样可以用GRUB加深对于这部分内容的理解。



在ExitBootServices()被调用后，OS Loader向OS Kernel转交控制权，也就进入了常规意义上的最后一个阶段——Run-Time阶段。在此阶段，如果检测到硬件或是软件的灾难性错误，系统固件需要给出错误处理和恢复机制，这时就进入了AL阶段。然而UEFI规范没有对AL阶段的行为进行定义，换言之，AL阶段是由OEM厂商自行决定的。



## 有关UEFI的一些名词



### FAT & Boot Loader

FAT得名于其文件分配表（File Allocation Tables）方式。FAT文件系统下，在开始处有一个目录，记录了块的使用情况。关于FAT的实现，可以参考[这篇文章](<https://zhuanlan.zhihu.com/p/25992179>)。FAT最为致命的问题之一，就是FAT表的大小一开始就是被限定的。换言之，由表制定的每块的大小、块可以由多少个Bit表示也是受限的，从而，支持的空间大小也是受限的。



在之前提到的UEFI启动中，我们提到了Boot Loader（OS Loader）。UEFI规定，将Boot Loader储存在硬盘中一个特殊的分区，这就是ESP分区（EFI System Partition）。事实上，ESP分区也会储存Kernel Images、Device Driver Files等在UEFI启动中，进入OS之前需要用到的一些数据。

UEFI规定，ESP分区采用FAT32格式，同时支持FAT12/FAT16作为移动介质中的文件系统。为了与一般FAT进行区分，ESP分区与FAT分区有着不同的OSType。在GPT分区表中，ESP分区有一个特殊的GUID：`C12A7328-F81F-11D2-BA4B-00A0C93EC93B`。

总的来说，启动操作系统的过程，就是运行ESP分区内的Boot Loader的过程。Boot Loader在UEFI启动中一般是一个EFI Application。一般来说，Boot Loader以`.efi`后缀储存在ESP分区中，不同的操作系统有不同的对应Boot Loader。Boot Loader的存放遵循[UEFI Spec规定](<https://uefi.org/>)。





### Secure Boot

> [Secure Boot参考](<https://blogs.msdn.microsoft.com/b8/2011/09/22/protecting-the-pre-os-environment-with-uefi/>)
>
> [安全机制参考](<https://zhuanlan.zhihu.com/p/25279889>)

Secure Boot是UEFI下的一个重要标准。为什么要有Secure Boot呢？

在Legacy BIOS时期，完成POST自检后BIOS会直接尝试读取OS Loader并由此启动OS。这造成了安全上的一个巨大隐患，即一些恶意用户可以通过修改硬盘中的OS Loader为伪装过的Malware，使得用户正常进入OS，但实际上Malware已经开始作祟。臭名昭著的有Rootkit，它可以在内核层面进行伪装，后患无穷。Legacy BIOS就是这样，BIOS只会读取并尝试运行它读取到的OS Loader，不加区分。

Secure Boot就是在这种背景下设计出来的。

> Secure boot defines how platform firmware manages security certificates, validation of firmware, and a definition of the interface (protocol) between firmware and the operating system.

简单来说，UEFI要求将安全证书储存在固件中。在启动Boot Loader时会进行校验，只有受信任的Boot Loader才能通过并正常启动。

Secure Boot的安全机制实际上涉及到了密码学知识。这里我们仅陈述一些最基本的事实。

> 在共享加密法出现之前，人们普遍认为，为了进行保密通信，必须在通信双方约定一个共享的秘密用来加密和解密，这就是**对称密钥**。
>
> 到了上世纪70年代，公钥加密法出现了。这是一种**非对称密钥**机制。密钥分为**公钥**和**私钥**，其中私钥仅为其所有者掌握，而公钥可以分发给任何受信赖的用户。公钥和私钥的关系，可以用保险箱来比喻：私钥的所有者相当于掌握了保险柜的制造方法，而公钥的所有者相当于掌握着打开保险柜的钥匙。公钥所有者可以解密信息（打开保险柜），但没有必要知道信息如何加密（保险柜如何制造）。
>
> 不久，RSA密码系统出现。这时，私钥持有者不仅可以利用私钥解密公钥所有者发来的信息，还可以对一些数据进行数字签名（配发钥匙）。专家们已经从数学上证明过，数据只能被私钥所有者签名，并且不会被私钥所有者以外的人更改。
>
> 接下来要解决的还有一个问题：如何传递公钥？或者，换个角度说，如何保证依赖方（需要验证数字签名的一方）能够确定公钥是合法的（保险柜的钥匙不是伪造的）呢？这时就引入了**公钥基础设施（Public-Key Infrastructures，PKI）**这一概念。PKI作为具有更高权威的第三方，可以在公钥上附加数字签名，来证明某个公钥来自相应的公钥-私钥对的创建者。数字签名的授权者称为**证书授权机构（Certificate Authority，CA）**。
>
> 那么现在，问题实际就转化为了如何信任CA的问题。只要CA保证授权正确，那么授权过的公钥就是合法的。而CA可以负责不同权限的授权。上级CA可以为下级CA签名授权，从而下级CA可以进行专门权限的授权。一般将顶级CA称作**根CA**或**信任锚**。顶级CA需要对自身进行自签名（我证明我是我自己）。逻辑上来讲这是不需要的。但为了依赖方对证书的一致使用，也就是管理上的方便，还是引入了自签名证书。
>
> CA的认证可以被撤销。如果某个私钥的所有者因某些安全问题而不再被信任（例如私钥丢失、有被入侵风险），那么其对应的签名也会被撤销。显然，这其中需要一个“时间戳”来判断签名失效与否，这也是由第三方机构，时间戳机构（Timetamping Authority）完成的。



说了这么多，UEFI上的Secure Boot的机制是如何实现的呢？

在Secure Boot的架构中，有一个构成信任基石的密钥——**平台密钥（Platform Key，PK）**。PK作为**信任根（Root of Trust）**，可以修改平台上的其他**信任锚（Trust Anchor）**列表。PK一般由设备制造商持有，但某些企业用户也可以要求设备制造商提供PK以实现对企业内部设备的安全管理。

Secure Boot维护以下几个数据库：

1. PK
2. Key Exchange Key，KEK
3. the Allowed Database/Signature Database
4. the Forbidden Signatures Database

一般PK只能有一个。PK验证KEK数据库中所有密钥的合法性。PK可以修改第二个数据库，KEK数据库。信任锚被储存在KEK数据库。KEK储存了第三、四个数据库的信息并负责它们的更新、验证，而第三个数据库储存了实际负责UEFI固件、EFI文件检测的CA。第四个数据库是用来记录被撤销的签名方，或者通俗的说，就是一个黑名单。出现在第四个数据库中的EFI文件不能执行。这一过程实现比较复杂，并且牵扯到大量标准，MS的[这篇文章](<https://docs.microsoft.com/zh-cn/windows-hardware/design/device-experiences/oem-secure-boot>)有一定的参考价值。

OS Loader的检测就是依赖于上述的第三个数据库。能通过Secure Boot的OS Loader才能正常开始执行，换言之，如果没有通过，就会卡在BDS阶段，无法进入TSL阶段。



Secure Boot最早是在Windows 8时期引入的。引入后，Secure Boot收到褒贬不一的评价。Secure Boot固然能极大程度的提高PC的安全性，但作为牵头者之一，巨硬对于Secure Boot的标准非常严格。Secure Boot要求开发者除了符合最新的UEFI标准以外，还有额外的一系列规范。这对于许多开源的发行版来说非常不友好。时至今日，仍然有某些Linux Distribution不能通过Secure Boot的检验。对于这个问题只能见仁见智。





### CSM

CSM是Compatibility Support Module的缩写，中文翻译过来叫做兼容性支持模块。

CSM是UEFI规范下的产物。出于对比较老的PC硬件、固件兼容性的考虑，大部分的UEFI Implements提供一个可选项目，允许PC以Legacy BIOS方式下的方式进行MBR启动，这就是CSM。

事实上，CSM不只是允许MBR启动那么简单。它会模拟许多Legacy BIOS时一些底层服务。例如，它也提供Legacy启动下的System Management Mode(SMM)的模拟。

Intel在2017年声称，将在2020年停止对CSM的支持。















