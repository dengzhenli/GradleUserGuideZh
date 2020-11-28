# Groovy插件


内容

  * [用法](#sec:groovy_usage)
  * [任务](#sec:groovy_tasks)
  * [项目布局](#sec:groovy_project_layout)
  * [依赖管理](#sec:groovy_dependency_management)
  * [groovyClasspath的自动配置](#sec:automatic_configuration_of_groovyclasspath)
  * [公约属性](#sec:groovy_convention_properties)
  * [源集属性](#sec:groovy_source_set_properties)
  * [Groovy编译](#sec:groovyCompile)
  * [Groovy增量编译](#sec:incremental_groovy_compilation)
  * [针对Java 6或Java 7进行编译和测试](#sec:groovy_cross_compilation)

Groovy插件扩展了[Java插件，](https://docs.gradle.org/6.7.1/userguide/java_plugin.html)以添加对[Groovy](https://groovy-lang.org/)项目的支持。它可以处理Groovy代码，混合的Groovy和Java代码，甚至是纯Java代码（尽管我们不一定建议将其用于后者）。该插件支持
_联合编译_
，可让您自由混合并匹配Groovy和Java代码，并具有双向依赖性。例如，Groovy类可以扩展Java类，而Java类又可以扩展Groovy类。这样就可以为作业使用最佳语言，并在需要时用其他语言重写任何类。

请注意，如果您希望从[API/实现分离中](https://docs.gradle.org/6.7.1/userguide/java_library_plugin.html#sec:java_library_separation)受益，则还可以将`java-
library`插件应用于Groovy项目。

## [](#sec:groovy_usage)[用法](#sec:groovy_usage)

要使用Groovy插件，请在构建脚本中包含以下内容：

例子1.使用Groovy插件

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'groovy'
    }

build.gradle.kts

    
    
    plugins {
        groovy
    }

## [](#sec:groovy_tasks)[任务](#sec:groovy_tasks)

Groovy插件将以下任务添加到项目中。在[此处](https://docs.gradle.org/6.7.1/userguide/building_java_projects.html#sec:building_jvm_lang)可以找到有关更改对Java编译任务的依赖性的信息。

`compileGroovy`—
[GroovyCompile](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.compile.GroovyCompile.html)

    

_取决于_ ：`compileJava`

编译生产Groovy源文件。

`compileTestGroovy`—
[GroovyCompile](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.compile.GroovyCompile.html)

    

_取决于_ ：`compileTestJava`

编译测试Groovy源文件。

`compile _SourceSet_ Groovy`—
[GroovyCompile](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.compile.GroovyCompile.html)

    

_取决于_ ：`compile _SourceSet_ Java`

编译给定源集的Groovy源文件。

`groovydoc`—
[Groovydoc](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.javadoc.Groovydoc.html)

    

为生产Groovy源文件生成API文档。

Groovy插件将以下依赖项添加到Java插件添加的任务中。

表1. Groovy插件-其他任务依赖性 

任务名称 | 依赖于取决于  
---|---  
`classes`|`compileGroovy`  
`testClasses`|`compileTestGroovy`  
`_sourceSet_ Classes`|`compile _SourceSet_ Groovy`  
  
![groovyPluginTasks](img/groovyPluginTasks.png)

图1. Groovy插件-任务

## [](#sec:groovy_project_layout)[项目布局](#sec:groovy_project_layout)

Groovy插件采用了[Groovy Layout中](#groovylayout)所示的项目[布局](#groovylayout)。所有Groovy源目录都可以包含Groovy
_和_ Java代码。Java源目录只能包含Java源代码。[[1](#_footnotedef_1"查看脚注。") ]这些目录都不需要存在或其中没有任何内容；Groovy插件将简单地编译找到的任何内容。

`src/main/java`

    

生产Java源代码。

`src/main/resources`

    

生产资源，例如XML和属性文件。

`src/main/groovy`

    

生产Groovy源。也可能包含用于联合编译的Java源文件。

`src/test/java`

    

测试Java源代码。

`src/test/resources`

    

测试资源。

`src/test/groovy`

    

测试Groovy源。也可能包含用于联合编译的Java源文件。

`src/ _sourceSet_ /java`

    

名为 _sourceSet_ 的源集的Java源。

`src/ _sourceSet_ /resources`

    

名为 _sourceSet_ 的源集的资源。

`src/ _sourceSet_ /groovy`

    

给定源集的Groovy源文件。也可能包含用于联合编译的Java源文件。

### [](#sec:changing_groovy_project_layout)[更改项目布局](#sec:changing_groovy_project_layout)

就像Java插件一样，Groovy插件允许您配置Groovy生产和测试源文件的自定义位置。

示例2.自定义Groovy源布局

`Groovy``Kotlin`

build.gradle

    
    
    sourceSets {
        main {
            groovy {
                srcDirs = ['src/groovy']
            }
        }
    
        test {
            groovy {
                srcDirs = ['test/groovy']
            }
        }
    }

build.gradle.kts

    
    
    sourceSets {
        main {
            withConvention(GroovySourceSet::class) {
                groovy {
                    setSrcDirs(listOf("src/groovy"))
                }
            }
        }
    
        test {
            withConvention(GroovySourceSet::class) {
                groovy {
                    setSrcDirs(listOf("test/groovy"))
                }
            }
        }
    }

## [](#sec:groovy_dependency_management)[依赖管理](#sec:groovy_dependency_management)

因为Gradle的构建语言是基于Groovy的，并且Gradle的某些部分是在Groovy中实现的，所以Gradle已经附带了Groovy库。尽管如此，Groovy项目仍需要显式声明一个Groovy依赖项。然后将在编译和运行时类路径上使用此依赖项。它还将分别用于掌握Groovy编译器和Groovydoc工具。

如果将Groovy用于生产代码，则应将Groovy依赖项添加到`implementation`配置中：

例子3. Groovy依赖的配置

`Groovy``Kotlin`

build.gradle

    
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        implementation 'org.codehaus.groovy:groovy-all:2.4.15'
    }

build.gradle.kts

    
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        implementation("org.codehaus.groovy:groovy-all:2.4.15")
    }

如果Groovy仅用于测试代码，则应将Groovy依赖项添加到`testImplementation`配置中：

示例4. Groovy测试依赖项的配置

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        testImplementation 'org.codehaus.groovy:groovy-all:2.4.15'
    }

build.gradle.kts

    
    
    dependencies {
        testImplementation("org.codehaus.groovy:groovy-all:2.4.15")
    }

要使用Gradle附带的Groovy库，请声明一个`localGroovy()`依赖项。请注意，不同的Gradle版本附带了不同的Groovy版本。因此，使用`localGroovy()`安全性要比声明常规Groovy依赖项安全。

例子5.捆绑的Groovy依赖项的配置

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        implementation localGroovy()
    }

build.gradle.kts

    
    
    dependencies {
        implementation(localGroovy())
    }

Groovy库不一定必须来自远程存储库。它也可能来自本地`lib`目录，可能已签到源代码管理：

示例6. Groovy文件依赖项的配置

`Groovy``Kotlin`

build.gradle

    
    
    repositories {
        flatDir { dirs 'lib' }
    }
    
    dependencies {
        implementation module('org.codehaus.groovy:groovy:2.4.15') {
            dependency('org.ow2.asm:asm-all:5.0.3')
            dependency('antlr:antlr:2.7.7')
            dependency('commons-cli:commons-cli:1.2')
            module('org.apache.ant:ant:1.9.4') {
                dependencies('org.apache.ant:ant-junit:1.9.4@jar',
                             'org.apache.ant:ant-launcher:1.9.4')
            }
        }
    }

build.gradle.kts

    
    
    repositories {
        flatDir { dirs("lib") }
    }
    
    dependencies {
        implementation(module("org.codehaus.groovy:groovy:2.4.15") {
            dependency("org.ow2.asm:asm-all:5.0.3")
            dependency("antlr:antlr:2.7.7")
            dependency("commons-cli:commons-cli:1.2")
            module("org.apache.ant:ant:1.9.4") {
                dependencies("org.apache.ant:ant-junit:1.9.4@jar",
                             "org.apache.ant:ant-launcher:1.9.4")
            }
        })
    }

## [](#sec:automatic_configuration_of_groovyclasspath)[groovyClasspath的自动配置](#sec:automatic_configuration_of_groovyclasspath)

在`GroovyCompile`和`Groovydoc`任务消耗两个方面Groovy代码：对他们`classpath`，以及他们`groovyClasspath`。前者用于查找源代码引用的类，通常将包含Groovy库以及其他库。后者分别用于加载和执行Groovy编译器和Groovydoc工具，并且应仅包含Groovy库及其依赖项。

除非`groovyClasspath`明确配置了任务的，否则Groovy（基本）插件将尝试从任务的推断出它`classpath`。这样做如下：

  * 如果在`groovy-all(-indy)`上找到了一个Jar `classpath`，则该jar将添加到中`groovyClasspath`。

  * 如果在`groovy(-indy)`上找到了jar `classpath`，并且项目中至少声明了一个存储库，`groovy(-indy)`则将向中添加相应的存储库依赖项`groovyClasspath`。

  * 否则，任务的执行将失败，并显示一条消息，提示`groovyClasspath`无法推断。

请注意，`-indy`每个jar的“ ”变体均指带有`invokedynamic`支持的版本。

## [](#sec:groovy_convention_properties)[公约属性](#sec:groovy_convention_properties)

Groovy插件不会向项目添加任何约定属性。

## [](#sec:groovy_source_set_properties)[源集属性](#sec:groovy_source_set_properties)

Groovy插件将以下约定属性添加到项目中的每个源集。您可以在构建脚本中使用这些属性，就像它们是源集对象的属性一样。

### [](#groovy_plugin_source_set_properties)[Groovy插件—源集属性](#groovy_plugin_source_set_properties)

`groovy`—
[SourceDirectorySet](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.file.SourceDirectorySet.html)（只读）

    

_默认值_ ：不为空

此源集的Groovy源文件。包含在Groovy源目录中找到的所有`.groovy`和`.java`文件，并且排除所有其他类型的文件。

`groovy.srcDirs` \- `Set<File>`

    

_预设值_ ：`[ _projectDir_ /src/ _name_ /groovy]`

包含此源集的Groovy源文件的源目录。也可能包含用于联合编译的Java源文件。可以使用“[指定多个文件”中](https://docs.gradle.org/6.7.1/userguide/working_with_files.html#sec:specifying_multiple_files)所述的任何内容进行设置。

`allGroovy`—
[FileTree](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/FileTree.html)（只读）

    

_默认值_ ：不为空

此源集的所有Groovy源文件。仅包含`.groovy`在Groovy源目录中找到的文件。

这些属性由[GroovySourceSet](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.GroovySourceSet.html)类型的约定对象提供。

Groovy插件还修改了一些源集属性：

### [](#groovy_plugin_modified_source_set_properties)[Groovy插件-修改的源集属性](#groovy_plugin_modified_source_set_properties)

物业名称 | 更改  
---|---   
`allJava`|添加`.java`在Groovy源目录中找到的所有文件。  
`allSource`|添加在Groovy源目录中找到的所有源文件。  
  
## [](#sec:groovyCompile)[Groovy编译](#sec:groovyCompile)

Groovy插件为项目中的每个源集添加了一个[GroovyCompile](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.compile.GroovyCompile.html)任务。任务类型扩展了`JavaCompile`任务（请参阅[相关的Java插件部分](https://docs.gradle.org/6.7.1/userguide/building_java_projects.html#sec:compile)）。该`GroovyCompile`任务支持官方Groovy编译器的大多数配置选项。

表2. Groovy插件-GroovyCompile属性 

任务属性 | 类型 | 默认值  
---|---|---  
`classpath`|[文件集](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/FileCollection.html)|`_sourceSet_.compileClasspath`  
`source`|[FileTree](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/FileTree.html)。可以使用“[指定多个文件”中](https://docs.gradle.org/6.7.1/userguide/working_with_files.html#sec:specifying_multiple_files)所述的任何内容进行设置。|`_sourceSet_.groovy`  
`destinationDir`|`File`。|`_sourceSet_.groovy.outputDir`  
`groovyClasspath`|[文件集](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/FileCollection.html)|`groovy`非空配置；在`classpath`其他地方找到Groovy库  
  
### [](#sec:groovy_compilation_avoidance)[避免编译](#sec:groovy_compilation_avoidance)

_注意：自Gradle 5.6起，避免Groovy编译是一个令人振奋的功能。 存在已知的错误，因此请自行承担风险。_

要启用避免Groovy编译的孵化支持，请[`enableFeaturePreview`](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/initialization/Settings.html#enableFeaturePreview-java.lang.String-)在您的设置文件中添加：

`Groovy``Kotlin`

settings.gradle

    enableFeaturePreview('GROOVY_COMPILATION_AVOIDANCE')

settings.gradle.kts

    
    
    enableFeaturePreview("GROOVY_COMPILATION_AVOIDANCE")

如果从属项目以与[ABI](https://en.wikipedia.org/wiki/Application_binary_interface)兼容的方式进行了更改（仅更改了其专用API），那么Groovy编译任务将是最新的。这意味着，如果project`A`依赖于project`B`并且inclass中的类以`B`ABI兼容的方式更改（通常仅更改方法的主体），则Gradle将不会重新编译`A`。

有关不影响ABI且被忽略的更改类型的详细列表，请参见[Java避免编译](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#sec:java_compile_avoidance)。

但是，类似于Java的注释处理，有多种方法可以[定制Groovy编译过程](https://melix.github.io/blog/2011/05/12/customizing_groovy_compilation_process.html)，而实现细节很重要。一些著名的例子是[GroovyAST转换](https://groovy-lang.org/metaprogramming.html#_code_generation_transformations)。在这些情况下，必须在称为的类路径中分别声明这些依赖项`astTransformationClasspath`：

例子7.声明AST转换

`Groovy``Kotlin`

build.gradle

    
    
    configurations { astTransformation }
    dependencies {
        astTransformation(project(":ast-transformation"))
    }
    tasks.withType(GroovyCompile).configureEach {
        astTransformationClasspath.from(configurations.astTransformation)
    }

build.gradle.kts

    
    
    val astTransformation by configurations.creating
    dependencies {
        astTransformation(project(":ast-transformation"))
    }
    tasks.withType<GroovyCompile>().configureEach {
        astTransformationClasspath.from(astTransformation)
    }

## [](#sec:incremental_groovy_compilation)[Groovy增量编译](#sec:incremental_groovy_compilation)

从5.6开始，Gradle引入了实验性的Groovy增量编译器。要为Groovy启用增量编译，您需要：

  * 启用[Groovy避免编译](#sec:groovy_compilation_avoidance)。

  * 在构建脚本中明确启用增量Groovy编译：

示例8.启用增量Groovy编译

`Groovy``Kotlin`

buildSrc / src / main / groovy / myproject.groovy-conventions.gradle

    
    
    tasks.withType(GroovyCompile).configureEach {
        options.incremental = true
    }

buildSrc/src/main/kotlin/myproject.groovy-conventions.gradle.kts

    
    
    tasks.withType<GroovyCompile>().configureEach {
        options.isIncremental = true
    }

这为您带来以下好处：

  * 增量构建要快得多。

  * 如果只更改了一小部分Groovy源文件，则仅重新编译受影响的源文件。不需要重新编译的类在输出目录中保持不变。例如，如果仅更改几个Groovy测试类，则无需重新编译所有Groovy测试源文件-仅需要重新编译更改的文件。

要了解增量编译的工作原理，请参见[增量Java编译](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#sec:incremental_compile)以获取详细概述。请注意，与Java增量编译有一些区别：

  * 与Java不同，Groovy编译器不内联常量，因此对常量的更改不会触发完全重新编译。

  * Groovy编译器不保留`@Retention`生成的注释类字节码（[GROOVY-9185](https://issues.apache.org/jira/browse/GROOVY-9185)），因此所有注释均为`RUNTIME`。这意味着对源保留批注的更改将不会触发完全重新编译。

### [](#sec:incremental_groovy_compilation_known_issues)[已知的问题](#sec:incremental_groovy_compilation_known_issues)

另请参阅[增量Java编译的已知问题](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#sec:incremental_compilation_known_issues)。

  * 对资源的更改不会触发重新编译，这可能会导致某些不正确的情况，例如[扩展模块](https://mrhaki.blogspot.com/2013/01/groovy-goodness-adding-extra-methods.html)。

## [](#sec:groovy_cross_compilation)[针对Java 6或Java 7进行编译和测试](#sec:groovy_cross_compilation)

Groovy编译器将始终与用于启动Gradle的Java版本一起执行。您应该将`sourceCompatibility`和设置`targetCompatibility`为`1.6`或`1.7`。如果您还具有Java源文件，则可以按照与[Java插件](https://docs.gradle.org/6.7.1/userguide/building_java_projects.html#sec:java_cross_compilation)相同的步骤进行操作，以确保使用正确的Java编译器。

### [](#example_configure_java_6_build_for_groovy)[示例：为Groovy配置Java 6构建](#example_configure_java_6_build_for_groovy)

gradle.properties

    
    
    # in $HOME/.gradle/gradle.properties
    java6Home=/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home

`Groovy``Kotlin`

build.gradle

    
    
    java {
        sourceCompatibility = JavaVersion.VERSION_1_6
        targetCompatibility = JavaVersion.VERSION_1_6
    }
    
    assert hasProperty('java6Home') : "Set the property 'java6Home' in your your gradle.properties pointing to a Java 6 installation"
    def javaExecutablesPath = new File(java6Home, 'bin')
    def javaExecutables = [:].withDefault { execName ->
        def executable = new File(javaExecutablesPath, execName)
        assert executable.exists() : "There is no $execName executable in $javaExecutablesPath"
        executable
    }
    tasks.withType(AbstractCompile) {
        options.with {
            fork = true
            forkOptions.javaHome = file(java6Home)
        }
    }
    tasks.withType(Javadoc) {
        executable = javaExecutables.javadoc
    }
    tasks.withType(Test) {
        executable = javaExecutables.java
    }
    tasks.withType(JavaExec) {
        executable = javaExecutables.java
    }

build.gradle.kts

    
    
    java {
        sourceCompatibility = JavaVersion.VERSION_1_6
        targetCompatibility = JavaVersion.VERSION_1_6
    }
    
    require(hasProperty("java6Home")) { "Set the property 'java6Home' in your your gradle.properties pointing to a Java 6 installation" }
    val java6Home: String by project
    val javaExecutablesPath = File(java6Home, "bin")
    fun javaExecutable(execName: String): String {
        val executable = File(javaExecutablesPath, execName)
        require(executable.exists()) { "There is no $execName executable in $javaExecutablesPath" }
        return executable.toString()
    }
    tasks.withType<JavaCompile>().configureEach {
        options.apply {
            isFork = true
            forkOptions.javaHome = file(java6Home)
        }
    }
    tasks.withType<Javadoc>().configureEach {
        executable = javaExecutable("javadoc")
    }
    tasks.withType<Test>().configureEach {
        executable = javaExecutable("java")
    }
    tasks.withType<JavaExec>.configureEach {
        executable = javaExecutable("java")
    }

* * *

[1](#_footnoteref_1)。Gradle使用与Russel Winder的[Gant工具](https://gant.github.io/)引入的约定相同的约定。

