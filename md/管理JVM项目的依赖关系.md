# 管理JVM项目的依赖关系


内容

  * [剖析典型的构建脚本](#sec:setting_up_a_standard_build_script_java_tutorial)
  * [声明模块依赖性](#sec:module_dependencies_java_tutorial)
  * [使用依赖项配置](#sec:configurations_java_tutorial)
  * [声明通用Java存储库](#sec:repositories_java_tutorial)
  * [发布工件](#sec:publishing_artifacts_java_tutorial)

本章说明如何将基本的依赖管理概念应用于基于JVM的项目。有关依赖管理的详细介绍，请参见[Gradle中的依赖管理](/md/Gradle中的依赖管理.md)。

<h2 id = '#sec:setting_up_a_standard_build_script_java_tutorial'> <a href = '#sec:setting_up_a_standard_build_script_java_tutorial'>剖析典型的构建脚本</a> </h2>

让我们看一下基于JVM的项目的非常简单的构建脚本。它应用了[Java库插件](/md/Java库插件.md#java_library_plugin)，该[插件](/md/Java库插件.md#java_library_plugin)会自动引入标准项目布局，提供执行典型工作的任务以及对依赖项管理的充分支持。

示例1.基于JVM的项目的依赖项声明

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'java-library'
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        implementation 'org.hibernate:hibernate-core:3.6.7.Final'
        api 'com.google.guava:guava:23.0'
        testImplementation 'junit:junit:4.+'
    }

build.gradle.kts

    
    
    plugins {
        `java-library`
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        implementation("org.hibernate:hibernate-core:3.6.7.Final")
        api("com.google.guava:guava:23.0")
        testImplementation("junit:junit:4.+")
    }

该[Project.dependencies
{}](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:dependencies\(groovy.lang.Closure\))代码块声明了Hibernate的核心3.6.7.Final需要编译该项目生产的源代码。它还指出，要编译项目的测试，需要junit>
= 4.0。所有依赖项都应该在[Project.repositories
{}](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:repositories\(groovy.lang.Closure\))定义的Maven
Central存储库中进行查找。以下各节将更详细地说明每个方面。

<h2 id = '#sec:module_dependencies_java_tutorial'> <a href = '#sec:module_dependencies_java_tutorial'>声明模块依赖性</a> </h2>

有多种[类型的依赖关系](/md/声明依赖.md#sec:dependency-
types)，你可以声明。一种这样的类型是 _模块依赖性_
。一个[模块依赖](/md/声明依赖.md#sub:module_dependencies)代表与当前构建建外特定版本模块的依赖关系。模块通常存储在存储库中，例如Maven
Central，公司Maven或Ivy存储库或本地文件系统中的目录。

要定义模块依赖项，请将其添加到[依赖项配置中](#sec:configurations_java_tutorial)：

例子2.模块依赖关系的定义

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        implementation 'org.hibernate:hibernate-core:3.6.7.Final'
    }

build.gradle.kts

    
    
    dependencies {
        implementation("org.hibernate:hibernate-core:3.6.7.Final")
    }

要了解有关定义依赖项的更多信息，请[参阅声明依赖项](/md/声明依赖.md)。

<h2 id = '#sec:configurations_java_tutorial'> <a href = '#sec:configurations_java_tutorial'>使用依赖项配置</a> </h2>

一个[配置](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.Configuration.html)是一组命名的依赖和文物。有一个三个主要目的
_配置_ ：

声明依赖

    

插件使用配置使构建作者可以轻松地声明在执行插件定义的任务期间出于各种目的还需要其他哪些子项目或外部工件。例如，一个插件可能需要Spring
Web框架依赖项来编译源代码。

解决依赖性

    

插件使用配置来查找（并可能下载）对其定义的任务的输入。例如，Gradle需要从Maven Central下载Spring Web框架JAR文件。

暴露文物以供消费

    

插件使用配置来定义它生成供其他项目使用的 _工件_ 。例如，该项目希望将其打包在JAR文件中的已编译源代码发布到内部Artifactory存储库中。

考虑到这三个目的，让我们看一下[Java库插件定义](/md/Java库插件.md#sec:java_library_configurations_graph)的一些[标准配置](/md/Java库插件.md#sec:java_library_configurations_graph)。

实作

    

编译项目生产源所需的依赖项，这些依赖项 _不是_ 项目公开的API的一部分。例如，该项目将Hibernate用于其内部持久层实现。

api

    

所需要的依赖关系来编译该项目其生产来源 _是_ 由项目露出的API的一部分。例如，该项目使用Guava，并在其方法签名中公开带有Guava类的公共接口。

测试实施

    

编译和运行项目的测试源所需的依赖项。例如，该项目决定使用测试框架JUnit编写测试代码。

各种插件添加了进一步的标准配置。您还可以通过[Project.configurations{}](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:configurations\(groovy.lang.Closure\))在构建中定义自己的自定义配置。有关定义和自定义依赖项配置的详细信息，请参阅[什么是](/md/声明依赖.md#sec:what-are-dependency-configurations)依赖项配置。

<h2 id = '#sec:repositories_java_tutorial'> <a href = '#sec:repositories_java_tutorial'>声明通用Java存储库</a> </h2>

Gradle如何知道在哪里可以找到外部依赖文件？Gradle在 _存储库中_
查找它们。库是模块，通过组织的集合`group`，`name`和`version`。Gradle了解不同的[存储库类型](/md/声明存储库.md#sec:repository-
types)，例如Maven和Ivy，并支持通过HTTP或其他协议访问存储库的各种方式。

默认情况下，Gradle不定义任何存储库。您需要在[Project.repositories{}](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:repositories\(groovy.lang.Closure\))的帮助下定义至少一个，然后才能使用模块依赖项。一种选择是使用MavenCentral存储库：

例子3. Maven中央仓库的使用

`Groovy``Kotlin`

build.gradle

    
    
    repositories {
        mavenCentral()
    }

build.gradle.kts

    
    
    repositories {
        mavenCentral()
    }

您还可以在本地文件系统上具有存储库。这适用于Maven和Ivy存储库。

例子4.本地Ivy目录的用法

`Groovy``Kotlin`

build.gradle

    
    
    repositories {
        ivy {
            // URL can refer to a local directory
            url "../local-repo"
        }
    }

build.gradle.kts

    
    
    repositories {
        ivy {
            // URL can refer to a local directory
            url = uri("../local-repo")
        }
    }

一个项目可以有多个存储库。Gradle将按照指定的顺序在每个存储库中查找依赖项，并在包含所请求模块的第一个存储库处停止。

要了解有关定义存储库的更多信息，请[参阅声明存储库](/md/声明存储库.md)。

<h2 id = '#sec:publishing_artifacts_java_tutorial'> <a href = '#sec:publishing_artifacts_java_tutorial'>发布工件</a> </h2>

要了解有关发布工件的更多信息，请查看[发布插件](/md/将项目发布为模块.md)。

