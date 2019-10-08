# 浅谈开源

[TOC]

近一段时间，开源算是一个比较火的概念。方舟编译器的开源、木兰宽松许可证等等，都让**开源**这个名词走进了大众的视野。这篇文章会简单的谈谈开源软件以及和它相关的那些概念、故事。



## Free Open-Source Software

让我们先从一个词说起，**Free Open-Source Software**。这个词常常被人用起，但有经常被用错。首先正如Wikipedia给出的说明，这个词是一个复合型术语——它是**Free Software**  和 **Open-Source Software**拼接到一起而成的。

> "**Free and open-source software**" (**FOSS**) is an umbrella term for software that is simultaneously considered both **Free software** and **open-source software**.

其中Open-Source是我们今天这篇文章的主角。最浅显的说，开源指的就是将程序的源代码公之于众，大家愿意把这份代码拿去使用、学习甚至改造都可以。

可Free Software又是个啥？可能是这个概念传入国内时翻译的问题，很多人都将这个词中的Free理解为免费之意——开源软件又不需要付费，那Free不就是免费的意思嘛？事实上，这个Free并不是Free Beer的Free，而是Free Speech的那个Free（a matter of liberty not price）。没错，Free Software通常指软件的证书（License）中包含了使用者“自由”（liberties）的那些软件。应该说，Free Software和Open-Source Software之间有着千丝万缕的联系，但它们又不尽相同。按照Free Software Foundation的说法，Free背后代表的是一种对使用者自由的尊重，而Open-Source则只是开发者希望不断完善自身代码的一种手段（大雾）。

> "The two terms describe almost the same category of software, but they stand for views based on fundamentally different values. Open-source is a development methodology; free software is a social movement. For the free-software movement, free software is an ethical imperative, essential respect for the users' freedom. By contrast, the philosophy of open-source considers issues in terms of how to make software “better”—in a practical sense only."

说一千道一万，虽然Free和Open-Source彼此之间有着超多的相通之处，但还是要有所区分的。Free Software的概念最早由美国人Richard Stallman在1986年提出，并且在前一年，他就成立了非营利性组织FSF（Free Software Foundation）。最早的时候对于Free Software的定义只有两大自由，但是经过不断修改，现在的Free Software已经有了四大自由：

- The freedom to run the program as you wish, for any purpose (freedom 0).
- The freedom to study how the program works, and change it so it does your computing as you wish (freedom 1). Access to the source code is a precondition for this.
- The freedom to redistribute copies so you can help your neighbor (freedom 2).
- The freedom to distribute copies of your modified versions to others (freedom 3). By doing this you can give the whole community a chance to benefit from your changes. Access to the source code is a precondition for this.

> 此外，the Four Freedoms中Freedom 0反而是最后一个被加入的，但是人们认为它很重要，所以就成为了Freedom 0。XD

事实上，Richard的这一系列操作都是在当时的一大运动**Free Software Movement**背景下展开的。这是早期计算机发展历史上的一个有着深远意义的社会性运动。

这一事件的大概背景是，在上世纪六十年代后期之前，计算机的使用还是处于一个小众的圈子内，大多数都是专门的研究机构或者高校。同时当时还没有一个普适性的操作系统出现，因而在那个时期大家都会将自己程序的源码进行分享。这样既方便学术上的交流，还可以改进自己的程序。更重要的是，本身小众的圈子加上局限的使用环境，即使将程序源码分享出去也不会造成大规模的随意使用。然而形势在60年代末发生了变化。随着操作系统和编程语言的发展，程序的移植性空前提高；同时由于要考虑移植、适配的问题，软件的编写成本要比之前的机器码时代更高。此外，这一时期硬件生产商的优势地位也在丧失。在之前，硬件生产商在提供机器时会提供相应的配套软件，但随着发展这种配套的软件无法满足顾客的需求，而更多的软件提供商则制作出了丰富的软件资源。1969年美国政府将绑定软件行为定性为不正当竞争，这个原本由硬件生产商占有的市场就被放给了软件制作商，这刺激了软件商业化的发展，一部分软件变得需要商业授权。之后70年代软件的版权化和80年代Unix系统停止发行免费版本加剧了软件商业化的趋势。软件商业化带来的必然结果就是——软件开发商们不再愿意公开自己程序的源码，而是只提供最终编译出的可执行文件。而Richard就是在这种大背景下号召展开了软件自由化的运动。

事实上，在FSF问世之前，Richard就主持发起了一个影响深远的项目——GNU（GNU’s Not Unix）。一般也认为，这个项目的发起就代表着**Free Software Movement**的正式开端。这个项目致力于**ensure that the entire software of a computer grants its users all freedom rights**。为了搭建一个这样的系统，Richard需要从头开始制作Compiler、Kernel甚至于Editor，于是就有了GCC、Linux以及Emacs。当然这些都是后话了。

说回Open-Source Software。Open-Source这个定义是由另一个组织发起的，这个组织就是OSI(Open Source Initiative)。这个组织基本就和Richard没有什么实质关系（当然精神是一脉相承的），成立于1998年。事实上，FSF和OSI（或者说这两大组织各自的定义）的初衷、目的是不同的。

> "Dump the moralizing and confrontational attitude that had been associated with 'free software'" and instead promote open source ideas on "pragmatic, business-case grounds."

OSI创始人之一Michael的原话中鲜明的表现出了对于前辈Richard理念的否定，无怪后来FSF和OSI对掐，正如上文所提，二者理念确实有诸多不同。

> 有趣的是，在Wiki上，FSF被称作**non-profit corporation**，而OSI被称作**public-benefit corporation**。二者的区别在于**Ownership Factor**。区别在于，nonprofit corporation是致力于服务大众的，不属于任何人，也不会盈利；而所谓的public-benefit corporation事实上是有Leadership，并且可以盈利的，只是他们的目标更为“宏伟”一些，会将一部分收入反馈社会。

扯了这么多，那么Open-Source Software的定义究竟是怎样的呢？

> Open source doesn't just mean access to the source code. The distribution terms of open-source software must comply with the following criteria:
>
> 1. **Free Redistribution** The license shall not restrict any party from selling or giving away the software as a component of an aggregate software distribution containing programs from several different sources. The license shall not require a royalty or other fee for such sale.
> 2. **Source Code** The program must include source code, and must allow distribution in source code as well as compiled form. Where some form of a product is not distributed with source code, there must be a well-publicized means of obtaining the source code for no more than a reasonable reproduction cost preferably, downloading via the Internet without charge. The source code must be the preferred form in which a programmer would modify the program. Deliberately obfuscated source code is not allowed. Intermediate forms such as the output of a preprocessor or translator are not allowed.
> 3. **Derived Works** The license must allow modifications and derived works, and ==must allow them to be distributed under the same terms as the license of the original software.==
> 4. **Integrity of The Author's Source Code** The license may restrict source-code from being distributed in modified form only if the license allows the distribution of "patch files" with the source code for the purpose of modifying the program at build time. The license must explicitly permit distribution of software built from modified source code. The license may require derived works to carry a different name or version number from the original software.
> 5. **No Discrimination Against Persons or Groups** The license must not discriminate against any person or group of persons.
> 6. **No Discrimination Against Fields of Endeavor** The license must not restrict anyone from making use of the program in a specific field of endeavor. For example, it may not restrict the program from being used in a business, or from being used for genetic research.
> 7. **Distribution of License** The rights attached to the program must apply to all to whom the program is redistributed without the need for execution of an additional license by those parties.
> 8. **License Must Not Be Specific to a Product** The rights attached to the program must not depend on the program's being part of a particular software distribution. If the program is extracted from that distribution and used or distributed within the terms of the program's license, all parties to whom the program is redistributed should have the same rights as those that are granted in conjunction with the original software distribution.
> 9. **License Must Not Restrict Other Software** The license must not place restrictions on other software that is distributed along with the licensed software. For example, the license must not insist that all other programs distributed on the same medium must be open-source software.
> 10. **License Must Be Technology-Neutral** No provision of the license may be predicated on any individual technology or style of interface.

其实乍看之下Open-Source在大多数方面和Free非常相似，只是在很多细节处加了完善。但仔细推敲之下，不难发现Open-Source最大的特点之一就是其传染性。简单说，Open-Source基本要求基于原本Open-Source软件开发的Redistribution版本也要保证Open-Source。这一点我们在接下来提到的几大著名开源协议中会体现的更加明显和详细。

总的来说，Free Software这一概念出现的比Open-Source Software要早。二者都是Free Software Movement这一影响深远的运动下的产物——或者说，都是这一思想的继承者，只是他们对这一思想有着各自的理解。（突然想到Metal Gear，怀念啊）这也使得两大组织之间并不算和谐，虽然他们坚持的事物在笔者看来并没有什么区别。从这两大概念，乃至他们背后的组织的种种对比，我们都可以看出，Open-Source的出发点要比Free Software更加现实，可能正是因此，目前Open-Source的影响要比Free Software广得多。

大人，时代变了。



##开源协议

> An **open-source license** is a type of license for computer software and other products that allows the source code, blueprint or design to be used, modified and/or shared under defined terms and conditions.

开源协议其实就是一种约定，如果一个软件使用了开源协议，这就意味着软件开发者和使用者的行为都要遵守协议的规定。类似的Software License其实很多，例如我们上文介绍的Free Software其实也有自己的License。著名的开源协议有：**Apache License	BSD License	GNU General Public License	MIT License	Mozilla Public License	GUN Lesser General Public License**等等。对于这六个最常用的开源协议，有一个很方便进行选择的图表。

![1-1Z32QI643931](../../Downloads/1-1Z32QI643931.gif)但一个恐怖的事实是现在已经有几十种被OSI认可的开源协议，所以在之前选择适合的开源协议对程序员来说这真的是一个极大的挑战。不过现在已经有了专门的分类网站，例如[Choose A License](https://choosealicense.com/)，可以让你根据自己的需要很快找到适合的协议。

## 参考

[Wikipedia-Free Open-Source Software and its RELATED PAGES :-)](https://en.wikipedia.org/wiki/Free_and_open-source_software)