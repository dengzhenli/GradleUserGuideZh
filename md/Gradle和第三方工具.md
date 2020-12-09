# Gradle和第三方工具


内容

  * [集成开发环境](#集成开发环境)
  * [持续集成](#持续集成)
  * [如何与Gradle集成](#如何与Gradle集成)
  * [使用Tooling API嵌入Gradle](#使用Tooling_API嵌入Gradle)

Gradle可以与许多不同的第三方工具集成，例如IDE和持续集成平台。在这里，我们看一些更常见的工具，以及如何将自己的工具与Gradle集成。

## [集成开发环境](#集成开发环境)

Android Studio

    

作为IntelliJ IDEA的变体，[Android Studio](https://developer.android.com/studio/)内置了对导入和构建Gradle项目的支持。
如果需要，您也可以使用[IDEA Gradle插件对](https://docs.gradle.org/6.7.1/userguide/idea_plugin.html)导入过程进行微调。

该IDE还具有[广泛的用户指南](https://developer.android.com/studio/intro/)，可帮助您充分利用IDE和Gradle。

Eclipse

    

如果要在Eclipse中具有Gradle构建的项目上工作，则应使用[EclipseBuildship插件](https://projects.eclipse.org/projects/tools.buildship)。
这将允许您导入和运行Gradle构建。如果您需要微调导入过程以正确加载项目，则可以使用[Eclipse Plugins for Gradle](https://docs.gradle.org/6.7.1/userguide/eclipse_plugin.html)。
有关可以执行的微调的详细信息，请参见[相关的发行公告](https://discuss.gradle.org/t/buildship-1-0-18-is-now-available/19012)。

IntelliJ IDEA

    

IDEA具有导入Gradle项目的内置支持。如果您需要微调导入过程以正确加载项目，则可以使用[IDEA Plugin for Gradle](https://docs.gradle.org/6.7.1/userguide/idea_plugin.html)。

NetBeans

    

将[Gradle支持](http://plugins.netbeans.org/plugin/44510/gradle- support)插件添加到NetBeans中，以便使用Gradle构建导入和运行项目。

Visual Studio

    

为了开发C ++项目，Gradle附带了[Visual Studio插件](https://docs.gradle.org/6.7.1/userguide/visual_studio_plugin.html)。

Xcode

    

为了开发C
++项目，Gradle附带了[Xcode插件](https://docs.gradle.org/6.7.1/userguide/xcode_plugin.html)。

CLion

    

JetBrains支持[使用Gradle](https://blog.jetbrains.com/clion/2018/05/clion-starts-2018-2-eap-sanitizers-gradle-db-performance/)
构建[C++项目](https://blog.jetbrains.com/clion/2018/05/clion-starts-2018-2-eap-sanitizers-gradle-db-performance/)。

## [持续集成](#持续集成)

我们有专门的指南，向您展示如何将Gradle项目与以下CI平台集成：

  * [詹金斯](https://guides.gradle.org/executing-gradle-builds-on-jenkins)

  * [团队城市](https://guides.gradle.org/executing-gradle-builds-on-teamcity)

  * [特拉维斯CI](https://guides.gradle.org/executing-gradle-builds-on-travisci)

即使您不使用上述方法之一，您也几乎可以确定将CI平台配置为使用[Gradle Wrapper](/md/gradle_wrapper.md)脚本。

## [如何与Gradle集成](#如何与Gradle集成)

将工具与Gradle集成的主要方法有两种：

  * Gradle构建使用该工具

  * 该工具执行Gradle构建

前一种情况通常[实现为Gradle插件](/md/开发自定义Gradle插件.md)。后者可通过如下所述通过Tooling
API嵌入Gradle来实现。

## [使用Tooling_API嵌入Gradle](#使用Tooling_API嵌入Gradle)

### [工具API简介](#工具API简介)

Gradle提供了一个称为Tooling
API的编程API，可用于将Gradle嵌入到自己的软件中。该API允许您执行和监视构建，并向Gradle查询构建的详细信息。该API的主要受众是IDE，CI服务器和其他UI作者；但是，该API对需要将Gradle嵌入其应用程序的任何人都是开放的。

  * [Gradle TestKit](/md/使用TestKit测试构建逻辑.md#test_kit)使用Tooling API对Gradle插件进行功能测试。

  * [Eclipse Buildship](http://projects.eclipse.org/projects/tools.buildship)使用Tooling API导入Gradle项目并运行任务。

  * [IntelliJ IDEA](https://www.jetbrains.com/idea/)使用Tooling API导入Gradle项目并运行任务。

### [工具API功能](#工具API功能)

Tooling
API的基本特征是它以与版本无关的方式运行。这意味着您可以使用相同的API来处理使用不同版本的Gradle的版本，包括比所使用的Tooling
API的版本新或旧的版本。Tooling API支持Gradle Wrapper，默认情况下，使用与由 Wrapper支持的版本使用的Gradle版本相同的Gradle版本。

工具API提供的一些功能：

  * 查询构建的详细信息，包括项目层次结构和项目依赖项，外部依赖项（包括源和Javadoc jar），每个项目的源目录和任务。

  * 执行构建并收听stdout和stderr日志记录和进度消息（例如，在命令行上运行时，“状态栏”中显示的消息）。

  * 执行特定的测试类或测试方法。

  * 在构建执行时接收有趣的事件，例如项目配置，任务执行或测试执行。

  * 取消正在运行的生成。

  * 将多个单独的Gradle构建合并到一个复合构建中。

  * Tooling API可以下载并安装适当的Gradle版本，类似于wrapper程序。

  * 该实现是轻量级的，仅具有少量依赖性。它也是一个行为良好的库，并且不对您的类加载器结构或日志记录配置进行任何假设。这使该API易于嵌入到您的应用程序中。

### [工具API和Gradle_Build守护进程](#工具API和Gradle_Build守护进程)

工具API始终使用Gradle守护程序。这意味着随后对Tooling
API的调用，无论是模型构建请求还是任务执行请求，都将在相同的长期过程中执行。[Gradle守护程序](/md/Gradle守护程序.md#gradle_daemon)包含有关该守护程序的更多详细信息，尤其是有关分派新守护程序时的情况的信息。

### [快速开始](#快速开始)

由于Tooling API是开发人员的接口，因此Javadoc是其主要文档。

要使用Tooling API，请将以下存储库和依赖项声明添加到构建脚本中：

例子1.使用工具API

`Groovy``Kotlin`

build.gradle

    
    
    repositories {
        maven { url 'https://repo.gradle.org/gradle/libs-releases' }
    }
    
    dependencies {
        implementation "org.gradle:gradle-tooling-api:$toolingApiVersion"
        // The tooling API need an SLF4J implementation available at runtime, replace this with any other implementation
        runtimeOnly 'org.slf4j:slf4j-simple:1.7.10'
    }

build.gradle.kts

    
    
    repositories {
        maven { url = uri("https://repo.gradle.org/gradle/libs-releases") }
    }
    
    dependencies {
        implementation("org.gradle:gradle-tooling-api:$toolingApiVersion")
        // The tooling API need an SLF4J implementation available at runtime, replace this with any other implementation
        runtimeOnly("org.slf4j:slf4j-simple:1.7.10")
    }

Tooling
API的主要入口点是[GradleConnector](https://docs.gradle.org/6.7.1/javadoc/org/gradle/tooling/GradleConnector.html)。您可以从那里导航以找到代码示例，并浏览可用的Tooling
API模型。您可以使用[GradleConnector.connect（）](https://docs.gradle.org/nightly/javadoc/org/gradle/tooling/GradleConnector.html#connect--)创建一个[ProjectConnection](https://docs.gradle.org/6.7.1/javadoc/org/gradle/tooling/ProjectConnection.html)。A`ProjectConnection`连接到单个Gradle项目。使用该连接，您可以执行任务，测试并检索与该项目相关的模型。

### [Java和Gradle版本的兼容性](#Java和Gradle版本的兼容性)

工具API需要Java
8或更高版本。构建使用的Gradle版本可能会强加[其他Java版本要求](/md/兼容性说明.md)。

Tooling API支持使用Gradle 2.6和更高版本运行构建。Gradle 5.0及更高版本要求客户端使用Tooling
API版本3.0或更高版本。

您应该注意，并非Tooling API的所有功能都可用于所有版本的Gradle。有关更多详细信息，请参阅每个类和方法的文档。

通常，Tooling API客户端可以在与构建版本不同的Java版本上运行，但是通过自定义构建操作发送到构建版本的类需要针对受支持的最低Java版本。

  * Gradle 2.6-2.14.1仅支持Java 6到Java 8。

  * Gradle 3.x和4.x需要最低版本的Java 7。

  * Gradle 5及更高版本需要Java 8的最低版本。

