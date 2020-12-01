# 什么是Gradle？

内容

* [总览](#总览)
* [您需要了解有关Gradle的五件事](#您需要了解有关Gradle的五件事)

## [总览](#总览)

Gradle是一个开放源代码的[构建自动化](https://en.wikipedia.org/wiki/Build_automation)工具，旨在灵活地构建几乎任何类型的软件。以下是其一些最重要功能的高级概述：

高性能

Gradle通过仅运行需要运行的任务来避免不必要的工作，因为它们的输入或输出已更改。您还可以使用构建缓存来重用以前运行的任务输出，甚至可以使用其他计算机（具有共享的构建缓存）重用任务输出。

Gradle实施了许多其他优化，并且开发团队不断努力以提高Gradle的性能。

JVM基础

Gradle在JVM上运行，您必须安装Java开发工具包（JDK）才能使用它。对于熟悉Java平台的用户来说，这是一个好处，因为您可以在构建逻辑中使用标准Java API，例如自定义任务类型和插件。它还使在不同平台上运行Gradle变得容易。

请注意，Gradle不仅限于构建JVM项目，它甚至附带对构建本机项目的支持。

约束

和Maven一样，Gradle通过实现约定使常见类型的项目（例如Java项目）易于构建。应用适当的插件，您可以轻松地为许多项目使用精简的构建脚本。但是这些约定并没有限制您：Gradle允许您覆盖它们，添加自己的任务以及对基于约定的构建进行许多其他自定义操作。

可扩展性

您可以轻松扩展Gradle以提供您自己的任务类型甚至构建模型。有关此示例，请参见Android构建支持：它添加了许多新的构建概念，例如flavors和构建类型（build types）。

IDE支持

几个主要的IDE允许您导入Gradle构建并与其进行交互：Android Studio，IntelliJ IDEA，Eclipse和NetBeans。Gradle还支持生成将项目加载到Visual Studio所需的解决方案文件。

洞悉

[构建扫描](https://scans.gradle.com/)提供了有关构建运行的广泛信息，可用于识别构建问题。他们特别擅长帮助您确定构建性能的问题。您还可以与其他人共享构建扫描，如果您需要咨询以解决构建问题，这将特别有用。

## [您需要了解有关Gradle的五件事](#您需要了解有关Gradle的五件事)

Gradle是一种灵活而强大的构建工具，在您初次启动时会很容易感到恐惧。但是，了解以下核心原则将使Gradle更加容易上手，并且您将在不了解该工具的情况下熟练掌握该工具。

### [1\__Gradle是通用的构建工具](#1\__Gradle是通用的构建工具)

Gradle允许您构建任何软件，因为它对正在尝试构建的内容或应如何完成的工作做出很少的假设。最明显的限制是，依赖项管理当前仅支持与Maven和Ivy兼容的存储库以及文件系统。

这并不意味着您需要做很多工作来创建构建。Gradle通过添加一层约定和通过[_插件_](/md/使用Gradle插件.md#plugins)预先构建的功能，使构建通用类型的项目（例如Java库）变得容易。您甚至可以创建和发布自定义插件来封装自己的约定并构建功能。

### [2_核心模型基于任务](#2_核心模型基于任务)

Gradle将其构建模型建模为任务（工作单元）的有向无环图（DAG）。这意味着构建实质上配置了一组任务，并根据它们的依赖关系将它们连接在一起以创建该DAG。创建任务图后，Gradle将确定需要按顺序运行的任务，然后继续执行它们。

此图显示了两个示例任务图，一个是抽象图，另一个是具体图，其中任务之间的依赖性表示为箭头：
![Gradle任务图的两个示例](https://docs.gradle.org/current/userguide/img/task-dag-examples.png)



这样，几乎所有构建过程都可以建模为任务图，这就是Gradle如此灵活的原因之一。任务图既可以由插件定义，也可以由您自己的构建脚本定义，任务通过[任务依赖机制](/md/构建脚本基础.md#任务依赖性)链接在一起。

任务本身包括：

* 动作-做某事的工作，例如复制文件或编译源代码
* 输入-操作使用或对其进行操作的值，文件和目录
* 输出-操作修改或生成的文件和目录

实际上，以上所有内容都是可选的，具体取决于任务需要执行的操作。有些任务（例如[标准生命周期任务）](https://docs.gradle.org/6.7.1/userguide/base_plugin.html#sec:base_tasks)甚至没有任何动作。他们只是为了方便而将多个任务汇总在一起。

> 您选择要运行的任务。通过指定执行所需任务的任务来节省时间，但仅此而已。如果您只想运行单元测试，请选择执行该任务的任务-通常为test。如果您要打包应用程序，则大多数构建都有一个assemble任务。

最后一件事：Gradle的[增量构建](/md/处理任务.md#最新检查（又称增量构建）)支持是可靠且可靠的，因此，`clean`除非您确实想执行清理操作，否则避免执行该任务以保持构建快速运行。

### [3\__Gradle有几个固定的构建阶段](#3\__Gradle有几个固定的构建阶段)

重要的是要了解Gradle分三个阶段评估和执行构建脚本：

1.  初始化  

    设置构建环境，并确定哪些项目将参与其中。
2.  组态  

    构造和配置构建的任务图，然后根据用户要运行的任务确定需要运行的任务和运行顺序。
3.  执行  

    运行在配置阶段结束时选择的任务。

这些阶段构成了Gradle的[Build Lifecycle](/md/构建生命周期.md#build_lifecycle)。

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-note"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><div class="title" style="color:rgb(85, 85, 85);font-size:1.25rem;"><font><font>与Apache Maven术语的比较</font></font></div><div class="paragraph"><p style="font-size: 1rem;"><font><font>Gradle的构建阶段与Maven的阶段不同。</font><font>Maven使用其阶段将构建执行划分为多个阶段。</font><font>它们的作用与Gradle的任务图相似，但灵活性较差。</font></font></p></div><div class="paragraph"><p style="font-size: 1rem;"><font><font>Maven的构建生命周期概念与Gradle的</font></font><a href="" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>生命周期任务</font></font></a><font><font>大致相似</font><font>。</font></font></p></div></td></tr></tbody></table>

设计良好的构建脚本主要由[声明性配置而不是命令式逻辑组成](/md/创作可维护版本的最佳实践.md#避免在脚本中使用命令式逻辑)。可以理解，在配置阶段评估该配置。即便如此，许多此类构建也具有任务操作（例如，通过`doLast {}`和`doFirst {}`块），这些任务在执行阶段进行评估。这很重要，因为在配置阶段评估的代码不会看到在执行阶段发生的更改。

配置阶段的另一个重要方面是，_每次构建运行时都要_评估其中涉及的所有内容。这就是为什么最佳做法是[在配置阶段避免昂贵的工作](/md/创作可维护版本的最佳实践.md#最小化在配置阶段执行的逻辑)。[构建扫描](https://scans.gradle.com/)可以帮助您识别此类热点。

### [4\__Gradle的扩展方式不止一种](#4\__Gradle的扩展方式不止一种)

如果您可以仅使用Gradle捆绑的构建逻辑来构建项目，那将是很好的，但这几乎是不可能的。大多数构建都有一些特殊要求，这意味着您需要添加自定义构建逻辑。

Gradle提供了多种机制来扩展它，例如：

* [自定义任务类型](/md/开发自定义Gradle任务类型.md)。  

  当您希望构建完成现有任务无法完成的工作时，只需编写自己的任务类型即可。通常最好将自定义任务类型的源文件放在[_buildSrc_](/md/组织Gradle项目.md#用`buildSrc`抽象逻辑势在必行)目录或打包的插件中。然后，您可以像Gradle提供的任何任务一样使用自定义任务类型。
* 自定义任务动作。  

  您可以通过[Task.doFirst（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Task.html#org.gradle.api.Task:doFirst\(org.gradle.api.Action\))
  和[Task.doLast（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Task.html#org.gradle.api.Task:doLast\(org.gradle.api.Action\))方法附加在任务之前或之后执行的自定义构建逻辑。
* 项目和任务的[额外属性](/md/编写构建脚本.md#额外的属性)。  

  这些允许您将自己的属性添加到项目或任务中，然后可以从您自己的自定义操作或任何其他构建逻辑中使用它们。额外的属性甚至可以应用于您未明确创建的任务，例如Gradle的核心插件创建的任务。
* 自定义约定。  

  约定是简化构建的强大方法，因此用户可以更轻松地理解和使用它们。使用标准项目结构和命名约定的[构建](/md/构建Java和JVM项目.md#building_java_projects)（例如[Java build）](/md/构建Java和JVM项目.md#building_java_projects)可以看出这一点。您可以编写自己的提供约定的插件-它们只需要为构建的相关方面配置默认值。
* [自定义模型](https://guides.gradle.org/implementing-gradle-plugins/#modeling_dsl_like_apis)。  

  Gradle允许您在任务，文件和依赖项配置之外将新概念引入构建。您可以在大多数语言插件中看到这一点，这些插件将[_源集_](/md/构建Java和JVM项目.md#通过源集声明源文件)的概念添加到了构建中。构建过程的适当建模可以大大提高构建的易用性及其效率。

### [5_构建脚本针对API运行](#5_构建脚本针对API运行)

将Gradle的构建脚本视为可执行代码很容易，因为它们本来就是。但是，这是一个实现细节：设计良好的构建脚本描述了构建软件需要哪些步骤，而不是这些步骤应该如何完成工作。那是自定义任务类型和插件的工作。
>  有一个普遍的误解，认为Gradle的强大和灵活性来自于它的构建脚本是代码这一事实。事实并不是这样，提供功能的是底层模型和API。
正如我们在最佳实践中建议的那样，您应该[避免在构建脚本中放置太多（如果有的话）命令式逻辑](/md/创作可维护版本的最佳实践.md#避免在脚本中使用命令式逻辑)

然而，在一个区域中，将构建脚本视为可执行代码很有用：了解构建脚本的语法如何映射到Gradle的API。由[Groovy DSL参考](https://docs.gradle.org/6.7.1/dsl/)和[Javadocs](https://docs.gradle.org/6.7.1/javadoc/)组成的API文档列出了方法和属性，并引用了闭包和操作。这些在构建脚本的上下文中是什么意思？
查看[Groovy Build Script Primer，](https://docs.gradle.org/6.7.1/userguide/groovy_build_script_primer.html#groovy_build_script_primer)以了解该问题的答案，以便您可以有效地使用API文档。
> 由于Gradle在JVM上运行，因此构建脚本也可以使用标准[Java API](https://docs.oracle.com/javase/8/docs/api)。Groovy构建脚本可以另外使用Groovy API，而Kotlin构建脚本可以使用Kotlin。
