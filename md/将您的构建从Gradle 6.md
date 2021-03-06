# 将您的构建从Gradle 6.x升级到最新版本


内容

  * [从6.6升级](#从6_6升级)
  * [从6.5升级](#从6_5升级)
  * [从6.4升级](#从6_4升级)
  * [从6.3升级](#从6_3升级)
  * [从6.2升级](#从6_2升级)
  * [从6.1升级](#从6_1升级)
  * [从6.0或更早版本升级](#从6_0或更早版本升级)

本章提供了将Gradle 6.x构建迁移到最新的Gradle版本所需的信息。要从Gradle
4.x或5.x进行迁移，请首先参阅[旧的迁移指南](/md/将您的构建从Gradle 5.md#upgrading_version_5)。

我们建议所有用户执行以下步骤：

  1. 尝试运行`gradle help --scan`并查看生成的构建扫描的[弃用视图](https://gradle.com/enterprise/releases/2018.4/#identify-usages-of-deprecated-gradle-functionality)。

![Gradle构建扫描的弃用视图](img/deprecations.png)

这样一来，您就可以看到适用于您的构建的所有弃用警告。

另外，您可能会`gradle help --warning-mode=all`在控制台中看到过时的信息，尽管它可能不会报告太多详细信息。

  2. 更新您的插件。

一些插件将随新版本的Gradle一起中断，例如，因为它们使用已被删除或更改的内部API。上一步将通过在插件确实尝试使用API​​弃用的部分时发出弃用警告来帮助您识别潜在的问题。

  3. 运行`gradle wrapper --gradle-version 6.7.1`以将项目更新为6.7.1。

  4. 尝试使用《[故障排除指南》](/md/对构建进行故障排除.md#troubleshooting)运行项目并调试所有错误。

## [从6_6升级](#从6_6升级)

### [潜在的重大变化](#潜在的重大变化)

#### [现在，buildSrc可以从根目录查看包含的构建](#现在，buildSrc可以从根目录查看包含的构建)

以前，`buildSrc`以这样的方式构建，即从根构建中忽略包含构建。

从Gradle
6.7开始，`buildSrc`可以从根版本中看到任何包含的版本。这可能会导致从包含的内置替代依赖`buildSrc`。如果所需的包含的构建，这也可能会更改执行某些构建的顺序`buildSrc`。

#### [更新到默认工具集成版本](#更新到默认工具集成版本)

  * PMD已更新至[PMD 6.26.0](https://github.com/pmd/pmd/releases/tag/pmd_releases%2F6.26.0)。

  * Checkstyle已更新至[Checkstyle 8.35](https://checkstyle.sourceforge.io/releasenotes.html#Release_8.35)。

  * CodeNarc已更新为[CodeNarc 1.6.1](https://github.com/CodeNarc/CodeNarc/blob/v1.6.1/CHANGELOG.md)。

### [弃用](#弃用)

#### [在执行阶段更改默认值排除](#在执行阶段更改默认值排除)

为了方便起见，Gradle的文件树应用了一些默认的排除模式-
实际上与Ant相同。有关更多信息，请参见[用户手册](/md/编写构建脚本.md#sec:file_trees)。有时，Ant的默认排除会证明是有问题的，例如，当您要`.gitignore`在存档文件中包含时。

在执行阶段更改Gradle的默认排除项可能导致最新检查的正确性问题，因此不建议使用。仅允许您在设置脚本中更改Gradle的默认排除项，有关示例，请参见[用户手册](/md/编写构建脚本.md#sec:change_default_excludes)。

#### [直接使用配置作为依赖项](#直接使用配置作为依赖项)

Gradle允许将的实例`Configuration`直接用作依赖项：

    
    
    dependencies {
        implementation(configurations.myConfiguration)
    }

现在不赞成使用此行为，因为它令人困惑：可以期望先解决“从属配置”，然后将解析结果作为从属关系添加到包含的配置中，事实并非如此。不推荐使用的版本可以替换为实际行为，即配置继承：

    
    
    configurations.implementation.extendsFrom(configurations.myConfiguration)

## [从6_5升级](#从6_5升级)

### [潜在的重大变化](#潜在的重大变化)

#### [更新捆绑的Gradle依赖项](#更新捆绑的Gradle依赖项)

  * Ant已更新至[1.10.8](https://downloads.apache.org/ant/RELEASE-NOTES-1.10.8.html)。

  * Groovy已更新为[Groovy 2.5.12](https://groovy-lang.org/changelogs/changelog-2.5.12.html)。

#### [依赖替换和变体感知依赖解析](#依赖替换和变体感知依赖解析)

在添加对表示依赖项替换中的[变体支持的支持](/md/直接自定义依赖项的解析.md#用另一个变量替换依赖)时，错误修复引入了某些构建可能依赖的行为更改。以前，替换的依赖项仍将使用原始选择器的[属性](/md/了解变体选择.md)，而不是替换选择器的[属性](/md/了解变体选择.md)。

有了这一更改，用丰富的选择器围绕依赖项进行的现有替换（例如平台依赖项）将不再像以前那样起作用。必须在目标选择器中定义变量识别部分。

如果发生以下情况，您可能会受到此更改的影响：

  * 对平台有依赖性，例如 `implementation platform("org:platform:1.0")`

  * _或者，_ 如果您指定依赖项的属性，

  * _并_ 在这些依赖项上使用[解析规则](/md/直接自定义依赖项的解析.md)。

如果您受到影响，请参阅[文档](/md/直接自定义依赖项的解析.md#用另一个变量替换依赖)以解决问题。

### [弃用](#弃用)

Gradle 6.6中未弃用。

## [从6_4升级](#从6_4升级)

### [潜在的重大变化](#潜在的重大变化)

#### [更新捆绑的Gradle依赖项](#更新捆绑的Gradle依赖项_2)

  * Kotlin已更新至[Kotlin 1.3.72](https://github.com/JetBrains/kotlin/releases/tag/v1.3.72)。

  * Groovy已更新为[Groovy 2.5.11](https://groovy-lang.org/changelogs/changelog-2.5.11.html)。

#### [更新到默认工具集成版本](#更新到默认工具集成版本_2)

  * PMD已更新至[PMD 6.23.0](https://github.com/pmd/pmd/releases/tag/pmd_releases%2F6.23.0)。

### [弃用](#弃用)

#### [内部类AbstractTask已弃用](#内部类AbstractTask已弃用)

`AbstractTask`是一个内部类，在公共API上可见，作为public type的超类`DefaultTask`。
`AbstractTask`将在Gradle 7.0中删除，而在Gradle 6.5中不推荐使用以下内容：

  * 注册类型为`AbstractTask`或的任务`TaskInternal`。您可以从任务注册中删除任务类型，而Gradle将`DefaultTask`代替使用。

  * 注册类型为的子类`AbstractTask`但不是的子类的任务`DefaultTask`。您可以更改任务类型以扩展`DefaultTask`。

  * 使用`AbstractTask`插件代码或构建脚本中的类。您可以更改代码以`DefaultTask`代替使用。

## [从6_3升级](#从6_3升级)

### [潜在的重大变化](#潜在的重大变化)

#### [PMD插件默认需要PMD6_0_0或更高版本](#PMD插件默认需要PMD6_0_0或更高版本)

Gradle 6.4默认情况下启用了增量分析。增量分析仅在PMD 6.0.0或更高版本中可用。如果要使用较旧的PMD版本，则需要禁用增量分析：

    
    
    pmd {
        incrementalAnalysis = false
    }

#### [依赖项锁定的更改](#依赖项锁定的更改)

使用Gradle
6.4，用于[依赖项锁定`LockMode`](/md/锁定依赖版本.md#使用锁定模式微调依赖项锁定行为)的孵化API已更改。现在通过a设置值，`Property<LockMode>`而不是直接设置器。这意味着必须为Kotlin
DSL更新设置值的表示法：

    
    
    dependencyLocking {
        lockMode.set(LockMode.STRICT)
    }

Groovy DSL的用户不会受到影响，因为该符号`lockMode = LockMode.STRICT`仍然有效。

#### [发布的元数据中的Java版本](#发布的元数据中的Java版本)

如果Java库与Gradle
Module元数据一起发布，则它支持的Java版本信息将编码在`org.gradle.jvm.version`属性中。默认情况下，此属性设置为您在中配置的属性`java.targetCompatibility`。如果未配置，则将其设置为运行Gradle的当前Java版本。例如，更改特定编译任务的版本`javaCompile.targetCompatibility`对该属性没有影响，如果未手动调整该属性，则会导致错误信息。现在，此问题已修复，并且属性默认为与构建发布jar的源相关联的编译任务的设置。

#### [具有自定义布局的Ivy存储库](#具有自定义布局的Ivy存储库)

在具有自定义存储库布局的Ivy存储库上发布时，包括的6.0至6.3.x的Gradle版本可能会生成错误的Gradle模块元数据。从6.4开始，如果Gradle检测到您正在使用自定义存储库布局，它将不再发布Gradle模块元数据。

#### [新属性可能会遮盖构建脚本中的变量](#新属性可能会遮盖构建脚本中的变量)

此版本引入了一些新特性- ， ，`mainClass`
-在不同的地方。由于这些是非常通用的名称，因此您有可能在构建脚本中使用其中之一作为变量名称。然后，新属性可能会以不希望的方式遮盖您的变量中的一个，从而导致构建失败，在该构建中，访问属性而不是使用具有相同名称的局部变量。您可以通过在构建脚本中重命名相应的变量来修复它。`mainModule``modularity`

受影响的是内部配置码`application {}`和`java {}`配置块，一个Java执行设置与内部`project.javaexec
{}`，和内部的各种任务配置（`JavaExec`，`CreateStartScripts`，`JavaCompile`，`Test`，`Javadoc`）。

#### [更新捆绑的Gradle依赖项](#更新捆绑的Gradle依赖项_3)

  * Kotlin已更新至[Kotlin 1.3.71](https://github.com/JetBrains/kotlin/releases/tag/v1.3.71)。

### [弃用](#弃用)

Gradle 6.3和6.4之间没有弃用。

## [从6_2升级](#从6_2升级)

### [潜在的重大变化](#潜在的重大变化)

#### [IDEA中可用的依赖项更少](#IDEA中可用的依赖项更少)

Gradle不再将注释处理器类路径包含为IDEA中提供的依赖项。IDEA在编译时看到的依赖性与Gradle在解决编译类路径（名为的配置`compileClasspath`）后看到的依赖性相同。这样可以防止注释处理器依赖项泄漏到项目代码中。

在Gradle引入[增量注释处理支持之前](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#sec:incremental_annotation_processing)，IDEA要求所有注释处理器都在编译类路径上，以便在IDEA中进行编译时能够运行注释处理。这不再是必需的，因为Gradle具有单独的[注释处理器classpath](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#tab:configurations)。导入具有批注处理器的Gradle项目时，批注处理器的依赖项不会添加到IDEA模块的类路径中。

#### [更新捆绑的Gradle依赖项](#更新捆绑的Gradle依赖项_4)

  * Kotlin已更新至[Kotlin 1.3.70](https://blog.jetbrains.com/kotlin/2020/03/kotlin-1-3-70-released/)。

  * Groovy已更新至[Groovy 2.5.10](http://groovy-lang.org/changelogs/changelog-2.5.10.html)。

#### [更新到默认工具集成版本](#更新到默认工具集成版本_3)

  * PMD已更新至[PMD 6.21.0](https://pmd.github.io/pmd-6.21.0/pmd_release_notes.html#24-january-2020---6210)。

  * CodeNarc已更新为[CodeNarc 1.5](https://github.com/CodeNarc/CodeNarc/blob/v1.5/CHANGELOG.md#version-15----nov-2019)。

#### [对某些32位操作系统删除了丰富的控制台支持](#对某些32位操作系统删除了丰富的控制台支持)

对于32位Unix系统和旧的FreeBSD版本（早于FreeBSD 10），Gradle
6.3不支持[丰富的控制台](/md/命令行界面.md#丰富控制台)。Microsoft
Windows 32位不受影响。

Gradle将继续在32位系统上构建项目，但不再显示丰富的控制台。

### [弃用](#弃用)

#### [使用默认配置和存档配置](#使用默认配置和存档配置)

几乎每个Gradle项目都有 _默认_ 配置和 _存档_ 配置，这些配置是由 _基本_
插件添加的。这些配置不再用于使用[变体感知依赖性管理](/md/使用变体属性.md)和[新发布插件的](/md/将项目发布为模块.md)现代Gradle构建中。

虽然目前这些配置仍保留在Gradle中以实现向后兼容，但现在不建议使用它们来声明依赖项或解析依赖项。

解决这些配置绝不是预期的用例，仅是可能的，因为在较早的Gradle版本中， _每个_
配置都是可以解决的。要声明依赖关系，请使用您使用的插件提供的配置，例如[Java库插件](/md/Java库插件.md#Java库插件配置)。

## [从6_1升级](#从6_1升级)

### [潜在的重大变化](#潜在的重大变化)

#### [现在，编译和运行时类路径默认情况下请求库变体](#现在，编译和运行时类路径默认情况下请求库变体)

现在，JVM项目中的类路径显式请求该`org.gradle.category=library`属性。如果无法使用某些库，这将导致更清晰的错误消息。例如，当库不支持所需的Java版本时。实际的效果是，现在所有[平台依赖项](/md/Java平台插件.md#消费平台)都必须这样声明。以前，当`platform()`本地平台或使用Gradle
Module Metadata发布的平台省略了关键字时，平台依赖性也偶然发生了。

#### [项目根目录中的属性`gradle_properties`泄漏到`buildSrc`包含的构建中](#项目根目录中的属性`gradle_properties`泄漏到`buildSrc`包含的构建中)

Gradle 6.2和Gradle
6.2.1中进行了回归，这导致在项目根`gradle.properties`文件中设置的Gradle属性泄漏到内部`buildSrc`版本以及该根所包含的所有内部版本中。

如果该`buildSrc`构建或包含的构建突然发现来自项目根`gradle.properties`文件的属性具有意外或不兼容的值，则可能导致构建开始失败。

回归已在Gradle 6.2.2中修复。

### [弃用](#弃用)

Gradle 6.1和6.2之间没有弃用。

## [从6_0或更早版本升级](#从6_0或更早版本升级)

### [弃用](#弃用)

#### [在任务完成之前查询任务的映射输出属性](#在任务完成之前查询任务的映射输出属性)

在任务完成之前查询映射的输出属性的值可能会导致奇怪的构建失败，因为这表明过时或不存在的输出可能会被错误使用。此行为已弃用，并将发出弃用警告。这将成为Gradle
7.0中的错误。

下面的示例演示了此问题，其中在生产者执行之前分析了生产者的输出文件：

    
    
    class Consumer extends DefaultTask {
        @Input
        final Property<Integer> threadPoolSize = ...
    }
    
    class Producer extends DefaultTask {
        @OutputFile
        final RegularFileProperty outputFile = ...
    }
    
    // threadPoolSize is read from the producer's outputFile
    consumer.threadPoolSize = producer.outputFile.map { it.text.toInteger() }
    
    // Emits deprecation warning
    println("thread pool size = " + consumer.threadPoolSize.get())

`consumer.threadPoolSize`如果在完成之前进行查询，则查询的值将产生弃用警告`producer`，因为尚未生成输出文件。

#### [停产方法](#停产方法)

以下方法已终止，不应再使用。它们将在Gradle 7.0中删除。

  * `BasePluginConvention.setProject(ProjectInternal)`

  * `BasePluginConvention.getProject()`

  * `StartParameter.useEmptySettings()`

  * `StartParameter.isUseEmptySettings()`

#### [备用JVM插件（也称为“软件模型”）](#备用JVM插件（也称为“软件模型”）)

Gradle
2.x中引入了一组用于Java和Scala开发的替代插件，作为基于“软件模型”的实验。这些插件现已弃用，最终将被删除。如果你还在使用这些老的插件之一（`java-
lang`，`scala-lang`，`jvm-component`，`jvm-resources`，`junit-test-
suite`）请查阅文档[构建Java和JVM的项目](/md/构建Java和JVM项目.md)，以确定其稳定的JVM插件是适合您的项目。

### [潜在的重大变化](#潜在的重大变化)

#### [`ProjectLayout`_不再可用于工作者作为服务的行为](#`ProjectLayout`_不再可用于工作者作为服务的行为)

在Gradle
6.0中，`ProjectLayout`通过服务注入使服务可用于工作人员操作。该服务允许易变状态泄漏到工作程序动作中，并为工作程序动作中未声明的依赖项提供了一种方法。

`ProjectLayout`已从可用服务中删除。正在使用的工作程序动作`ProjectLayout`应改为注入`projectDirectory`或`buildDirectory`作为参数。

#### [更新捆绑的Gradle依赖项](#更新捆绑的Gradle依赖项_5)

  * Kotlin已更新至[Kotlin 1.3.61](https://blog.jetbrains.com/kotlin/2019/11/kotlin-1-3-60-released/)。

#### [更新到默认工具集成版本](#更新到默认工具集成版本_4)

  * Checkstyle已更新至[Checkstyle 8.27](https://checkstyle.org/releasenotes.html#Release_8.27)。

  * PMD已更新至[PMD 6.20.0](https://pmd.github.io/pmd-6.20.0/pmd_release_notes.html#29-november-2019---6200)。

#### [发布Spring_Boot应用程序](#发布Spring_Boot应用程序)

从Gradle
6.2开始，Gradle会在上传之前执行健全性检查，以确保您没有上传陈旧的文件（由另一个构建生成的文件）。这会导致使用该`components.java`组件上传的Spring
Boot应用程序出现问题：

    
    
    Artifact my-application-0.0.1-SNAPSHOT.jar wasn't produced by this build.

这是由于以下事实造成`jar`的：Spring Boot应用程序禁用了主要任务，并且组件希望该任务存在。由于默认情况下该`bootJar`任务使用 _与_
主`jar`任务 _相同的文件_ ，因此Gradle的早期版本将：

  * 发布陈旧的`bootJar`工件

  * 或如果`bootJar`以前未调用过任务则失败

一种解决方法是告诉Gradle上传什么。如果要上传`bootJar`，则需要配置传出配置以执行此操作：

    
    
    configurations {
       [apiElements, runtimeElements].each {
           it.outgoing.artifacts.removeIf { it.buildDependencies.getDependencies(null).contains(jar) }
           it.outgoing.artifact(bootJar)
       }
    }

或者，您可能想重新启用`jar`任务，并`bootJar`使用其他分类器添加。

    
    
    jar {
       enabled = true
    }
    
    bootJar {
       classifier = 'application'
    }

