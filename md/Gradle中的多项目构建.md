# Gradle中的多项目构建


内容

  * [创建多项目构建](#%E5%88%9B%E5%BB%BA%E5%A4%9A%E9%A1%B9%E7%9B%AE%E6%9E%84%E5%BB%BA)
  * [添加子项目](#%E6%B7%BB%E5%8A%A0%E5%AD%90%E9%A1%B9%E7%9B%AE)
  * [命名建议](#%E5%91%BD%E5%90%8D%E5%BB%BA%E8%AE%AE)

Gradle中的多项目构建由一个根项目和一个或多个子项目组成。

## [创建多项目构建](#%E5%88%9B%E5%BB%BA%E5%A4%9A%E9%A1%B9%E7%9B%AE%E6%9E%84%E5%BB%BA)

基本的多项目构建包含一个根项目和一个子项目。这是一个多项目构建的结构，其中包含一个名为的子项目`app`：

例子1.基本的多项目构建

`Groovy``Kotlin`

项目布局

    
    
    .
    ├── app
    │   ...
    │   └── build.gradle
    └── settings.gradle

Project layout

    
    
    .
    ├── app
    │   ...
    │   └── build.gradle.kts
    └── settings.gradle.kts

这是用于启动任何Gradle项目的推荐项目结构。在[构建初始化插件](https://docs.gradle.org/6.7.1/userguide/build_init_plugin.html)也产生了按照这一结构骨架项目-
用一个单一的子项目根项目。

注意，根项目没有Gradle构建文件，只有一个定义子项目包括的设置文件。

`Groovy``Kotlin`

settings.gradle

    
    
    rootProject.name = 'basic-multiproject'
    include 'app'

settings.gradle.kts

    
    
    rootProject.name = "basic-multiproject"
    include("app")

在这种情况下，Gradle将在`app`目录中查找构建文件。

我们可以通过运行`gradle projects`命令来查看多项目构建的结构。

    
    
    > gradle -q projects
    
    ------------------------------------------------------------
    Root project
    ------------------------------------------------------------
    
    Root project 'basic-multiproject'
    \--- Project ':app'
    
    To see a list of the tasks of a project, run gradle <project-path>:tasks
    For example, try running gradle :app:tasks

假设`app`通过应用[应用程序插件](/md/Java应用插件.md)并配置主类，该子项目是一个Java应用[程序](/md/Java应用插件.md)：

`Groovy``Kotlin`

app / build.gradle

    
    
    plugins {
        id 'application'
    }
    
    application {
        mainClass = 'com.example.Hello'
    }

app/build.gradle.kts

    
    
    plugins {
        id("application")
    }
    
    application {
        mainClass.set("com.example.Hello")
    }

app / src / main / java / com / example / Hello.java

    
    
    package com.example;
    
    public class Hello {
        public static void main(String[] args) {
            System.out.println("Hello, world!");
        }
    }

然后，我们可以通过执行[应用程序插件中](/md/Java应用插件.md)的`run`任务来运行应用[程序](/md/Java应用插件.md)。

    
    
    > gradle -q run
    Hello, world!

这就是创建基本的多项目构建的简单程度。

## [添加子项目](#%E6%B7%BB%E5%8A%A0%E5%AD%90%E9%A1%B9%E7%9B%AE)

假设我们要向`lib`先前创建的项目添加另一个子项目。我们需要做的就是`include`在根设置文件中添加另一条语句：

`Groovy``Kotlin`

settings.gradle

    
    
    rootProject.name = 'basic-multiproject'
    include 'app'
    include 'lib'

settings.gradle.kts

    
    
    rootProject.name = "basic-multiproject"
    include("app")
    include("lib")

然后Gradle将在项目的`lib/`子目录中寻找新子项目的构建文件：

示例2.具有两个子项目的多项目构建

`Groovy``Kotlin`

项目布局

    
    
    .
    ├── app
    │   ...
    │   └── build.gradle
    ├── lib
    │   ...
    │   └── build.gradle
    └── settings.gradle

Project layout

    
    
    .
    ├── app
    │   ...
    │   └── build.gradle.kts
    ├── lib
    │   ...
    │   └── build.gradle.kts
    └── settings.gradle.kts

接下来，将探讨如何[在子项目之间共享](/md/在子项目之间共享构建逻辑.md)构建逻辑[以及子项目](/md/在子项目之间共享构建逻辑.md)
如何[相互依赖](/md/声明子项目之间的依赖关系.md)。

## [命名建议](#%E5%91%BD%E5%90%8D%E5%BB%BA%E8%AE%AE)

随着项目的发展，命名和一致性变得越来越重要。为了使您的构建可维护，我们建议以下操作：

  1. _保留子项目的默认项目名称_ ：可以在设置文件中配置自定义项目名称。但是，对于开发人员来说，跟踪哪个项目属于哪个文件夹是不必要的额外工作。

  2. _所有项目名称均使用kebab大小写格式_ ：kebab大小写格式是当所有字母均小写，单词之间用短划线（'-'）分隔时（例如`kebab-case-formatting`）。这已经是许多大型项目的实际模式。此外，Gradle支持[kebab案例名称的名称缩写](/md/命令行界面.md#名称缩写)。

  3. _在设置文件中定义根项目名称_ ：“ rootProject.name”有效地为整个构建分配一个名称，该名称用于诸如构建扫描的报告中。如果未设置根项目名称，则该名称将是容器目录名称，该名称可能不稳定（即，您可以将项目检出到任何目录）。

