# 应用插件


内容

  * [构建JVM应用程序](#构建JVM应用程序)
  * [使用Java模块系统构建应用程序](#使用Java模块系统构建应用程序)
  * [建立发行版](#建立发行版)
  * [任务](#任务)
  * [应用扩展](#应用扩展)
  * [发牌](#发牌)
  * [约定属性（已弃用）](#约定属性（已弃用）)

应用程序插件有助于创建可执行的JVM应用程序。它使在开发过程中本地启动应用程序以及将应用程序打包为TAR和/或ZIP（包括操作系统特定的启动脚本）变得容易。

应用Application插件也隐式应用[Java插件](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#java_plugin)。该`main`源集是有效的“应用程序”。

应用Application插件还隐式应用[Distribution插件](https://docs.gradle.org/6.7.1/userguide/distribution_plugin.html#distribution_plugin)。一个`main`分布创建包起来的应用程序，包括代码依赖关系和生成的启动脚本。

## [构建JVM应用程序](#构建JVM应用程序)

要使用应用程序插件，请在构建脚本中包括以下内容：

例子1.使用应用程序插件

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'application'
    }

build.gradle.kts

    
    
    plugins {
        application
    }

插件的唯一必需配置是应用程序的主类（即入口点）的规范。

例子2.配置应用程序主类

`Groovy``Kotlin`

build.gradle

    
    
    application {
        mainClass = 'org.gradle.sample.Main'
    }

build.gradle.kts

    
    
    application {
        mainClass.set("org.gradle.sample.Main")
    }

您可以通过执行`run`任务
（类型：[JavaExec](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/JavaExec.html)）
来运行应用程序。这将编译主源集，并使用指定的主类启动一个新的JVM，并将其类（以及所有运行时依赖项）作为类路径。
您可以使用`gradlerun --debug-jvm`
（请参阅[JavaExec.setDebug（boolean）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/JavaExec.html#setDebug-boolean-)）
以调试模式启动应用程序。

从Gradle 4.9开始，可以使用传递命令行参数`--args`。例如，如果要使用命令行参数启动应用程序`foo --bar`，则可以使用`gradle
run --args="foo
--bar"`（请参阅[JavaExec.setArgsString（java.lang.String）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/JavaExec.html#setArgsString-java.lang.String-)。

如果您的应用程序需要一组特定的JVM设置或系统属性，则可以配置该`applicationDefaultJvmArgs`属性。这些JVM参数将应用于`run`任务，并且还会在发行版的生成的启动脚本中予以考虑。

示例3.配置默认的JVM设置

`Groovy``Kotlin`

build.gradle

    
    
    application {
        applicationDefaultJvmArgs = ['-Dgreeting.language=en']
    }

build.gradle.kts

    
    
    application {
        applicationDefaultJvmArgs = listOf("-Dgreeting.language=en")
    }

如果您的应用程序的启动脚本应位于以外的目录中`bin`，则可以配置该`executableDir`属性。

例子4.为启动脚本配置定制目录

`Groovy``Kotlin`

build.gradle

    
    
    application {
        executableDir = 'custom_bin_dir'
    }

build.gradle.kts

    
    
    application {
        executableDir = "custom_bin_dir"
    }

## [使用Java模块系统构建应用程序](#使用Java模块系统构建应用程序)

Gradle支持[Java模块](https://www.oracle.com/corporate/features/understanding-java-9-modules.html)[插件文档](/md/Java库插件.md#Java模块系统的构建模块)的[相应部分中所述](/md/Java库插件.md#Java模块系统的构建模块)的[Java模块](https://www.oracle.com/corporate/features/understanding-java-9-modules.html)的构建。Java模块也可以运行，并且您可以使用应用程序插件来运行和打包此类模块化应用程序。为此，除了要对非模块化应用程序进行操作之外，还需要做两件事。

首先，您需要添加一个`module-
info.java`文件来描述您的应用程序模块。有关此主题的更多详细信息，请参阅[Java库插件文档](/md/Java库插件.md#Java模块系统的构建模块)。

其次，除了像这样的主类名称之外，还需要告诉Gradle您要运行的模块的名称：

例子5.配置模块化应用程序的主模块

`Groovy``Kotlin`

build.gradle

    
    
    application {
        mainModule = 'org.gradle.sample.app' // name defined in module-info.java
        mainClass = 'org.gradle.sample.Main'
    }

build.gradle.kts

    
    
    application {
        mainModule.set("org.gradle.sample.app") // name defined in module-info.java
        mainClass.set("org.gradle.sample.Main")
    }

就这样。如果运行应用程序，通过执行`run`任务或通过[生成的启动脚本](#建立发行版)，它将作为模块运行，并在运行时遵守模块边界。例如，从另一个模块反射访问内部包装可能会失败。

配置的 _主类_ 也被烘焙到`module-
info.class`应用程序Jar的文件中。如果直接使用`java`命令运行模块化应用程序，则只需提供模块名称即可。

您还可以查看一个[现成的示例](https://docs.gradle.org/6.7.1/samples/sample_java_modules_multi_project.html)，该[示例](https://docs.gradle.org/6.7.1/samples/sample_java_modules_multi_project.html)包括作为多项目一部分的模块化应用程序。

╔═════════════════════════════  

Java Module System支持是一个孵化功能，因此您需要显式打开 _模块路径推断_ ，如下所示。  
  
╚═════════════════════════════    
  
例子6.激活模块路径推断

`Groovy``Kotlin`

build.gradle

    
    
    java {
        modularity.inferModulePath = true
    }

build.gradle.kts

    
    
    java {
        modularity.inferModulePath.set(true)
    }

## [建立发行版](#建立发行版)

可以通过[Distribution插件](https://docs.gradle.org/6.7.1/userguide/distribution_plugin.html#distribution_plugin)（自动应用）创建应用程序的[发行版](https://docs.gradle.org/6.7.1/userguide/distribution_plugin.html#distribution_plugin)。一个`main`分布与以下内容创建：

表1.分发内容 

位置 | 内容  
---|---   
（根目录）|`src/dist`  
`lib`|所有运行时依赖项和主要的源集类文件。  
`bin`|启动脚本（由`startScripts`任务生成）。  
  
要添加到分发中的静态文件可以简单地添加到中`src/dist`。通过配置主发行版提供的[CopySpec，](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/CopySpec.html)可以完成更高级的自定义。

例子7.在应用程序分发中包括其他任务的输出

`Groovy``Kotlin`

build.gradle

    
    
    task createDocs {
        def docs = file("$buildDir/docs")
        outputs.dir docs
        doLast {
            docs.mkdirs()
            new File(docs, 'readme.txt').write('Read me!')
        }
    }
    
    distributions {
        main {
            contents {
                from(createDocs) {
                    into 'docs'
                }
            }
        }
    }

build.gradle.kts

    
    
    val createDocs by tasks.registering {
        val docs = file("$buildDir/docs")
        outputs.dir(docs)
        doLast {
            docs.mkdirs()
            File(docs, "readme.txt").writeText("Read me!")
        }
    }
    
    distributions {
        main {
            contents {
                from(createDocs) {
                    into("docs")
                }
            }
        }
    }

通过指定发行版应包含任务的输出文件（请参阅[关于](/md/处理任务.md#任务输入和输出)task的[更多信息](/md/处理任务.md#任务输入和输出)），Gradle知道在组装发行版之前必须先调用生成文件的任务，并将为您解决这一问题。

您可以在中运行`gradle
installDist`以创建应用程序的映像。您可以运行以创建包含发行版的ZIP，创建应用程序TAR或同时构建两者。`build/install/
_projectName_``gradle distZip``gradle distTar``gradle assemble`

### [自定义启动脚本生成](#自定义启动脚本生成)

该应用程序插件可以立即生成Unix（适用于Linux，macOS等）和Windows启动脚本。启动脚本使用定义为原始构建和运行时环境（例如`JAVA_OPTS`env
var）的一部分的指定设置启动JVM 。默认脚本模板基于用于启动Gradle本身的相同脚本，这些脚本作为Gradle发行版的一部分提供。

启动脚本是完全可定制的。有关更多详细信息和自定义示例，请参考[CreateStartScripts](https://docs.gradle.org/6.7.1/dsl/org.gradle.jvm.application.tasks.CreateStartScripts.html) 文档。

## [任务](#任务)

Application插件将以下任务添加到项目中。

`run`—
[JavaExec](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.JavaExec.html)

    

_取决于_ ：`classes`

启动应用程序。

`startScripts`—
[CreateStartScripts](https://docs.gradle.org/6.7.1/dsl/org.gradle.jvm.application.tasks.CreateStartScripts.html)

    

_取决于_ ：`jar`

创建特定于OS的脚本以将项目作为JVM应用程序运行。

`installDist`—[同步](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.Sync.html)

    

_取决于_ ：`jar`，`startScripts`

将应用程序安装到指定目录。

`distZip`—[邮编](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.bundling.Zip.html)

    

_取决于_ ：`jar`，`startScripts`

创建完整的分发ZIP存档，包括运行时库和特定于操作系统的脚本。

`distTar`—[焦油](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.bundling.Tar.html)

    

_取决于_ ：`jar`，`startScripts`

创建完整的发行版TAR归档文件，包括运行时库和特定于OS的脚本。

## [应用扩展](#应用扩展)

应用程序插件将扩展添加到项目中，您可以使用它来配置其行为。有关扩展中可用属性的更多信息，请参见[JavaApplication](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.plugins.JavaApplication.html)
DSL文档。

您可以通过`application {}`前面显示的块来配置扩展，例如，在构建脚本中使用以下内容：

`Groovy``Kotlin`

build.gradle

    
    
    application {
        executableDir = 'custom_bin_dir'
    }

build.gradle.kts

    
    
    application {
        executableDir = "custom_bin_dir"
    }

## [发牌](#发牌)

与您的应用程序捆绑在一起的Gradle启动脚本已获得[Apache 2.0软件许可的许可](https://www.apache.org/licenses/LICENSE-2.0) 。这不会影响您的应用程序，您可以选择许可该应用程序。

## [约定属性（已弃用）](#约定属性（已弃用）)

该插件还向项目添加了一些约定属性，您可以使用它们来配置其行为。这些 **已** 被上述扩展 **弃用**
并被其取代。有关它们的信息，请参阅[Project](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#N14FED)
DSL文档。

与扩展属性不同，这些属性在构建脚本中显示为顶级项目属性。例如，要更改应用程序名称，您可以将以下内容添加到构建脚本中：

`Groovy``Kotlin`

build.gradle

    
    
    applicationName = 'my-app'

build.gradle.kts

    
    
    application.applicationName = "my-app"

