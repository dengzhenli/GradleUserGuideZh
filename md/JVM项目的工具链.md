# JVM项目的工具链


内容

  * [消费工具链](#消费工具链)
  * [为个别任务指定自定义工具链](#为个别任务指定自定义工具链)
  * [自动检测已安装的工具链](#自动检测已安装的工具链)
  * [自动配置](#自动配置)
  * [自定义工具链位置](#自定义工具链位置)
  * [插件作者的工具链](#插件作者的工具链)

默认情况下，Gradle使用相同的Java版本来运行Gradle本身和构建JVM项目。

这并不总是可取的。在不同的开发人员机器和CI服务器上使用不同的Java版本构建项目可能会导致意外问题。此外，您可能想使用不支持运行Gradle的Java版本来构建项目。

Java工具链（从现在开始简称为工具链）是一组工具，通常取自本地JRE /
JDK安装，用于配置构建的不同方面。编译任务可以`javac`用作其编译器，而test和exec任务可以使用该`java`命令，同时`javadoc`将用于生成文档。

## [消费工具链](#消费工具链)

构建可以通过声明所需的Java语言版本来全局定义其目标工具链：

`Groovy``Kotlin`

buildSrc / src / main / groovy / myproject.java-conventions.gradle

    
    
    java {
        toolchain {
            languageVersion = JavaLanguageVersion.of(14)
        }
    }

buildSrc/src/main/kotlin/myproject.java-conventions.gradle.kts

    
    
    java {
        toolchain {
            languageVersion.set(JavaLanguageVersion.of(14))
        }
    }

现在，执行构建（例如使用`gradle check`）将为您和其他运行构建的人处理几件事

  1. 设置所有编译，测试和javadoc任务以使用已定义的工具链，该工具链可能与Gradle本身使用的工具链不同

  2. Gradle检测[本地安装的JVM](#自动检测已安装的工具链)

  3. Gradle选择符合构建要求的JRE / JDK（在这种情况下，JVM支持Java 14）

  4. 如果找不到匹配的JVM，它将自动从[AdoptOpenJDK](https://adoptopenjdk.net/)下载匹配的JDK。[](https://adoptopenjdk.net/)

╔═════════════════════════════  

工具链支持仅在Java插件及其定义的任务中可用。Groovy和Scala插件的支持将在以后发布。该支持将涵盖这些插件添加的特定编译和文档编制任务。但是，由于任务确实支持工具链，因此
_运行测试_ 可以利用工具链的支持`Test`。  
  
╚═════════════════════════════    
  
## [为个别任务指定自定义工具链](#为个别任务指定自定义工具链)

如果要调整用于特定任务的工具链，可以指定任务使用的确切工具。例如，该`Test`任务公开了一个`JavaLauncher`属性，该属性定义了用于启动测试的java可执行文件。

在下面的示例中，我们将默认工具链配置为使用JDK8进行编译和测试。此外，我们引入了一个新`Test`任务，它将使用JDK 14运行单元测试。

`Groovy``Kotlin`

list / build.gradle

    
    
    tasks.withType(JavaCompile).configureEach {
        javaCompiler = javaToolchains.compilerFor {
            languageVersion = JavaLanguageVersion.of(8)
        }
    }
    test {
        javaLauncher = javaToolchains.launcherFor {
            languageVersion = JavaLanguageVersion.of(14)
        }
    }

list/build.gradle.kts

    
    
    tasks.withType<JavaCompile>().configureEach {
        javaCompiler.set(javaToolchains.compilerFor {
            languageVersion.set(JavaLanguageVersion.of(8))
        })
    }
    
    tasks.withType<Test>().configureEach {
        javaLauncher.set(javaToolchains.launcherFor {
            languageVersion.set(JavaLanguageVersion.of(14))
        })
    }

根据任务的不同，一个JRE可能就足够了，而对于其他任务（例如，编译），则需要一个JDK。默认情况下，Gradle比JRE更喜欢安装的JDK。

可以从[`javaToolchains`](https://docs.gradle.org/6.7.1/javadoc/org/gradle/jvm/toolchain/JavaToolchainService.html)扩展中获得工具链工具提供者。

提供三种工具：

  * A`JavaCompiler`是`JavaCompile`任务使用的工具

  * A`JavaLauncher`是`JavaExec`或`Test`任务使用的工具

  * A`JavadocTool`是`Javadoc`任务使用的工具

### [与依赖Java可执行文件或Java主目录的任务集成](#与依赖Java可执行文件或Java主目录的任务集成)

可以使用Java可执行文件的路径或Java主目录配置的任何任务都可以从工具链中受益。

尽管您将无法直接连接工具链工具，但是它们都具有元数据，可以访问其完整路径或它们所属的Java安装路径。

例如，您可以按以下方式为Kotlin编译任务配置可执行文件：

`Groovy``Kotlin`

build.gradle

    
    
    def compiler = javaToolchains.compilerFor {
        languageVersion = JavaLanguageVersion.of(11)
    }
    
    tasks.withType(KotlinJvmCompile).configureEach {
        kotlinOptions.jdkHome = compiler.get().metadata.installationPath.asFile.absolutePath
    }

build.gradle.kts

    
    
    val compiler = javaToolchains.compilerFor {
        languageVersion.set(JavaLanguageVersion.of(11))
    }
    
    tasks.withType<KotlinJvmCompile>().configureEach {
        kotlinOptions.jdkHome = compiler.get().metadata.installationPath.asFile.absolutePath
    }

同样，这样做`compiler.get().executablePath`会为您`javac`提供给定工具链的完整路径。但是请注意，这可能会急切地实现（并提供）工具链。

## [自动检测已安装的工具链](#自动检测已安装的工具链)

默认情况下，Gradle自动检测本地JRE / JDK安装，因此用户不需要进一步的配置。以下是JVM自动检测支持的常见程序包管理器和位置的列表。

操作系统特定的位置：

  * 的Linux

  * 苹果系统

  * 视窗

支持的软件包管理器：

  * [音频文件](https://asdf-vm.com/#/)

  * [贾巴](https://github.com/shyiko/jabba)

  * [SDKMAN！](https://sdkman.io/)

### [如何禁用自动检测](#如何禁用自动检测)

为了禁用自动检测，可以使用`org.gradle.java.installations.auto-detect`Gradle属性：

  * 要么使用开始gradle `-Porg.gradle.java.installations.auto-detect=false`

  * 或放入`org.gradle.java.installations.auto-detect=false`您的`gradle.properties`文件中。

## [自动配置](#自动配置)

如果Gradle找不到与构建要求匹配的本地可用工具链，它将自动尝试从AdoptOpenJDK下载它。默认情况下，它将请求与当前操作系统和体系结构匹配的HotSpot
JDK。预配JDK安装在[Gradle用户主目录中](/md/Gradle目录和文件的使用.md#Gradle用户主目录)。

╔═════════════════════════════  

Gradle将仅下载GA发行版的JDK版本。不支持下载早期访问版本。  
  
╚═════════════════════════════    
  
默认情况下，公共[AdoptOpenJDK
API](https://api.adoptopenjdk.net/)用于确定和下载匹配的JDK。如果您要使用与AdoptOpenJDK API
v3兼容的另一台服务器，则可以使Gradle使用其他主机。为此，您可以使用Gradle属性，如以下示例所示：

    
    
    org.gradle.jvm.toolchain.install.adoptopenjdk.baseUri = https：//api.company.net/

╔═════════════════════════════  

仅`https`接受像这样的安全协议。这是确保没有人可以篡改飞行中的下载所必需的。  
  
╚═════════════════════════════    
  
### [如何禁用自动配置](#如何禁用自动配置)

为了禁用自动配置，可以使用`org.gradle.java.installations.auto-download`Gradle属性：

  * 要么使用开始gradle `-Porg.gradle.java.installations.auto-download=false`

  * 或把`org.gradle.java.installations.auto-download=false`成一个`gradle.properties`文件。

## [自定义工具链位置](#自定义工具链位置)

如果自动检测本地工具链不足或被禁用，则可以通过其他方法让Gradle知道已安装的工具链。

如果您的设置已经提供了指向已安装的JVM的环境变量，则还可以让Gradle知道要考虑哪些环境变量。假设环境变量`JDK8`并`JRE14`指向有效的Java安装，以下内容指示Gradle在寻找匹配的工具链时解析这些环境变量并考虑这些安装。

    
    
    org.gradle.java.installations.fromEnv = JDK8，JRE14

另外，您可以使用该`org.gradle.java.installations.paths`属性提供以逗号分隔的特定安装路径的列表。例如，在您的代码中使用以下命令`gradle.properties`可使Gradle知道检测JVM时要查看的目录。Gradle会将这些目录视为可能的安装，但不会下降到任何嵌套目录中。

    
    
    org.gradle.java.installations.paths = / custom / path / jdk1.8，/ shared / jre11

## [插件作者的工具链](#插件作者的工具链)

需要JDK中的工具的自定义任务应将`Property<T>`带有所需工具的公开为通用类型。通过`JavaToolchainService`在插件或任务中注入，也可以通过`JavaToolchainSpec`从`java`项目扩展名中获取来在这些属性中连接约定。下面的示例展示了如何使用默认工具链作为约定，同时允许用户针对每个任务单独配置工具链。

`Groovy``Kotlin`

build.gradle

    
    
    import javax.inject.Inject;
    
    class CustomTaskUsingToolchains extends DefaultTask {
    
        @Input
        final Property<JavaLauncher> launcher = project.objects.property(JavaLauncher.class);
    
        @Inject
        CustomTaskUsingToolchains(ObjectFactory factory) {
            // Access the default toolchain
            def toolchain = project.getExtensions().getByType(JavaPluginExtension.class).toolchain
    
            // acquire a provider that returns the launcher for the toolchain
            JavaToolchainService service = project.getExtensions().getByType(JavaToolchainService.class);
            Provider<JavaLauncher> defaultLauncher = service.launcherFor(toolchain);
    
            // use it as our default for the property
            launcher.convention(defaultLauncher);
        }
    
        @TaskAction
        def showConfiguredToolchain() {
            println launcher.get().executablePath
            println launcher.get().metadata.installationPath
        }
    }
    
    plugins {
        id 'java'
    }
    
    java {
        toolchain {
            languageVersion = JavaLanguageVersion.of(8)
        }
    }
    
    task showDefaultToolchain(type: CustomTaskUsingToolchains)
    
    task showCustomToolchain(type: CustomTaskUsingToolchains) {
        launcher = javaToolchains.launcherFor {
            languageVersion = JavaLanguageVersion.of(14)
        }
    }

build.gradle.kts

    
    
    import javax.inject.Inject;
    
    open class CustomTaskUsingToolchains : DefaultTask {
    
        @get:Input
        val launcher: Property<JavaLauncher> = project.objects.property()
    
        @Inject
        constructor() {
            // Access the default toolchain
            val toolchain = project.extensions.getByType<JavaPluginExtension>().toolchain
    
            // acquire a provider that returns the launcher for the toolchain
            val service = project.extensions.getByType<JavaToolchainService>()
            val defaultLauncher = service.launcherFor(toolchain)
    
            // use it as our default for the property
            launcher.convention(defaultLauncher);
        }
    
        @TaskAction
        fun showConfiguredToolchain() {
            println(launcher.get().executablePath)
            println(launcher.get().metadata.installationPath)
        }
    }
    
    plugins {
        java
    }
    
    java {
        toolchain {
            languageVersion.set(JavaLanguageVersion.of(8))
        }
    }
    
    tasks.register<CustomTaskUsingToolchains>("showDefaultToolchain")
    
    tasks.register<CustomTaskUsingToolchains>("showCustomToolchain") {
        launcher.set(javaToolchains.launcherFor {
            languageVersion.set(JavaLanguageVersion.of(14))
        })
    }

