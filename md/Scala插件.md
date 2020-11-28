# Scala插件


内容

  * [用法](#sec:scala_usage)
  * [任务](#sec:scala_tasks)
  * [项目布局](#sec:scala_project_layout)
  * [依赖管理](#sec:scala_dependency_management)
  * [自动配置scalaClasspath](#sec:configure_scala_classpath)
  * [配置Zinc编译器](#sec:configure_zinc_compiler)
  * [将插件添加到Scala编译器](#sec:scala_compiler_plugins)
  * [公约属性](#sec:scala_convention_properties)
  * [源集属性](#sec:scala_source_set_properties)
  * [在外部过程中进行编译](#sec:scala_compiling_in_external_process)
  * [增量编译](#sec:scala_incremental_compilation)
  * [针对Java 6或Java 7进行编译和测试](#sec:scala_cross_compilation)
  * [Eclipse整合](#sec:eclipse_integration)
  * [IntelliJ IDEA集成](#sec:intellij_idea_integration)

Scala插件扩展了[Java插件，](https://docs.gradle.org/6.7.1/userguide/java_plugin.html)以添加对[Scala](https://www.scala-
lang.org/)项目的支持。它可以处理Scala代码，Scala和Java混合代码，甚至是纯Java代码（尽管我们不一定建议将其用于后者）。该插件支持
_联合编译_
，可让您自由地混合和匹配Scala和Java代码，并具有双向依赖性。例如，Scala类可以扩展Java类，而Java类又可以扩展Scala类。这样就可以为作业使用最佳语言，并在需要时用其他语言重写任何类。

请注意，如果您希望从[API/实现分离中](https://docs.gradle.org/6.7.1/userguide/java_library_plugin.html#sec:java_library_separation)受益，还可以将`java-
library`插件应用于Scala项目。

## [](#sec:scala_usage)[用法](#sec:scala_usage)

要使用Scala插件，请在构建脚本中包含以下内容：

例子1.使用Scala插件

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'scala'
    }

build.gradle.kts

    
    
    plugins {
        scala
    }

## [](#sec:scala_tasks)[任务](#sec:scala_tasks)

Scala插件将以下任务添加到项目中。在[此处](https://docs.gradle.org/6.7.1/userguide/building_java_projects.html#sec:building_jvm_lang)可以找到有关更改对Java编译任务的依赖性的信息。

`compileScala`—
[ScalaCompile](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.scala.ScalaCompile.html)

    

_取决于_ ：`compileJava`

编译生产Scala源文件。

`compileTestScala`—
[ScalaCompile](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.scala.ScalaCompile.html)

    

_取决于_ ：`compileTestJava`

编译测试Scala源文件。

`compile _SourceSet_ Scala`—
[ScalaCompile](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.scala.ScalaCompile.html)

    

_取决于_ ：`compile _SourceSet_ Java`

编译给定源集的Scala源文件。

`scaladoc`—
[ScalaDoc](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.scala.ScalaDoc.html)

    

为生产Scala源文件生成API文档。

Scala插件将以下依赖项添加到Java插件添加的任务中。

表1. Scala插件-其他任务依赖性 任务名称 | 依赖于取决于  
---|---   
`classes`|`compileScala`  
`testClasses`|`compileTestScala`  
`_sourceSet_ Classes`|`compile _SourceSet_ Scala`  
  
![scalaPluginTasks](img/scalaPluginTasks.png)

图1. Scala插件-任务

## [](#sec:scala_project_layout)[项目布局](#sec:scala_project_layout)

Scala插件采用如下所示的项目布局。所有的Scala源目录都可以包含Scala _和_
Java代码。Java源目录只能包含Java源代码。这些目录都不需要存在或包含任何内容。Scala插件将简单地编译找到的任何内容。

`src/main/java`

    

生产Java源代码。

`src/main/resources`

    

生产资源，例如XML和属性文件。

`src/main/scala`

    

生产Scala源。也可能包含用于联合编译的Java源文件。

`src/test/java`

    

测试Java源代码。

`src/test/resources`

    

测试资源。

`src/test/scala`

    

测试Scala源。也可能包含用于联合编译的Java源文件。

`src/ _sourceSet_ /java`

    

名为 _sourceSet_ 的源集的Java源。

`src/ _sourceSet_ /resources`

    

名为 _sourceSet_ 的源集的资源。

`src/ _sourceSet_ /scala`

    

给定源集的Scala源文件。也可能包含用于联合编译的Java源文件。

### [](#sec:changing_scala_project_layout)[更改项目布局](#sec:changing_scala_project_layout)

就像Java插件一样，Scala插件允许您配置Scala生产和测试源文件的自定义位置。

例子2.自定义Scala源布局

`Groovy``Kotlin`

build.gradle

    
    
    sourceSets {
        main {
            scala {
                srcDirs = ['src/scala']
            }
        }
        test {
            scala {
                srcDirs = ['test/scala']
            }
        }
    }

build.gradle.kts

    
    
    sourceSets {
        main {
            withConvention(ScalaSourceSet::class) {
                scala {
                    setSrcDirs(listOf("src/scala"))
                }
            }
        }
        test {
            withConvention(ScalaSourceSet::class) {
                scala {
                    setSrcDirs(listOf("test/scala"))
                }
            }
        }
    }

## [](#sec:scala_dependency_management)[依赖管理](#sec:scala_dependency_management)

Scala项目需要声明一个`scala-
library`依赖项。然后将在编译和运行时类路径上使用此依赖项。它还将分别用于获取Scala编译器和Scaladoc工具。[[1](#_footnotedef_1"查看脚注。") ]

如果将Scala用于生产代码，`scala-library`则应将依赖项添加到`compile`配置中：

例子3.声明生产代码的Scala依赖

`Groovy``Kotlin`

build.gradle

    
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        implementation 'org.scala-lang:scala-library:2.11.12'
        testImplementation 'org.scalatest:scalatest_2.11:3.0.0'
        testImplementation 'junit:junit:4.13'
    }

build.gradle.kts

    
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        implementation("org.scala-lang:scala-library:2.11.12")
        testImplementation("org.scalatest:scalatest_2.11:3.0.0")
        testImplementation("junit:junit:4.13")
    }

如果Scala仅用于测试代码，`scala-library`则应将依赖项添加到`testCompile`配置中：

例子4.声明测试代码的Scala依赖

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        testImplementation 'org.scala-lang:scala-library:2.11.1'
    }

build.gradle.kts

    
    
    dependencies {
        testImplementation("org.scala-lang:scala-library:2.11.1")
    }

## [](#sec:configure_scala_classpath)[自动配置scalaClasspath](#sec:configure_scala_classpath)

在`ScalaCompile`和`ScalaDoc`任务消耗两个方面Scala代码：对他们`classpath`，以及他们`scalaClasspath`。前者用于查找源代码引用的类，通常将`scala-
library`与其他库一起包含。后者分别用于加载和执行Scala编译器和Scaladoc工具，并且应仅包含`scala-compiler`库及其依赖项。

除非`scalaClasspath`明确配置了任务的，否则Scala（基本）插件将尝试从任务的中推断出它`classpath`。这样做如下：

  * 如果在`scala-library`上找到了jar `classpath`，并且项目中至少声明了一个存储库，`scala-compiler`则将向中添加相应的存储库依赖项`scalaClasspath`。

  * 否则，任务的执行将失败，并显示一条消息，提示`scalaClasspath`无法推断。

## [](#sec:configure_zinc_compiler)[配置Zinc编译器](#sec:configure_zinc_compiler)

Scala插件使用名为的配置`zinc`来解析[Zinc编译器](https://github.com/typesafehub/zinc)及其依赖项。Gradle将提供Zinc的默认版本，但是如果您需要使用特定的Zinc版本，则可以对其进行更改。Gradle支持Zinc及更高版本的1.2.0。

例子5.声明要使用的Zinc编译器版本

`Groovy``Kotlin`

build.gradle

    
    
    scala {
        zincVersion = "1.2.1"
    }

build.gradle.kts

    
    
    scala {
        zincVersion.set("1.2.1")
    }

Zinc编译器本身需要兼容的版本，`scala-library`该版本可能与您的应用程序所需的版本不同。Gradle会`scala-
library`为您指定兼容版本。 [[2](#_footnotedef_2"查看脚注。") ]

可以诊断出通过运行选择的锌编译器的版本问题[dependencyInsight](https://docs.gradle.org/6.7.1/userguide/viewing_debugging_dependencies.html)的`zinc`配置。

表2.锌兼容性表 

Gradle版本 | 支持的锌版本 | 锌座标 | 所需的Scala版本 | 支持的Scala编译版本  
---|---  |---|---  |---  
6.0及更高版本|[SBT锌](https://github.com/sbt/zinc)。1.2.0及更高版本。|`org.scala-sbt:zinc_2.12`|_运行_ Zinc`2.12.x`需要Scala 。 __|`2.10.x`通过Scala`2.13.x`可以编译。  
1.x至5.x|[**不推荐使用的** TypesafeZinc编译器。](https://github.com/typesafehub/zinc)版本0.3.0及更高版本，但0.3.2至0.3.5.2除外。|`com.typesafe.zinc:zinc`|_运行_ Zinc`2.10.x`需要Scala 。 __|`2.9.x`通过Scala`2.12.x`可以编译。  
  
## [](#sec:scala_compiler_plugins)[将插件添加到Scala编译器](#sec:scala_compiler_plugins)

Scala插件添加了一个名为的配置`scalaCompilerPlugins`，该配置用于声明和解析可选的编译器插件。

示例6.在Scala编译器插件上添加依赖项

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        implementation "org.scala-lang:scala-library:2.13.1"
        scalaCompilerPlugins "org.typelevel:kind-projector_2.13.1:0.11.0"
    }

build.gradle.kts

    
    
    dependencies {
        implementation("org.scala-lang:scala-library:2.13.1")
        scalaCompilerPlugins("org.typelevel:kind-projector_2.13.1:0.11.0")
    }

## [](#sec:scala_convention_properties)[公约属性](#sec:scala_convention_properties)

Scala插件不会向项目添加任何约定属性。

## [](#sec:scala_source_set_properties)[源集属性](#sec:scala_source_set_properties)

Scala插件将以下约定属性添加到项目中的每个源集。您可以在构建脚本中使用这些属性，就像它们是源集对象的属性一样。

`scala`—
[SourceDirectorySet](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.file.SourceDirectorySet.html)（只读）

    

此源集的Scala源文件。包含在Scala源目录中找到的所有`.scala`和`.java`文件，并且排除所有其他类型的文件。 _默认值：_ 非空。

`scala.srcDirs` \- `Set<File>`

    

包含此源集的Scala源文件的源目录。也可能包含用于联合编译的Java源文件。可以使用“[了解隐式转换为文件集合”中](https://docs.gradle.org/6.7.1/userguide/working_with_files.html#sec:specifying_multiple_files)所述的任何内容进行设置。
_默认值：_ 。`[ _projectDir_ /src/ _name_ /scala]`

`allScala`—
[FileTree](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/FileTree.html)（只读）

    

该源集的所有Scala源文件。仅包含`.scala`在Scala源目录中找到的文件。 _默认值：_ 非空。

这些约定属性由类型为[ScalaSourceSet](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.ScalaSourceSet.html)的约定对象提供。

Scala插件还修改了一些源集属性：

表3. Scala插件-源集属性

 物业名称 | 更改  
---|---    
`allJava`|添加`.java`在Scala源目录中找到的所有文件。  
`allSource`|添加在Scala源目录中找到的所有源文件。  
  
## [](#sec:scala_compiling_in_external_process)[在外部过程中进行编译](#sec:scala_compiling_in_external_process)

Scala编译在外部过程中进行。

外部进程的内存设置默认为JVM的默认值。要调整内存设置，请`scalaCompileOptions.forkOptions`根据需要配置属性：

示例7.调整内存设置

`Groovy``Kotlin`

build.gradle

    
    
    tasks.withType(ScalaCompile) {
        scalaCompileOptions.forkOptions.with {
            memoryMaximumSize = '1g'
            jvmArgs = ['-XX:MaxPermSize=512m']
        }
    }

build.gradle.kts

    
    
    tasks.withType<ScalaCompile>().configureEach {
        scalaCompileOptions.forkOptions.apply {
            memoryMaximumSize = "1g"
            jvmArgs = listOf("-XX:MaxPermSize=512m")
        }
    }

## [](#sec:scala_incremental_compilation)[增量编译](#sec:scala_incremental_compilation)

通过仅编译自上次编译以来其源代码已更改的类以及受这些更改影响的类，增量编译可以显着减少Scala编译时间。如开发时经常这样做，当频繁编译较小的代码增量时，它特别有效。

通过与[Zinc](https://github.com/typesafehub/zinc)集成，Scala插件默认为增量编译，它是[sbt](https://github.com/harrah/xsbt)增量Scala编译器的独立版本。如果要禁用增量编译，请`force
= true`在构建文件中进行设置：

例子8.强制所有代码被编译

`Groovy``Kotlin`

build.gradle

    
    
    tasks.withType(ScalaCompile) {
        scalaCompileOptions.with {
            force = true
        }
    }

build.gradle.kts

    
    
    tasks.withType<ScalaCompile>().configureEach {
        scalaCompileOptions.apply {
            isForce = true
        }
    }

_注意：_ 仅当至少一个输入源文件已更改时，这才会导致重新编译所有类。如果源文件没有任何更改，`compileScala`则仍将`UP-TO-
DATE`照常执行该任务。

基于Zinc的Scala编译器支持Java和Scala代码的联合编译。默认情况下，所有Java和Scala代码`src/main/scala`都将参与联合编译。甚至Java代码也将以增量方式编译。

增量编译需要对源代码进行依赖性分析。分析结果存储在指定的文件中`scalaCompileOptions.incrementalOptions.analysisFile`（该文件具有明智的默认值）。在多项目构建中，分析文件将传递到下游`ScalaCompile`任务，以实现跨项目边界的增量编译。对于`ScalaCompile`Scala插件添加的任务，无需进行配置即可完成此工作。对于`ScalaCompile`您可能添加的其他任务，`scalaCompileOptions.incrementalOptions.publishedCode`需要将该属性配置为指向classes文件夹或Jar存档，通过该文件夹传递代码以编译下游`ScalaCompile`任务的类路径。请注意，如果`publishedCode`设置不正确，下游任务可能不会重新编译受上游更改影响的代码，从而导致错误的编译结果。

请注意，不支持Zinc基于Nailgun的守护程序模式。取而代之的是，我们计划增强Gradle自己的编译器守护进程，以在Gradle调用中保持活动状态，并重用相同的Scala编译器。预计这将为Scala编译带来另一个显着的加速。

## [](#sec:scala_cross_compilation)[针对Java 6或Java 7进行编译和测试](#sec:scala_cross_compilation)

Scala编译器会忽略Gradle的`targetCompatibility`和`sourceCompatibility`设置。在Scala
2.11中，Scala编译器始终将其编译为与Java 6兼容的字节码。在Scala 2.12中，Scala编译器始终将其编译为Java
8兼容的字节码。如果您也有Java源代码，则可以按照与[Java插件](https://docs.gradle.org/6.7.1/userguide/building_java_projects.html#sec:java_cross_compilation)相同的步骤进行操作，以确保使用正确的Java编译器。

gradle.properties

    
    
    # in $HOME/.gradle/gradle.properties
    java6Home=/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home

`Groovy``Kotlin`

build.gradle

    
    
    java {
        sourceCompatibility = JavaVersion.VERSION_1_6
    }
    
    assert hasProperty('java6Home') : "Set the property 'java6Home' in your your gradle.properties pointing to a Java 6 installation"
    def javaExecutablesPath = new File(java6Home, 'bin')
    def javaExecutables = [:].withDefault { execName ->
        def executable = new File(javaExecutablesPath, execName)
        assert executable.exists() : "There is no ${execName} executable in ${javaExecutablesPath}"
        executable
    }
    
    tasks.withType(AbstractCompile) {
        options.with {
            fork = true
            forkOptions.javaHome = file(java6Home)
        }
    }
    tasks.withType(Test) {
        executable = javaExecutables.java
    }
    tasks.withType(JavaExec) {
        executable = javaExecutables.java
    }
    tasks.withType(Javadoc) {
        executable = javaExecutables.javadoc
    }

build.gradle.kts

    
    
    java {
        sourceCompatibility = JavaVersion.VERSION_1_6
    }
    
    require(hasProperty("java6Home")) { "Set the property 'java6Home' in your your gradle.properties pointing to a Java 6 installation" }
    val java6Home: String by project
    val javaExecutablesPath = File(java6Home, "bin")
    fun javaExecutable(execName: String): String {
        val executable = File(javaExecutablesPath, execName)
        require(executable.exists()) { "There is no ${execName} executable in ${javaExecutablesPath}" }
        return executable.toString()
    }
    
    tasks.withType<ScalaCompile>().configureEach {
        options.apply {
            isFork = true
            forkOptions.javaHome = file(java6Home)
        }
    }
    tasks.withType<Test>().configureEach {
        executable = javaExecutable("java")
    }
    tasks.withType<JavaExec>().configureEach {
        executable = javaExecutable("java")
    }
    tasks.withType<Javadoc>().configureEach {
        executable = javaExecutable("javadoc")
    }

## [](#sec:eclipse_integration)[Eclipse整合](#sec:eclipse_integration)

当Eclipse插件遇到Scala项目时，它会添加其他配置，以使该项目可以立即使用Scala IDE。具体来说，该插件添加了Scala性质和依赖项容器。

## [](#sec:intellij_idea_integration)[IntelliJ IDEA集成](#sec:intellij_idea_integration)

当IDEA插件遇到Scala项目时，它会添加其他配置，以使该项目开箱即可使用IDEA。具体来说，该插件添加了一个Scala SDK（IntelliJ
IDEA 14+）和一个Scala编译器库，该库与项目的类路径上的Scala版本匹配。Scala插件与IntelliJ
IDEA的早期版本向后兼容，并且可以通过`targetVersion`在[IdeaModel](https://docs.gradle.org/6.7.1/dsl/org.gradle.plugins.ide.idea.model.IdeaModel.html)上进行配置来添加Scala构面而不是默认的Scala
SDK 。

示例9.明确指定目标IntelliJ IDEA版本

`Groovy``Kotlin`

build.gradle

    
    
    idea {
        targetVersion = '13'
    }

build.gradle.kts

    
    
    idea {
        targetVersion = "13"
    }

* * *

[1](#_footnoteref_1)。请参阅[自动配置Scala类路径](#sec:configure_scala_classpath)。

[2](#_footnoteref_2)。Gradle不支持在Scala
2.11中运行Zinc编译器v1.2.0。

