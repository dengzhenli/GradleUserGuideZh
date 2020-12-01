# 组织Gradle项目


内容

  * [特定于语言的单独源文件](#特定于语言的单独源文件)
  * [每种测试类型的源文件分开](#每种测试类型的源文件分开)
  * [尽可能使用标准约定](#尽可能使用标准约定)
  * [始终定义一个设置文件](#始终定义一个设置文件)
  * [用`buildSrc`抽象逻辑势在必行](#用`buildSrc`抽象逻辑势在必行)
  * [声明`gradle.properties`文件中的属性](#声明`gradle.properties`文件中的属性)
  * [避免任务输出重叠](#避免任务输出重叠)
  * [使用自定义Gradle发行版对构建进行标准化](#使用自定义Gradle发行版对构建进行标准化)

每个软件项目的源代码和构建逻辑都应以有意义的方式进行组织。该页面列出了导致可读性，可维护性项目的最佳实践。以下各节还探讨了常见问题以及如何避免它们。

## [特定于语言的单独源文件](#特定于语言的单独源文件)

Gradle的语言插件建立了发现和编译源代码的约定。例如，应用[Java插件](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#java_plugin)的项目将自动编译directory目录中的代码`src/main/java`。其他语言插件遵循相同的模式。目录路径的最后部分通常指示源文件的预期语言。

一些编译器能够在同一源目录中交叉编译多种语言。Groovy编译器可以处理混合Java和Groovy源文件的场景`src/main/groovy`。Gradle建议您根据源语言将源放置在目录中，因为构建性能更高，并且用户和构建都可以做出更强的假设。

以下源代码树包含Java和Kotlin源文件。Java源文件位于其中`src/main/java`，而Kotlin源文件位于其中`src/main/kotlin`。

`Groovy``Kotlin`

    
    
    .
    ├── build.gradle
    └── src
        └── main
            ├── java
            │   └── HelloWorld.java
            └── kotlin
                └── Utils.kt
    
    
    .
    ├── build.gradle.kts
    └── src
        └── main
            ├── java
            │   └── HelloWorld.java
            └── kotlin
                └── Utils.kt

## [每种测试类型的源文件分开](#每种测试类型的源文件分开)

一个项目定义并执行不同类型的测试是很常见的，例如单元测试，集成测试，功能测试或冒烟测试。最好将每种测试类型的测试源代码存储在专用的源目录中。分离的测试源代码对可维护性和关注点分离有积极的影响，因为您可以彼此独立地运行测试类型。

看一下该[示例](https://docs.gradle.org/6.7.1/samples/sample_jvm_multi_project_with_additional_test_types.html)
，该[示例](https://docs.gradle.org/6.7.1/samples/sample_jvm_multi_project_with_additional_test_types.html)演示如何将单独的集成测试配置添加到基于Java的项目中。

## [尽可能使用标准约定](#尽可能使用标准约定)

所有Gradle核心插件[在配置方面均](https://en.wikipedia.org/wiki/Convention_over_configuration)遵循软件工程范例[约定](https://en.wikipedia.org/wiki/Convention_over_configuration)。插件逻辑可在特定上下文中为用户提供合理的默认值和标准，约定。让我们以[Java插件](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#java_plugin)为例。

  * 它将目录定义`src/main/java`为编译的默认源目录。

  * 编译后的源代码和其他工件（例如JAR文件）的输出目录为`build`。

通过遵循默认约定，项目的新开发人员立即知道如何找到解决方法。尽管可以重新配置这些约定，但使构建脚本用户和作者来管理构建逻辑及其结果变得更加困难。除非您需要适应旧项目的布局，否则请尽量遵循默认约定。请参阅相关插件的参考页面以了解其默认约定。

## [始终定义一个设置文件](#始终定义一个设置文件)

Gradle会在每次调用构建时尝试找到一个`settings.gradle`（Groovy
DSL）或`settings.gradle.kts`（Kotlin
DSL）文件。为此，运行时将目录树的层次结构移至根目录。一旦找到设置文件，该算法即停止搜索。

始终将a添加`settings.gradle`到构建的根目录中，以避免对最初的性能产生影响。此建议适用于单个项目构建以及多个项目构建。该文件可以为空，也可以定义所需的项目名称。

具有设置文件的典型Gradle项目如下所示：

`Groovy``Kotlin`

    
    
    .
    ├── settings.gradle
    ├── subproject-one
    │   └── build.gradle
    └── subproject-two
        └── build.gradle
    
    
    .
    ├── settings.gradle.kts
    ├── subproject-one
    │   └── build.gradle.kts
    └── subproject-two
        └── build.gradle.kts

## [用`buildSrc`抽象逻辑势在必行](#用`buildSrc`抽象逻辑势在必行)

复杂的构建逻辑通常很适合作为自定义任务或二进制插件进行封装。自定义任务和插件实现不应存在于构建脚本中。`buildSrc`只要不需要在多个独立项目之间共享代码，就可以非常方便地使用该代码。

该目录`buildSrc`被视为[包含的构建](/md/复合构建.md#什么是复合构建？)。发现目录后，Gradle会自动编译并测试此代码，并将其放入构建脚本的类路径中。对于多项目构建，只能有一个`buildSrc`目录，该目录必须位于根项目目录中。
`buildSrc`应该比[脚本插件](/md/使用Gradle插件.md#脚本插件)更可取，因为它更易于维护，重构和测试代码。

`buildSrc`使用适用于Java和Groovy项目的相同[源代码约定](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#javalayout)。它还提供对Gradle
API的直接访问。其他依赖项可以在专用的`build.gradle`下声明`buildSrc`。

示例1.自定义buildSrc构建脚本

`Groovy``Kotlin`

buildSrc / build.gradle

    
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        testImplementation 'junit:junit:4.13'
    }

buildSrc/build.gradle.kts

    
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        testImplementation("junit:junit:4.13")
    }

一个典型的项目包括`buildSrc`以下布局。下面的任何代码`buildSrc`都应使用类似于应用程序代码的包。（可选）`buildSrc`如果需要其他配置（例如，应用插件或声明依赖项），该目录可以托管构建脚本。

`Groovy``Kotlin`

    
    
    .
    ├── buildSrc
    │   ├── build.gradle
    │   └── src
    │       ├── main
    │       │   └── java
    │       │       └── com
    │       │           └── enterprise
    │       │               ├── Deploy.java
    │       │               └── DeploymentPlugin.java
    │       └── test
    │           └── java
    │               └── com
    │                   └── enterprise
    │                       └── DeploymentPluginTest.java
    ├── settings.gradle
    ├── subprojecto-one
    │   └── build.gradle.kts
    └── subproject-two
        └── build.gradle.kts
    
    
    .
    ├── buildSrc
    │   ├── build.gradle.kts
    │   └── src
    │       ├── main
    │       │   └── java
    │       │       └── com
    │       │           └── enterprise
    │       │               ├── Deploy.java
    │       │               └── DeploymentPlugin.java
    │       └── test
    │           └── java
    │               └── com
    │                   └── enterprise
    │                       └── DeploymentPluginTest.java
    ├── settings.gradle.kts
    ├── subproject-one
    │   └── build.gradle.kts
    └── subproject-two
        └── build.gradle.kts

╔═════════════════════════════   
更改`buildSrc`会导致整个项目过时。因此，当进行小的增量更改时，[`--no-
rebuild`命令行选项](/md/命令行界面.md#执行选项)通常有助于获得更快的反馈。不过请记住要定期或至少在完成后运行完整版本。  
╚═════════════════════════════  
  
## [声明`gradle.properties`文件中的属性](#声明`gradle.properties`文件中的属性)

在Gradle中，可以在构建脚本中，`gradle.properties`文件中或命令行中将属性定义为参数。

在临时方案中，通常在命令行上声明属性。例如，您可能希望仅针对构建的这一调用传递一个特定的属性值来控制运行时行为。构建脚本中的属性很容易引起维护麻烦，并且使构建脚本逻辑复杂化。`gradle.properties`将属性与构建脚本分开的帮助，应作为可行的选项加以探讨。这是放置[控制构建环境的属性](/md/Gradle环境搭建.md#Gradle属性)的好位置。

典型的项目设置将`gradle.properties`文件放置在构建的根目录中。另外，`GRADLE_USER_HOME`如果您想将该文件应用于计算机上的所有内部版本，则该文件也可以位于目录中。

`Groovy``Kotlin`

    
    
    .
    ├── gradle.properties
    └── settings.gradle
    ├── subproject-a
    │   └── build.gradle
    └── subproject-b
        └── build.gradle
    
    
    .
    ├── gradle.properties
    └── settings.gradle.kts
    ├── subproject-a
    │   └── build.gradle.kts
    └── subproject-b
        └── build.gradle.kts

## [避免任务输出重叠](#避免任务输出重叠)

任务应该定义输入和输出，以获得[增量构建功能](/md/处理任务.md#最新检查（又称增量构建）)的性能优势。在声明任务的输出时，请确保用于写入输出的目录在项目中的所有任务中都是唯一的。

混合或覆盖由不同任务生成的输出文件会损害最新的检查，从而导致构建速度变慢。反过来，这些文件系统更改可能会阻止Gradle的[构建缓存](/md/构建缓存.md#build_cache)正确识别和缓存本来可以缓存的任务。

## [使用自定义Gradle发行版对构建进行标准化](#使用自定义Gradle发行版对构建进行标准化)

企业通常希望通过定义通用约定或规则来标准化组织中所有项目的构建平台。您可以借助初始化脚本来实现。
[初始化脚本](/md/初始化脚本.md#init_scripts)使在单个计算机上的所有项目中应用构建逻辑变得极为容易。例如，声明内部存储库及其凭证。

该方法有一些缺点。首先，您将必须在公司中所有开发人员之间交流设置过程。此外，统一更新初始化脚本逻辑可能会带来挑战。

自定义Gradle发行版是解决此问题的实用方法。自定义Gradle发行版由标准Gradle发行版以及一个或多个自定义初始化脚本组成。初始化脚本与发行版捆绑在一起，并在每次运行构建时应用。开发人员仅需要将其签入的[Wrapper](/md/gradle_wrapper.md#gradle_wrapper)文件指向自定义Gradle发行版的URL。

自定义Gradle发行版还可能`gradle.properties`在发行版的根目录中包含一个文件，该文件提供了组织范围[内控制构建环境的一组属性](/md/Gradle环境搭建.md#Gradle属性)。

以下步骤是创建自定义Gradle发行版的典型步骤：

  1. 实施用于下载和重新打包Gradle发行版的逻辑。

  2. 用所需的逻辑定义一个或多个初始化脚本。

  3. 将初始化脚本与Gradle分发包捆绑在一起。

  4. 将Gradle发行档案上传到HTTP服务器。

  5. 更改所有项目的包装器文件，以指向自定义Gradle分发的URL。

例子2.建立一个定制的Gradle发行版

build.gradle

    
    
    plugins {
        id 'base'
    }
    
    // This is defined in buildSrc
    import org.gradle.distribution.DownloadGradle
    
    version = '0.1'
    
    task downloadGradle(type: DownloadGradle) {
        description = 'Downloads the Gradle distribution with a given version.'
        gradleVersion = '4.6'
    }
    
    task createCustomGradleDistribution(type: Zip) {
        description = 'Builds custom Gradle distribution and bundles initialization scripts.'
    
        dependsOn downloadGradle
    
        def projectVersion = project.version
        archiveFileName = downloadGradle.gradleVersion.map { gradleVersion ->
            "mycompany-gradle-${gradleVersion}-${projectVersion}-bin.zip"
        }
    
        from zipTree(downloadGradle.destinationFile)
    
        from('src/init.d') {
            into "${downloadGradle.distributionNameBase.get()}/init.d"
        }
    }

