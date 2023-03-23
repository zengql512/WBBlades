## [简体中文](#WBBlades_cn)|English

## <a name="WBBlades">Introduction</a>

WBBlades is a tool set based on `Mach-O` file parsing, including one-click detection for app  (supports `OC` and `Swift`), package size analysis (supports a single static library/dynamic library), point-to-point crash analysis ( analyse system crash log, based on symbol file and without symbol files),  Class automatic extraction and Hook capability based on Mach-O file. It mainly uses __Text assembly code analysis, architecture extraction, DYSM file stripping, symbol table stripping, crash file (ips) analysis technology.Support big method/small method parsing and iOS 15 above about dyld_chained_Fixups processing.

### Tool Overview

| Tool Name                   | Function Overview                                            | Related Technologies                                         |
| --------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| One-Click Detection for App | Swift & OC useless class detection, useless resource detection, and package downsizing optimization suggestions. | Disassembly, __Text instruction extraction, Mach-O parsing, symbol table and Swift class structure in Mach-O |
| Package Size Analysis       | Detecting the actual size occupied by static & dynamic libraries in .ipa file. | Architecture stripping, static library structure analysis    |
| Crash Analysis              | Using .app or dYSM file to parse the system crash log.       | DYSM file parsing, lightweight symbol table extraction, ips file parsing, with/without symbol table crash parsing |
| Mach-O Class Hook           | Automatically extract classes from any Mach-O file, and then hook them. | dyld_ chained_ Fixups, binding, rebase, and big/small method parsing |

The new version  implements a comprehensive visual implementation of the toolset based on the original command-line-based operation of the above tools, and is designed for R&D efficiency improvement. In addition, in the analysis of difficult crashes, for some crashes that are not easy to reproduce and cannot be collected by general tools (the app process is directly killed by the operating system), a point-to-point crash analysis is provided.

## Installation

```
$ git clone https://github.com/wuba/WBBlades.git
$ cd WBBlades
$ pod install
```

### Usage of Visualization Tool
Target selects "WBBladesCrashApp".

Click the button on the left function area, select a tool such as Useless Classes Detection,Application Size Analysis,etc., and operate according to the prompts in the tool, and the result will be output to the text box;

> Detailed introduction to visualization tool: [Detailed introduction to visualization tool](https://juejin.cn/post/7176441132447399993)
### Usage for Mac Command Line
Target selects "WBBlades"，Compile and build to generate command line tools
Copy the generated product "blades" to /usr/local/bin，as follows：
sudo cp ${Your_BUILD_DIR}/blades /usr/local/bin


- Unused Code Detection ObjC & Swift

   `$ blades -unused xxx.app -from xxx.a xxx.a ....`
   
	> -from indicating that only the unused code in the following static libraries is analyzed. Without this parameter, the default is all classes in the APP.
   
- App Size Analysis (Directly measure the size of .a or .framework after linking)

  `$ blades -size xxx.a xxx.framework ....`
  
  > Supporting input a folder path, all static libraries under the folder will be analyzed.
  
- Log Recovery without dSYM File (In the case of missing dSYM file, try `ObjC` crash stack symbolization, `Swift` is not supported)

  `$ blades -symbol xxx.app -logPath xxx.ips`

## Tool Features

### Unused code (unused class) detection support range

| Description                     | Support | Code Example                                     |
| :----------------------- | :----------: | :------------------------------------------- |
| ObjC classes's static call       |      ✅       | `[MyClass new]`                              |
| ObjC classes's dynamic call           |      ✅       | `NSClassFromString(@"MyClass")`              |
| ObjC dynamic call througn string concatenation    |      ❌       | `NSClassFromString(@"My" + @"Class")`        |
| ObjC load method         |      ✅       | `+load{...} `                                |
| ObjC & Swift being inherited       |      ✅       | `SomClass : MyClass`                         |
| ObjC & Swift being properties      |      ✅       | `@property (strong,atomic) MyClass *obj;`    |
| Swift class direct call         |      ✅       | `MyClass.init()`                             |
| Swift call using runtime    |      ❌       | `objc_getClass("Demo.MyClass")`              |
| Swift generic parameters           |      ✅       | `SomeClass<MyClass>.init()`                  |
| Swfit class dynamic call in ObjC   |      ✅       | `NSClassFromString("Demo.MyClass")`          |
| Swift type declaration in the container |      ❌       | `var array:[MyClass]`                        |
| Swift multiple nesting           |      ✅       | ` class SomeClass {class MyClass {...} ...}` |

### App Size Analysis Tool

Supports quick detection of the linked size of a static library. No need to compile and link. **For example: If you want to know how much app size will increase when an SDK is imported or updated, you can use `blades -size` to estimate the size**, without the need to connect the SDK to compile and link successfully to calculate.

### Crash Log Symbolization Tool Without dSYM File

In the case of losing the dSYM file, try to restore the log via `blades -symbol`. **For example, in an app packaging, the dSYM file is cleared after a period of time, but the app file is retained. In this case, you can consider using blades for symbolization. **Before using the tool, pay attention to a few points:

- If your app is a debug package or a package that does not strip the symbol table, you can use `dsymutil app -o xx.dSYM `to extract the symbol table. Then use the symbol table to symbolize the log.

- This tool only supports ObjC, and its principle is to determine the function of the crash by analyzing the address of the ObjC method in Mach-O. Therefore, it is not suitable for Swfit, C, and C++. In addition, tools are not omnipotent, and are only used as emergency supplementary technical means. In daily situations, it is recommended to use symbol tables for log symbolization.


## Contributing & Feedback

We sincerely hope that developers can provide valuable comments and suggestions, and developers can provide feedback on suggestions and problems by submitting PR or Issue.

If you are interested in WBBlades, you can join the WBBlades development group and discuss technical issues together. Please add the wechat account "zqlong320" or scan the QR code below as your friend and then we will invite you to join the group.

![wechat_qrcode.png](imgs/wechat_qrcode.png)

## Related Technical Articles
- [58tongcheng Packet Size Management Tool Decryption](https://juejin.cn/post/7176441132447399993)
- [Important Updates - Designed to Improve Performance](https://mp.weixin.qq.com/s/tXxhnDKerobyxoWuEBGjNQ)
- [Point to Point Analyze and Governance of Crashes](https://mp.weixin.qq.com/s/tGvE-2flzhm4skkrfbUIBA)
- [58tongcheng Size Analysis and Statistics for iOS Client Components](https://blog.csdn.net/csdnnews/article/details/100354658/)
- [Unused Class Detection Based on Mach-O Disassembly](https://www.jianshu.com/p/c41ad330e81c)
- [Open Source｜WBBlades：APP Analysis Tool Set Based on Mach-O File Analysis](https://mp.weixin.qq.com/s/HWJArO5y9G20jb2pqaAQWQ)
- [The Storage Difference between Swift and ObjC from the Perspective of Mach-O](https://www.jianshu.com/p/ef0ff6ee6bc6)
- [New Approach to Swift Hook - Virtual Method Table](https://mp.weixin.qq.com/s/mjwOVdPZUlEMgLUNdT6o9g)

## Thanks

GitHub: [https://github.com/aquynh/capstone](https://github.com/aquynh/capstone "GitHub for capstone")

GitHub: [https://github.com/Sunnyyoung/SYFlatButton](https://github.com/Sunnyyoung/SYFlatButton "GitHub for SYFlatButton") 

GitHub: [https://github.com/nygard/class-dump](https://github.com/nygard/class-dump "GitHub for class-dump")

GitHub: [https://github.com/alexrozanski/PXListView](https://github.com/alexrozanski/PXListView "GitHub for PXListView")

GitHub: [https://github.com/steventroughtonsmith/cartool](https://github.com/steventroughtonsmith/cartool "cartool")

DWARF: [https://www.prevanders.net/dwarf.html#releases](https://www.prevanders.net/dwarf.html#releases "Source Code for DWARF") 



## <a name="WBBlades_cn">简体中文</a>|[English](#WBBlades)

---

## 简介

WBBlades是基于`Mach-O`文件解析的工具集，包括App一键体检（支持`OC`和`Swift`的无用类检测）、包大小分析（支持单个静态库/动态库的包大小分析）、点对点崩溃解析（基于系统日志，支持有符号状态和无符号状态）、基于Mach-O的Class自动提取并Hook能力。主要利用了__Text汇编代码分析、架构提取、符号表剥离、dYSM文件提取、崩溃文件(ips)解析等技术手段实现，支持big method /small method 解析 以及 iOS 15以上关于  dyld_chained_fixups的处理。

#### 工具概览

| 工具名称     | 功能概述                                         | 相关技术                                                     |
| ------------ | ------------------------------------------------ | ------------------------------------------------------------ |
| App一键体检  | Swift/OC无用类检测、无用资源检测、包瘦身优化建议 | 反汇编、__Text指令提取、Mach-O解析、符号表/Swift类在Mach-O中的结构 |
| 包大小分析   | 检测静态库/动态库在.ipa中占用的实际大小          | 架构剥离、静态库结构分析                                     |
| 崩溃解析     | 利用.app或者dYSM对系统崩溃日志解析               | dYSM文件解析、轻量符号表提取、ips文件解析、有/无符号表崩溃解析 |
| Mach-O类Hook | 自动提取任意Mach-O中的Class并Hook                | dyld_chained_fixups 、binding、rebase、big/small method解析  |

新版本在原来基于命令行运行上述工具的基础上，对工具集进行了全面的可视化实现，开箱即用，专为研发提效而设计。另外，在疑难崩溃解析上，针对一些不易复现、且通用工具无法收集的崩溃（被操作系统直接杀死App进程场景），提供了基于系统日志的点对点崩溃解析功能。


## 安装

```
$ git clone https://github.com/wuba/WBBlades.git
$ cd WBBlades
$ pod install
```

## 使用

### 可视化工具用法

target选择：WBBladesCrashApp

点击左侧功能区按钮，点击进入无用类检测、包大小检测等工具，根据工具内的提示进行操作即可，具体的数据会输出到文本框中；

> 可视化工具详细介绍: [可视化工具详细介绍](https://github.com/wuba/WBBlades)

### 命令行工具blades用法

target选择：WBBlades，编译运行，生成命令行工具
将生成的产物blades 拷贝至 /usr/local/bin 下，具体操作如：
sudo cp ${Your_BUILD_DIR}/blades /usr/local/bin

- 无用代码检测 `OC` & `Swift`

  `$ blades -unused xxx.app -from xxx.a xxx.a ....`

  > -from 标识只分析以下静态库中的无用代码，不加此参数默认为APP中全部类

- 包大小分析 (直接测算.a |.framework链接后的大小)

  `$ blades -size xxx.a xxx.framework ....`

  > 支持输入一个文件夹路径，输入后该文件下所有的静态库都会被分析

- 无符号表日志符号化（在丢失符号表的情况下，尝试`OC`崩溃堆栈符号化，不支持`Swift`）

  `$ blades -symbol xxx.app -logPath xxx.ips`

  

## 工具特性介绍

### 无用代码（无用类）检测支持范围

| 说明                     | 是否支持 | 代码示例                                     |
| :----------------------- | :------: | :------------------------------------------- |
| OC 的类的静态调用        |    ✅     | `[MyClass new]`                              |
| OC 的动态调用            |    ✅     | `NSClassFromString(@"MyClass")`              |
| OC 字符串拼接动态调用    |    ❌     | `NSClassFromString(@"My" + @"Class")`        |
| OC load方法使用          |    ✅     | `+load{...} `                                |
| OC & Swift 被继承        |    ✅     | `SomClass : MyClass`                         |
| OC & Swift 作为属性      |    ✅     | `@property (strong,atomic) MyClass *obj;`    |
| Swift 类直接调用         |    ✅     | `MyClass.init()`                             |
| Swift 通过runtime调用    |    ❌     | `objc_getClass("Demo.MyClass")`              |
| Swift 泛型参数           |    ✅     | `SomeClass<MyClass>.init()`                  |
| Swfit 类在OC中动态调用   |    ✅     | `NSClassFromString("Demo.MyClass")`          |
| Swift 容器中作为类型声明 |    ❌     | `var array:[MyClass]`                        |
| Swift 多重嵌套           |    ✅     | ` class SomeClass {class MyClass {...} ...}` |

### 包大小分析工具

支持快速检测一个静态库的链接后大小。无需编译链接。**举例说明：如果你想知道一个接入或更新一个SDK对会增加多少包大小，可以用`blades -size `来预估下大小**，而无需将SDK接入编译链接成功后进行测算。



### 无符号表日志符号化工具

在丢失dSYM文件的情况下，尝试通过`blades -symbol`恢复日志。**例如某次打包，在一段时间后符号表被清除，但是保留了app文件，这种情况下可以考虑使用WBBlades进行符号化**。在工具使用前应先注意几点：

- 如果你的app是debug包或者没有剥离符号表的包，那么可以通过`dsymutil app -o xx.dSYM `来提取符号表。然后用符号表进行日志符号化。
- 工具只适用于OC的场景，其原理为通过分析Mach-O中OC方法地址来确定崩溃的函数。因此不适用于Swfit、C、C++场景。另外，工具并非万能，仅作为应急补充技术手段，日常情况下还是推荐用符号表进行日志符号化。


## 如何贡献&反馈问题

我们诚挚地希望开发者提出宝贵的意见和建议，开发者可以通过提交PR或者Issue来反馈建议和问题。

对WBBlades感兴趣的小伙伴，可以加入WBBlades开发交流群，一起探讨技术问题，请先添加微信号"zqlong320"或者扫描下面的二维码为好友，备注WBBlades后，我们会邀请进群。

![wechat_qrcode.png](imgs/wechat_qrcode.png)

## 相关技术文章

- [58同城iOS包大小治理工具解密](https://juejin.cn/post/7176441132447399993)
- [开源｜WBBlades重要节点更新-专为提效而设计](https://mp.weixin.qq.com/s/tXxhnDKerobyxoWuEBGjNQ)
- [iOS不必现崩溃的点对点解析以及治理](https://mp.weixin.qq.com/s/tGvE-2flzhm4skkrfbUIBA)
- [58 同城 iOS 客户端组件体积分析与统计实践](https://blog.csdn.net/csdnnews/article/details/100354658/)
- [基于mach-o+反汇编的无用类检测](https://www.jianshu.com/p/c41ad330e81c)
- [开源｜WBBlades：基于Mach-O文件解析的APP分析工具](https://mp.weixin.qq.com/s/HWJArO5y9G20jb2pqaAQWQ)
- [从Mach-O角度谈谈Swift和OC的存储差异](https://www.jianshu.com/p/ef0ff6ee6bc6)
- [Swift Hook新思路--虚函数表](https://mp.weixin.qq.com/s/mjwOVdPZUlEMgLUNdT6o9g)


## 致谢

GitHub地址：[https://github.com/aquynh/capstone](https://github.com/aquynh/capstone "GitHub for capstone")

GitHub地址：[https://github.com/Sunnyyoung/SYFlatButton](https://github.com/Sunnyyoung/SYFlatButton "GitHub for SYFlatButton")

GitHub地址：[https://github.com/nygard/class-dump](https://github.com/nygard/class-dump "GitHub for class-dump")

GitHub地址：[https://github.com/alexrozanski/PXListView](https://github.com/alexrozanski/PXListView "GitHub for PXListView")

GitHub地址：[https://github.com/steventroughtonsmith/cartool](https://github.com/steventroughtonsmith/cartool "cartool")

DWARF地址：[https://www.prevanders.net/dwarf.html#releases](https://www.prevanders.net/dwarf.html#releases "Source Code for DWARF") 

GitHub: [https://github.com/nygard/class-dump](https://github.com/nygard/class-dump "GitHub for class-dump")
