# 将您的构建从Gradle 4.x升级到5.0

内容

* [对于所有用户](#for_all_users)
* [从4.10及更早版本升级](#changes_5.0)
* [从4.9或更早版本升级](#changes_4.10)
* [从4.8或更早版本升级](#changes_4.9)
* [从4.7或更早版本升级](#changes_4.8)
* [从4.6及更早版本升级](#changes_4.7)
* [从4.5或更早版本升级](#changes_4.6)
* [从4.4及更早版本升级](#changes_4.5)
* [从4.3或更早版本升级](#changes_4.4)
* [从4.2或更早版本升级](#changes_4.3)
* [从4.1及更早版本升级](#changes_4.2)
* [从4.0升级](#changes_4.1)
* [细节变化](#changes_in_detail)

本章提供了将较早的Gradle 4.x构建迁移到Gradle 5.0所需的信息。在大多数情况下，您将需要应用升级版本之后的所有版本中的更改。例如，如果您是从Gradle 4.3升级到5.0，则还需要应用4.4、4.5等（直到5.0）的更改。

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-tip"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><font><font>如果您使用的是Android Gradle，则需要同时升级到Android Gradle插件和Android Studio的3.3版或更高版本。</font></font></td></tr></tbody></table>

## [](#for_all_users)[对于所有用户](#for_all_users)

1.  如果尚未使用最新的4.10.x版本，请阅读以下各节[，](#changes_4.10)以帮助您将项目升级到最新的4.10.x版本。我们建议升级到最新的4.10.x版本以获取最有用的警告和弃用信息，然后再升级到5.0。避免同时升级Gradle并迁移到Kotlin DSL，以便在出现潜在问题时简化故障排除。
2.  尝试运行`gradle help --scan`并查看生成的构建扫描的[弃用视图](https://gradle.com/enterprise/releases/2018.4/#identify-usages-of-deprecated-gradle-functionality)。如果没有警告，则不会出现“弃用”选项卡。  

    这样一来，您就可以看到适用于您的构建的所有弃用警告。如果您尝试直接升级到Gradle 5.x，它将生成（可能不太明显）错误。

    或者，您可以运行`gradle help \--warning-mode=all`在控制台中查看弃用项，尽管它可能不会报告太多详细信息。
3.  更新您的插件。  

    一些插件将随新版本的Gradle一起中断，例如，因为它们使用已被删除或更改的内部API。上一步将通过在插件确实尝试使用API弃用的部分时发出弃用警告来帮助您识别潜在的问题。

    特别是，您将至少需要使用2.x版本的[**Shadow Plugin**](https://plugins.gradle.org/plugin/com.github.johnrengelman.shadow)。
4.  运行`gradle wrapper --gradle-version 5.0`以将项目更新到5.0
5.  如果还没有，请转到Java 8或更高版本。而Gradle 4.x需要Java 7，Gradle 5需要Java 8才能运行。
6.  阅读“[从4.10升级”](#changes_5.0)部分并进行任何必要的更改。
7.  尝试使用《[故障排除指南》]()运行项目并调试所有错误。

此外，Gradle还添加了一些重要的新功能和改进的功能，您应该在构建中考虑使用这些功能：

* [Maven Publish和Ivy Publish插件](#rel4.8:switch_to_publishing_plugins)现在支持使用[Signing Plugin进行]()数字签名。
* 在构建中使用本机[BOM导入](#rel5.0:bom_import)。
* 用于使工作单元并行运行的[Worker API]()。
* 用于[延迟创建和配置任务的](#rel4.9:lazy_task_creation)新API，可以显着缩短构建的配置时间。

需要注意的其他值得注意的更改可能会破坏您的构建，包括：

* [使用POM时将编译和运行时依赖项分开](#rel5.0:pom_compile_runtime_separation)
* 更改意味着您应该[配置现有任务`wrapper`和`init`任务，](#rel4.8:configure_internal_tasks)而不是定义自己的[任务](#rel4.8:configure_internal_tasks)。
* 的[Maven中POM排除隐式通配符的表彰](#rel4.8:pom_wildcard_exclusions)，这可能会导致依赖性被排除那些不前。
* 一[的方式来改变你添加Java注释处理器的一个项目](#rel4.6:annotation_processor_configuration)。
* 命令行客户端Gradle守护程序以及所有工作程序（包括编译器和测试执行程序）的[默认内存设置](#rel5.0:default_memory_settings)已大大减少。
* [几个代码质量插件](#rel5.0:default_tool_versions)的[默认版本](#rel5.0:default_tool_versions)已更新。
* [Gradle使用的](#rel5.0:library_upgrades)几个[库版本](#rel5.0:library_upgrades)已升级。

## [](#changes_5.0)[从4.10及更早版本升级](#changes_5.0)

如果您尚未使用4.10版本，请跳至适用于当前Gradle版本的部分，然后逐步升级，直至到达此处。然后，从Gradle 4.10移至5.0时应用这些更改。

### [](#other_changes)[其他变化](#other_changes)

* 在`enableFeaturePreview('IMPROVED_POM_SUPPORT')`和`enableFeaturePreview('STABLE_PUBLISHING')`标志不再是必要的。现在默认情况下启用了这些功能。
* Gradle现在捆绑了Java 9及更高版本的[JAXB](#rel5.0:jaxb_and_java9)。您可以从中删除该`--add-modules java.xml.bind`选项`org.gradle.jvmargs`（如果已设置）。

### [](#potential_breaking_changes)[潜在的重大变化](#potential_breaking_changes)

本部分中的更改有可能破坏您的构建，但是大多数版本已被弃用了相当长的时间，很少有构建会受到大量构建的影响。我们强烈建议先升级到Gradle 4.10，以获取有关弃用会影响构建的报告。

以下重大变化不是因为弃用，而是行为变化的结果：

* [使用POM时将编译和运行时依赖项分开](#rel5.0:pom_compile_runtime_separation)
* 对该`publishing {}`块的评估不再推迟到需要时才执行，但其行为类似于任何其他块。`afterEvaluate {}`如果您需要推迟评估，请使用。
* 在[`Javadoc`]()和[`Groovydoc`]()任务现在执行之前删除的文件的目标目录。已添加它以从上一次任务执行中删除过时的输出文件。
* 该[Java库分布插件]()现在是基于[Java库插件]()，而不是[Java插件]()。  

  在应用Java插件时，其行为略有不同（例如，它添加了`api`配置）。因此，请确保在升级后检查您的构建是否符合预期。
* 该`html`物业`CheckstyleReport`和`FindBugsReport`现在返回一个[`CustomizableHtmlReport`]()实例，它是更容易从像Java和科特林静态类型语言配置。
* 该[配置避免API](#rel5.0:configuration_avoidance)已更新，以防止从未使用任务的创建和配置。
* 命令行客户端Gradle守护程序以及所有工作程序（包括编译器和测试执行程序）的[默认内存设置](#rel5.0:default_memory_settings)已大大减少。
* [几个代码质量插件](#rel5.0:default_tool_versions)的[默认版本](#rel5.0:default_tool_versions)已更新。
* [Gradle使用的](#rel5.0:library_upgrades)几个[库版本](#rel5.0:library_upgrades)已升级。

以下重大更改将在Gradle 4.10中作为弃用警告显示：

一般

* `<<`任务定义不再有效。换句话说，您不能使用语法`task myTask << { … }`。  

  改用[Task.doLast（）]()方法，如下所示：

  任务myTask \{ doLast \{ ... \} \}
* 您不能再在域对象名称中使用以下任何字符，例如项目和任务名称：\<space>`/ \ : < > " ? * |`。您也不应将其`.`用作前导或尾随字符。

运行Gradle和构建环境

* 如前所述，Gradle不能再在Java 7上运行。但是，您仍然可以使用[派生的编译和测试]()来构建和测试Java 6及更高版本的软件。
* 该`-Dtest.single`命令行选项已被删除-使用[测试过滤]()来代替。
* 该`-Dtest.debug`命令行选项已被删除-使用[`--debug-jvm`选项]()来代替。
* 将`-u`/`--no-search-upward`命令行选项已被删除-确保所有的构建有_settings.gradle_文件。
* 该`--recompile-scripts`命令行选项已被删除。
* 除非嵌套的构建具有_settings.gradle_文件，否则您不能再将Gradle嵌套在另一个Gradle构建的子目录中。
* 该`DirectoryBuildCache.setTargetSizeInMB(long)`方法已删除-使用[DirectoryBuildCache.removeUnusedEntriesAfterDays]()代替。
* 该`org.gradle.readLoggingConfigFile`系统属性不再做任何事情-更新受影响的测试，你的工作`java.util.logging`设置。

处理文件

* 您不再可以`FileCollection`使用`as`关键字或`asType()`方法将对象转换为其他类型。
* 您不能再`null`作为[CopySpec.from（Object，Action）]()的配置操作通过。
* 为了与Kotlin DSL更好地兼容，[CopySpec.duplicatesStrategy]()不再可以为空。属性设置器不再接受`null`将其重置为默认值的方式。使用`DuplicatesStrategy.INHERIT`代替。
* 该`FileCollection.stopExecutionIfEmpty()`方法已删除-在任务属性上使用[\@SkipWhenEmpty]()批注`FileCollection`。
* 该`FileCollection.add()`方法已删除-使用[Project.files（）]()和[Project.fileTree（）]()创建可配置的文件集合/文件树，并通过[ConfigurableFileCollection.from（）]()将其添加到其中。
* `SimpleFileCollection`已被删除-使用[Project.files（Object ...）]()代替。
* 没有自己的类扩展`AbstractFileCollection`\-改用[Project.files（）]()方法。此问题可能显示为缺少的`getBuildDependencies()`方法。

Java构建

* 该`CompileOptions.bootClasspath`属性已删除-使用[CompileOptions.bootstrapClasspath]()代替。
* 您不能再`-source-path`用作通用编译器参数，而应改用[CompileOptions.sourcepath]()。
* 您不能再`-processorpath`用作通用编译器参数，而应改用[CompileOptions.annotationProcessorPath]()。
* Gradle将不再自动应用编译类路径上的注释处理器-改用[CompileOptions.annotationProcessorPath]()。
* 该`testClassesDir`属性已从“[测试”]()任务中删除\-改为使用[testClassesDirs]()。
* 该`classesDir`属性已从_JDepend_任务和[SourceSetOutput中删除]()。请改用_JDepend.classesDirs_和[SourceSetOutput.classesDirs]()属性。
* `JavaLibrary(PublishArtifact, DependencySet)`构造函数已被删除[\-Shadow Plugin](https://plugins.gradle.org/plugin/com.github.johnrengelman.shadow)使用了该构造函数，因此请确保至少升级到该插件的2.x版本。
* 该`JavaBasePlugin.configureForSourceSet()`方法已被删除。
* 您不再可以创建自己的[JavaPluginConvention]()，[ApplicationPluginConvention]()，[WarPluginConvention]()，[EarPluginConvention]()，[BasePluginConvention]()和[ProjectReportsPluginConvention]()实例。
* 该`Maven`插件用于发布过时的Maven 2元数据格式。这已更改，现在将像`Maven Publish`插件一样发布Maven 3元数据。  

  随着对Maven 2支持的删除，配置唯一快照行为的方法也已删除。Maven 3仅支持唯一的快照，因此我们决定删除它们。

任务和属性

* 删除了以下与[惰性属性]()相关的旧类和方法\-使用[ObjectFactory.property（）]()创建`Property`实例：  

  * `PropertyState`
  * `DirectoryVar`
  * `RegularFileVar`
  * `ProjectLayout.newDirectoryVar()`
  * `ProjectLayout.newFileVar()`
  * `Project.property(Class)`
  * `Script.property(Class)`
  * `ProviderFactory.property(Class)`
* 使用[任务配置避免]()API配置和注册的[任务]()对可以从配置操作中调用的其他方法有更多限制。
* 内部`@Option`和`@OptionValues`注释（程序包`org.gradle.api.internal.tasks.options`）已被删除。请改用公共[\@Option]()和[\@OptionValues]()批注。
* 该`Task.deleteAllActions()`方法已删除，无法替代。
* 该`Task.dependsOnTaskDidWork()`方法已删除-改用[声明的输入和输出]()。
* `TaskInternal`已删除以下属性和方法\-使用任务依赖项，任务规则，可重用的实用程序方法或[Worker API]()代替直接执行任务。  

  * `execute()`
  * `executer`
  * `getValidators()`
  * `addValidator()`
* 该[TaskInputs.file（对象）]()方法可以不再用参数来调用解析为比一个常规文件的其他任何内容。
* 该[TaskInputs.dir（对象）]()方法可以不再用参数来调用解析为多单目录以外的任何其他。
* 您不再可以通过[TaskInputs]()和[TaskOutputs]()注册无效的输入和输出。
* 该`TaskDestroyables.file()`和`TaskDestroyables.files()`方法已被删除-使用[TaskDestroyables.register（）]()来代替。
* `SimpleWorkResult`已被删除-使用[WorkResult.didWork]()。
* 现在，覆盖[4.8](#deprecations_4.8)中[弃用的](#deprecations_4.8)内置任务会产生错误。  

  尝试替换内置任务将产生类似于以下错误：

  \>无法添加任务“包装器”，因为该名称的任务已经存在。

Scala和Play

* 不再支持Play 2.2-请升级您使用的Play版本。
* 该`ScalaDocOptions.styleSheet`属性已被删除-Scala 2.11.8及更高版本中的Scaladoc Ant任务不再支持此属性。

Kotlin DSL

* Artifact配置访问器现在具有类型，`NamedDomainObjectProvider<Configuration>`而不是`Configuration`
* `PluginAware.apply<T>(to)`被重命名了`PluginAware.applyTo<T>(target)`。

这两个更改都可能导致脚本编译错误。请参阅[Gradle Kotlin DSL发行说明，](https://github.com/gradle/kotlin-dsl-samples/releases/tag/v1.0.2#breaking-changes)以获取更多信息以及如何修复因上述更改而损坏的构建。

杂

* 该`ConfigurableReport.setDestination(Object)`方法已删除-使用[ConfigurableReport.setDestination（File）]()代替。
* 该`Signature.setFile(File)`方法已删除-Gradle不支持更改生成的签名的输出文件。
* 只读`Signature.toSignArtifact`属性已被删除-永远不应成为公共API的一部分。
* 该`@DeferredConfigurable`注释已被删除。
* 该方法`isDeferredConfigurable()`已从中删除`ExtensionSchema`。
* `IdeaPlugin.performPostEvaluationActions()`并`EclipsePlugin.performPostEvaluationActions()`已被删除。
* ``The `BroadcastingCollectionEventRegister.getAddAction()``方法已删除，无法替代。
* `org.gradle.util`默认情况下，不再导入内部软件包。  

  理想情况下，您不应该使用此软件包中的类，但是，作为快速解决方案，您可以将显式导入添加到这些类的构建脚本中。
* [默认情况下](#rel5.0:gradle_plugin_portal_metadata)，`gradlePluginPortal()`存储库[不再查找没有POM的JAR](#rel5.0:gradle_plugin_portal_metadata)。
* Tooling API不能再使用Gradle 2.6以下的Gradle版本连接到构建。通过TestKit运行的构建也是如此。
* Gradle 5.0需要最低版本的Tooling API客户端3.0。较旧的客户端库无法再使用Gradle 5.0运行构建。
* 所述IdeaModule模具API模型元素包含方法，所以这些元素是从结果移除以检索资源和测试资源`IdeaModule.getSourceDirs()`和`IdeaModule.getTestSourceDirs()`。
* 在以前的Gradle版本中，可从子类访问中的`source`字段`SourceTask`。`source`现在不再是这种情况，因为该字段现在声明为`private`。
* 在Worker API中，[不能再设置worker的工作目录](#rel5.0:worker_api)。
* 与[依赖关系和版本约束](#rel5.0:dependency_constraints)有关的行为更改可能会影响少量用户。
* [对DefaultTask](#rel5.0:changes_to_default_task)上的[属性工厂方法进行](#rel5.0:changes_to_default_task)了一些更改，这些更改可能会影响自定义任务的创建。

## [](#changes_4.10)[从4.9或更早版本升级](#changes_4.10)

如果您尚未安装4.9版，请跳至适用于当前Gradle版本的部分，然后逐步升级，直至到达此处。然后，在升级到Gradle 4.10时应用这些更改。

### [](#deprecated_classes_methods_and_properties)[不推荐使用的类，方法和属性](#deprecated_classes_methods_and_properties)

按照API链接了解如何处理这些弃用（如果此处未提供其他信息）：

* `TaskContainer.add()`和`TaskContainer.addAll()`\-使用[TaskContainer.create（）]()或[TaskContainer.register（）]()代替

### [](#potential_breaking_changes_2)[潜在的重大变化](#potential_breaking_changes_2)

* Kotlin DSL中存在一些潜在的重大更改—请参阅该[项目的发行说明中](https://github.com/gradle/kotlin-dsl/releases/tag/v1.0-RC3)的_“重大更改”_部分。[](https://github.com/gradle/kotlin-dsl/releases/tag/v1.0-RC3)
* 您不能再使用具有惰性任务配置的任何[Project.beforeEvaluate（）]()或[Project.afterEvaluate（）]()方法，例如在[TaskContainer.register（）]()块内。
* [发布到AWS S3需要新权限](#rel4.10:aws_s3_permissions)。
* 无论[PluginUnderTestMetadata]()和[GeneratePluginDescriptors]()\-由所使用的类[Java的摇篮插件开发插件]()\-已更新为使用提供API。  

  使用[Property.set（）]()方法修改其值，而不要使用标准的属性分配语法，除非您在Groovy构建脚本中这样做。在这种情况下，标准属性分配仍然有效。

## [](#changes_4.9)[从4.8或更早版本升级](#changes_4.9)

* [考虑尝试使用惰性API进行任务创建和配置](#rel4.9:lazy_task_creation)

### [](#potential_breaking_changes_3)[潜在的重大变化](#potential_breaking_changes_3)

* 您不能再将GPath语法与[task.withType（）一起使用]()。  

  请改用[Groovy的传播算子](https://docs.groovy-lang.org/latest/html/documentation/#_spread_operator)。例如，您将替换`tasks.withType(JavaCompile).name`为`tasks.withType(JavaCompile)*.name`。

## [](#changes_4.8)[从4.7或更早版本升级](#changes_4.8)

* [切换到Maven Publish和Ivy Publish插件](#rel4.8:switch_to_publishing_plugins)
* [将延迟配置与发布插件一起使用](#rel4.8:deferred_configuration)
* [配置现有任务`wrapper`和`init`任务，](#rel4.8:configure_internal_tasks)而不是定义自己的[任务](#rel4.8:configure_internal_tasks)
* 如果当前正在为此目的使用插件或自定义解决方案，请考虑迁移到内置的[依赖项锁定机制]()

### [](#potential_breaking_changes_4)[潜在的重大变化](#potential_breaking_changes_4)

* 现在，如果找不到指定的初始化脚本，构建将失败。
* `TaskContainer.remove()`现在实际上删除了给定的任务-一些插件可能不小心依赖了旧的行为。
* [Gradle现在可以在Maven POM排除中使用隐式通配符](#rel4.8:pom_wildcard_exclusions)。
* Kotlin DSL现在遵守JSR-305封装注释。  

  这将导致根据JSR-305注释的某些类型在以前被视为不可为空的情况下被视为可为空。这可能会导致生成脚本中的编译错误。有关详细信息，请参阅[相关的Kotlin DSL发行说明](https://github.com/gradle/kotlin-dsl/releases/tag/v0.17.4)。
* 错误消息现在将定向到标准错误而不是标准输出，除非将控制台附加到标准输出和标准错误上。这可能会影响抓取构建的普通控制台输出的工具。如果要从较早版本的Gradle升级，请忽略此更改。

### [](#deprecations_4.8)[弃用](#deprecations_4.8)

在此版本之前，允许内部版本替换内置任务。[不推荐使用此功能](https://docs.gradle.org/4.8/release-notes.html#overwriting-gradle's-built-in-tasks)。

内置的任务的完整列表不应该被替换为：`wrapper`，`init`，`help`，`tasks`，`projects`，`buildEnvironment`，`components`，`dependencies`，`dependencyInsight`，`dependentComponents`，`model`，`properties`。

## [](#changes_4.7)[从4.6及更早版本升级](#changes_4.7)

### [](#potential_breaking_changes_5)[潜在的重大变化](#potential_breaking_changes_5)

* 按照约定，Gradle现在将在根项目的_config / checkstyle_目录中查找Checkstyle配置文件。  

  除非您通过[checkstyle.configDir]()或[checkstyle.config]()显式配置它们的路径，否则子项目中的Checkstyle配置文件（旧的约定场所）将被忽略。
* Gradle[普通控制台输出](#rel4.7:plain_console_output)的结构已更改，这可能会破坏抓取该输出的工具。
* 与编译，链接和安装相关的许多本机任务的API[发生了重大变化](#rel:4.6:native_task_api_changes)。
* \[Kotlin DSL\]现在必须显式键入用于访问Gradle的构建属性的委托属性（例如，在_gradle.properties中_定义）。
* \[Kotlin DSL\]`plugins {}`现在，在嵌套作用域内声明一个块会引发异常。
* \[Kotlin DSL\]`pluginManagement {}`现在仅允许一个块。
* `org.gradle.api.artifacts.cache.*`接口提供的缓存控制DSL不再可用。
* `getEnabledDirectoryReportDestinations()`，`getEnabledFileReportDestinations()`并且`getEnabledReportNames()`都已从中删除`org.gradle.api.reporting.ReportContainer`。
* [StartParameter.projectProperties]()和[StartParameter.systemPropertiesArgs]()现在返回不可变映射。

## [](#changes_4.6)[从4.5或更早版本升级](#changes_4.6)

### [](#deprecations)[弃用](#deprecations)

* 您不应将注释处理器放在编译类路径上，也不要使用`-processorpath`编译器参数对其进行声明。  

  应该将它们添加到`annotationProcessor`配置中。如果您不希望进行任何处理，但是编译类路径无意中包含一个处理器（例如，作为您依赖的库的一部分），请使用`-proc:none`编译器参数将其忽略。
* 使用[CommandLineArgumentProvider]()代替[CompilerArgumentProvider](https://docs.gradle.org/4.10.3/javadoc/org/gradle/api/tasks/compile/CompilerArgumentProvider.html)。

### [](#potential_breaking_changes_6)[潜在的重大变化](#potential_breaking_changes_6)

* Java插件现在`_sourceSet_AnnotationProcessor`为每个源集添加一个配置，如果其中任何一个与您现有的配置相匹配，则该配置可能会中断。我们建议您删除冲突的配置声明。
* 该`StartParameter.taskOutputCacheEnabled`属性已由[StartParameter.setBuildCacheEnabled（boolean）]()替换。
* 现在，Visual Studio集成仅为[构建中的所有组件配置一个解决方案](#rel4.6:visual_studio_single_solution)。
* Gradle已用4.5.5版本替换了HttpClient 4.4.1。
* Gradle现在捆绑了`kotlin-stdlib-jdk8`神器而不是`kotlin-stdlib-jre8`。这可能会影响您的构建。请参阅[Kotlin文档](http://kotlinlang.org/docs/reference/whatsnew12.html#kotlin-standard-library-artifacts-and-split-packages)以了解更多详细信息。

## [](#changes_4.5)[从4.4及更早版本升级](#changes_4.5)

* 确保您具有_settings.gradle_文件：它避免了性能下降，并允许您设置根项目的名称。
* Gradle现在忽略包含的构建（[复合构建]()）的构建缓存配置，而是对所有构建使用根构建的配置。

### [](#potential_breaking_changes_7)[潜在的重大变化](#potential_breaking_changes_7)

* `ValidateTaskProperties.setOutputFile()`删除了两个重载方法。当从构建脚本访问任务时，它们会被自动生成的setter替换，但是构建脚本之外的插件和其他代码不会出现这种情况。
* Maven Publish插件现在可以生成更完整的maven-metadata.xml文件，包括维护`<snapshotVersion>`元素列表。一些旧版本的Maven可能无法使用此元数据。
* [`HttpBuildCache`不再遵循重定向](#rel4.5:http_build_cache_no_follow_redirects)。
* 该`Depend`任务类型已被删除。
* [Project.file（Object）]()不再规范不区分大小写的文件系统上文件路径的大小写。现在，在这种情况下，它会忽略大小写，并且不会影响文件系统。
* [ListProperty]()不再扩展[Property]()。

## [](#changes_4.4)[从4.3或更早版本升级](#changes_4.4)

### [](#potential_breaking_changes_8)[潜在的重大变化](#potential_breaking_changes_8)

* 现在，非JVM测试任务以及[Test]()扩展了[AbstractTestTask]()。因此，插件应注意配置所有类型的任务`AbstractTestTask`。
* [EclipseClasspath.defaultOutputDir]()的默认输出位置已从_`$projectDir`/ bin_更改为_`$projectDir`/ bin / default_。
* 不推荐使用的产品`InstallExecutable.setDestinationDir(Provider)`已删除-使用[InstallExecutable.installDirectory]()代替。
* 已弃用的`InstallExecutable.setExecutable(Provider)`已删除-使用[InstallExecutable.executableFile]()代替。
* Gradle将不再喜欢在路径上找到的Visual Studio版本，而不是其他位置。现在是不得已了。  

  您可以通过[VisualCpp.setInstallDir（Object）]()指定所需的Visual Studio版本的安装目录来绕过工具链发现。
* `pluginManagement.repositories`现在的类型为[RepositoryHandler]()而不是`PluginRepositoriesSpec`已被删除的。
* 依赖关系解决期间的5xx HTTP错误现在将触发构建中的异常。
* 嵌入式Apache Ant已从1.9.6升级到1.9.9。
* [Gradle使用的一些第三方库已升级，](#rel4.4:security_library_upgrades)可以解决安全问题。

## [](#changes_4.3)[从4.2或更早版本升级](#changes_4.3)

* `plugins {}`现在，该块可以[用于子项目中]()，也可以[用于]()[_buildSrc_]()[目录中的]()[插件]()。

### [](#other_deprecations)[其他折旧](#other_deprecations)

* 您不应再通过Tooling API运行2.6之前的Gradle版本。
* 您不应再通过比3.0更旧版本的Tooling API运行任何版本的Gradle。
* 您不应再链接[TaskInputs.property（String，Object）]()和[TaskInputs.properties（Map）]()方法。

### [](#potential_breaking_changes_9)[潜在的重大变化](#potential_breaking_changes_9)

* [DefaultTask.newOutputDirectory（）]()现在返回`DirectoryProperty`而不是`DirectoryVar`。
* [DefaultTask.newOutputFile（）]()现在返回`RegularFileProperty`而不是`RegularFileVar`。
* [DefaultTask.newInputFile（）]()现在返回`RegularFileProperty`而不是`RegularFileVar`。
* [ProjectLayout.buildDirectory]()现在返回`DirectoryProperty`而不是`DirectoryVar`。
* 现在，[AbstractNativeCompileTask.compilerArgs]()的类型`ListProperty<String>`为`List<String>`。
* 现在，[AbstractNativeCompileTask.objectFileDir]()的类型为`DirectoryProperty`而不是`File`。
* 现在，[AbstractLinkTask.linkerArgs]()的类型`ListProperty<String>`为`List<String>`。
* `TaskDestroyables.getFiles()`不再是公共API的一部分。
* 现在，依赖项的版本范围重叠会导致Gradle选择一个满足所有声明范围的版本。  

  例如，如果`some-module`找到了版本范围为的依存关系，`[3,6]`并且也可传递范围的范围为`[4,8]`，则Gradle现在选择版本6而不是8。以前的行为是选择8。
* `Iterable`标记为`@OutputFiles`或`@OutputDirectories`现在的属性中元素的顺序很重要。如果顺序更改，则该属性不再被视为最新。  

  建议将单独的属性与`@OutputFile`/`@OutputDirectory`注释一起使用`Map`，或者将属性与`@OutputFiles`/一起使用`@OutputDirectories`。
* 当存在可以检查的另一个存储库时，Gradle将不再忽略该存储库中的依赖项解决错误。依赖关系解析将失败。这导致相对于分辨率结果更具确定性的行为。

## [](#changes_4.2)[从4.1及更早版本升级](#changes_4.2)

### [](#potential_breaking_changes_10)[潜在的重大变化](#potential_breaking_changes_10)

* [TaskFilePropertyBuilder]()和[TaskOutputFilePropertyBuilder]()`withPathSensitivity()`上的方法已删除。
* 捆绑包`bndlib`已从3.2.0升级到3.4.0。
* FindBugs插件不再从其分析中呈现进度信息。如果您以任何方式依赖该输出，都可以使用_FindBugs.showProgress_启用它。

## [](#changes_4.1)[从4.0升级](#changes_4.1)

* 考虑使用新的[Worker API]()来使构建中的工作单元并行运行。

### [](#deprecated_classes_methods_and_properties_2)[不推荐使用的类，方法和属性](#deprecated_classes_methods_and_properties_2)

按照API链接了解如何处理这些弃用（如果此处未提供其他信息）：

* [可空](https://docs.gradle.org/4.10.3/javadoc/org/gradle/api/Nullable.html)

### [](#potential_breaking_changes_11)[潜在的重大变化](#potential_breaking_changes_11)

* 与Java项目有[项目依赖性]()的非Java项目现在`runtimeElements`默认使用配置而不是`default`配置。  

  要覆盖此行为，您可以显式声明要在项目依赖项中使用的配置。例如：`project(path: ':myJavaProject', configuration: 'default')`。
* 默认的Zinc编译器从0.3.13升级到0.3.15。
* \[Kotlin DSL\]基本软件包从改名`org.gradle.script.lang.kotlin`为`org.gradle.kotlin.dsl`。

## [](#changes_in_detail)[细节变化](#changes_in_detail)

### [](#rel5.0:default_memory_settings)[\[5.0\]默认内存设置已更改](#rel5.0:default_memory_settings)

现在，命令行客户端从64MB的堆开始，而不是1GB。这可能会影响使用`--no-daemon`模式在客户端VM内部直接运行的内部版本。我们不鼓励使用`--no-daemon`，但是如果必须使用它，则可以使用`GRADLE_OPTS`环境变量来增加可用内存。

Gradle守护程序现在从512MB的堆而不是1GB的堆开始。大型项目可能必须使用该[`org.gradle.jvmargs`]()属性来增加此设置。

现在，所有工作程序（包括编译器和测试执行程序）都以512MB的堆开始。先前的默认值为物理内存的1/4。大型项目可能必须在相关任务上增加此设置，例如[`JavaCompile`]()或[`Test`]()。

### [](#rel5.0:default_tool_versions)[\[5.0\]代码质量插件的新默认版本](#rel5.0:default_tool_versions)

以下代码质量插件的默认工具版本已更新：

* 该[Checkstyle的插件](http://checkstyle.sourceforge.net)现在使用[8.12](http://checkstyle.sourceforge.net/releasenotes.html#Release_8.12)默认，而不是6.19。
* 现在，默认情况下，[CodeNarc插件](http://codenarc.sourceforge.net)使用[1.2.1](https://github.com/CodeNarc/CodeNarc/blob/master/CHANGELOG.md#version-121-aug-2018)而不是1.1。
* 该[JaCoCo插件](https://www.jacoco.org/jacoco/)现在使用[0.8.2](https://www.jacoco.org/jacoco/trunk/doc/changes.html)，而不是0.8.1默认。
* 现在，[PMD插件](https://pmd.github.io/)默认使用[6.8.0](https://pmd.github.io/pmd-6.8.0/pmd_release_notes.html#30-september-2018---680)而不是5.6.1。  

  此外，默认规则集已从现已弃用更改`java-basic`为`category/java/errorprone.xml`。

  但是，我们建议显式配置规则集。

### [](#rel5.0:library_upgrades)[\[5.0\]库升级](#rel5.0:library_upgrades)

Gradle使用的一些库已升级：

* Groovy从2.4.15升级到[2.5.4](https://groovy-lang.org/releasenotes/groovy-2.5.html)。
* Ant已从1.9.11升级到[1.9.13](https://archive.apache.org/dist/ant/RELEASE-NOTES-1.9.13.html)。
* 用于访问S3支持的Maven / Ivy存储库的AWS开发工具包已从1.11.267升级到[1.11.407](https://github.com/aws/aws-sdk-java/blob/master/CHANGELOG.md#111407-2018-09-11)。
* OSGi插件使用的BND库已从3.4.0升级到[4.0.0](https://github.com/bndtools/bnd/wiki/Changes-in-4.0.0)。
* 用于访问Google Cloud Storage支持的Maven / Ivy存储库的Google Cloud Storage JSON API客户端库已从v1-rev116-1.23.0升级到v1-rev136-1.25.0。
* Ivy已从2.2.0升级到[2.3.0](http://ant.apache.org/ivy/history/2.3.0/release-notes.html)。
* `Test`任务使用的JUnit Platform库已从1.0.3升级到1.3.1。
* 用于访问Maven存储库的Maven Wagon库已从2.4升级到3.0.0。
* SLF4J已从1.7.16升级到[1.7.25](https://www.slf4j.org/news.html)。

### [](#rel5.0:dependency_constraints)[\[5.0\]改进了对依赖关系和版本限制的支持](#rel5.0:dependency_constraints)

通过Gradle 4.x发行流，新`@Incubating`功能已添加到依赖关系解析引擎。这包括先进的版本约束（`prefer`，`strictly`，`reject`），依赖约束和`platform`依赖关系。

如果您一直在使用`IMPROVED_POM_SUPPORT`功能预览，使用约束条件或偏好，拒绝，以及其他特定版本指示，那么请确保对依赖关系解决方案的结果进行了仔细的研究。

### [](#rel5.0:bom_import)[\[5.0\] BOM导入](#rel5.0:bom_import)

Gradle现在为导入物料清单（BOM）文件提供支持，该文件是有效的POM文件，使用`<dependencyManagement>`部分来控制直接和传递依赖项的版本。您需要做的就是将POM声明为`platform`依赖项。

以下示例从声明的Spring Boot BOM中选择`gson`和和`dom4j`依赖项的版本：

依赖项\{ //导入BOM 实现平台（'org.springframework.boot：spring-boot-dependencies：1.5.8.RELEASE'）
 //定义没有版本的依赖项 实施'com.google.code.gson：gson' 实现'dom4j：dom4j' \}

### [](#rel5.0:pom_compile_runtime_separation)[\[5.0\]使用POM时，将编译和运行时依赖项分开](#rel5.0:pom_compile_runtime_separation)

从Gradle 1.0开始，Java编译类路径中已包含运行时范围的依赖项，这具有一些缺点：

* 编译类路径比其所需的要大得多，从而减慢了编译速度。
* 编译类路径包括不会影响编译的运行时作用域文件，当这些文件更改时，会导致不必要的重新编译。

通过这种新行为，Java和Java库插件都可以[实现编译范围和运行时范围]()的[分离]()。这意味着编译类路径仅包含编译范围的依赖关系，而运行时类路径也添加了运行时范围的依赖关系。如果您使用Gradle开发和发布Java库，并且它们之间的依赖关系`api`和`implementation`依赖关系反映在已发布的范围中，则这特别有用。

### [](#rel5.0:changes_to_default_task)[\[5.0\]对属性工厂方法的更改`DefaultTask`](#rel5.0:changes_to_default_task)

#### [](#property_factory_methods_on_defaulttask_are_now_final)[物业工厂方法`DefaultTask`现已确定](#property_factory_methods_on_defaulttask_are_now_final)

诸如此类的属性工厂方法`newInputFile()`旨在从extended类型的构造函数中调用`DefaultTask`。这些方法现在是最终方法，以避免子类覆盖这些方法并使用未初始化的状态。

#### [](#inputs_and_outputs_are_not_automatically_registered)[输入和输出不会自动注册](#inputs_and_outputs_are_not_automatically_registered)

这些方法返回的Property实例不再自动注册为任务的输入或输出。必须以通常的方式将Property实例声明为输入或输出，例如附加注释，例如`@OutputFile`或使用运行时API来注册属性。

例如，您以前可能使用以下语法，并且将两个outputFile实例都注册为声明的输出：

`Groovy``Kotlin`

build.gradle

class MyTask extends DefaultTask \{ // note: no annotation here final RegularFileProperty outputFile = newOutputFile\(\) \} task myOtherTask \{ def outputFile = newOutputFile\(\) doLast \{ ... \} \}

 

1

class MyTask extends DefaultTask \{

2

 // note: no annotation here

3

 final RegularFileProperty outputFile \= newOutputFile\(\)

4

\}

5

6

task myOtherTask \{

7

 def outputFile \= newOutputFile\(\)

8

 doLast \{ ... \}

9

\}

现在，您必须显式注册`outputFile`，如下所示：

`Groovy``Kotlin`

build.gradle

class MyTask extends DefaultTask \{ \@OutputFile // property needs an annotation final RegularFileProperty outputFile = project.objects.fileProperty\(\) \} task myOtherTask \{ def outputFile = project.objects.fileProperty\(\) outputs.file\(outputFile\) // or to be registered using the runtime API doLast \{ ... \} \}

 

1

class MyTask extends DefaultTask \{

2

 \@OutputFile // property needs an annotation

3

 final RegularFileProperty outputFile \= project.objects.fileProperty\(\)

4

\}

5

6

task myOtherTask \{

7

 def outputFile \= project.objects.fileProperty\(\)

8

 outputs.file\(outputFile\) // or to be registered using the runtime API

9

 doLast \{ ... \}

10

\}

### [](#rel5.0:jaxb_and_java9)[\[5.0\] Gradle现在捆绑了Java 9及更高版本的JAXB](#rel5.0:jaxb_and_java9)

为了使用S3支持的工件存储库，您以前必须在Java 9及更高版本上运行时添加`--add-modules java.xml.bind`到`org.gradle.jvmargs`。

由于Java 11不再包含该`java.xml.bind`模块，因此Gradle现在捆绑了JAXB 2.3.1（`com.sun.xml.bind:jaxb-impl`）并在Java 9及更高版本上使用它。

请`--add-modules java.xml.bind`从中删除该选项`org.gradle.jvmargs`（如果已设置）。

### [](#rel5.0:gradle_plugin_portal_metadata)[\[5.0\]`gradlePluginPortal()`默认情况下，存储库不再查找没有POM的JAR](#rel5.0:gradle_plugin_portal_metadata)

通过这种新行为，如果在`gradlePluginPortal()`存储库中找到的插件或插件的传递依赖项没有Maven POM，它将无法解决。

在没有POM的情况下发布到Maven存储库的工件应得到修复。如果遇到此类工件，请让插件或库作者发布具有适当元数据的新版本。

如果您遇到了插件问题，可以通过重新启用JAR作为`gradlePluginPortal()`存储库的元数据源来解决：

`Groovy``Kotlin`

settings.gradle

pluginManagement \{ repositories \{ gradlePluginPortal\(\).tap \{ metadataSources \{ mavenPom\(\) artifact\(\) \} \} \} \}

 

1

pluginManagement \{

2

 repositories \{

3

 gradlePluginPortal\(\).tap \{

4

 metadataSources \{

5

 mavenPom\(\)

6

 artifact\(\)

7

 \}

8

 \}

9

 \}

10

\}

### [](#rel5.0:java_library_distribution_plugin)[Java库分发插件利用Java库插件](#rel5.0:java_library_distribution_plugin)

该[Java库分布插件]()现在是基于[Java库插件]()，而不是[Java插件]()。

此外，由插件创建的默认发行版将包含`runtimeClasspath`配置的所有工件，而不是已弃用的`runtime`配置。

### [](#rel5.0:configuration_avoidance)[配置回避API禁止常见的配置错误](#rel5.0:configuration_avoidance)

该[配置避免API]()在摇篮4.9推出可以让你避免创建和配置是从未使用过的任务。

使用现有的API，此示例添加了两个任务（`foo`和`bar`）：

`Groovy``Kotlin`

build.gradle

tasks.create\("foo"\) \{ tasks.create\("bar"\) \}

 

1

tasks.create\("foo"\) \{

2

 tasks.create\("bar"\)

3

\}

将其转换为使用新的API时，会发生一些令人惊讶的事情：`bar`不存在。新的API仅在必要时执行配置操作，因此`register()`for任务`bar`仅在`foo`配置时执行。

`Groovy``Kotlin`

build.gradle

tasks.register\("foo"\) \{ tasks.register\("bar"\) // WRONG \}

 

1

tasks.register\("foo"\) \{

2

 tasks.register\("bar"\) // WRONG

3

\}

为了避免这种情况，Gradle现在可以检测到这一点，并在使用新API时防止修改基础容器（通过`create()`或`register()`）。

### [](#rel5.0:worker_api)[\[5.0\] Worker API：无法再设置工人的工作目录](#rel5.0:worker_api)

由于JDK 11不再支持更改正在运行的进程的工作目录，因此现在禁止通过其fork选项设置工作程序的工作目录。

现在，所有工作程序都使用相同的工作目录来启用重用。

请改为将文件和目录作为参数传递。

### [](#rel4.10:aws_s3_permissions)[\[4.10\]发布到AWS S3需要新权限](#rel4.10:aws_s3_permissions)

S3存储库传输协议允许Gradle将工件发布到AWS S3存储桶。从此版本开始，每个上传到S3存储桶的工件都将配备`bucket-owner-full-control`罐装ACL。确保用于发布工件的AWS账户具有`s3:PutObjectAcl`和`s3:PutObjectVersionAcl`权限，否则上传将失败。

\{ "Version":"2012-10-17", "Statement":\[ // ... \{ "Effect":"Allow", "Action":\[ "s3:PutObject", // necessary for uploading objects "s3:PutObjectAcl", // required starting with this release "s3:PutObjectVersionAcl" // if S3 bucket versioning is enabled \], "Resource":"arn:aws:s3:::myCompanyBucket/\*" \} \] \}

 

1

\{

2

 "Version":"2012-10-17",

3

 "Statement":\[

4

 // ...

5

 \{

6

 "Effect":"Allow",

7

 "Action":\[

8

 "s3:PutObject", // necessary for uploading objects

9

 "s3:PutObjectAcl", // required starting with this release

10

 "s3:PutObjectVersionAcl" // if S3 bucket versioning is enabled

11

 \],

12

 "Resource":"arn:aws:s3:::myCompanyBucket/\*"

13

 \}

14

 \]

15

\}

有关更多信息，请参阅[AWS S3跨账户访问]()。

### [](#rel4.9:lazy_task_creation)[\[4.9\]考虑尝试使用惰性API进行任务创建和配置](#rel4.9:lazy_task_creation)

Gradle 4.9引入了一种新的方式来创建和配置惰性工作的任务。当您使用这种方法来配置昂贵的任务时，或者当您有许多任务时，如果这些任务不运行，则构建配置时间会大大减少。

您可以在“[避免任务配置”]()一章中了解有关延迟创建任务的更多信息。您还可以在[此博客文章中](https://blog.gradle.org/preview-avoiding-task-configuration-time)了解此新功能的背景。

### [](#rel4.8:switch_to_publishing_plugins)[\[4.8\]切换到Maven Publish和Ivy Publish插件](#rel4.8:switch_to_publishing_plugins)

现在，发布插件已经稳定了，我们建议您从标准Java项目（即基于[Java Plugin的]()那些）的[旧发布]()机制中迁移。其中包括使用以下任何一项的项目：[Java库插件]()，[应用程序插件]()或[War插件]()。

要使用新方法，只需将任何`upload<Conf>`配置替换为一个`publishing {}`块。有关更多信息，请参见[发布概述一章]()。

### [](#rel4.8:deferred_configuration)[\[4.8\]使用延迟配置发布插件](#rel4.8:deferred_configuration)

在Gradle 4.8之前，该`publishing {}`块被隐式地视为在评估项目后就执行了其中的所有逻辑。这令人困惑，因为它是唯一以这种方式运行的块。作为Gradle 4.8稳定工作的一部分，我们将弃用此行为，并要求所有用户迁移其内部版本。

通过将以下内容添加到设置文件中，可以打开新的稳定行为：

`Groovy``Kotlin`

settings.gradle

enableFeaturePreview\('STABLE\_PUBLISHING'\)

 

1

enableFeaturePreview\('STABLE\_PUBLISHING'\)

我们建议对本地存储库进行测试运行，以查看所有工件是否仍具有预期的坐标。在大多数情况下，一切都应该像以前一样工作，您已完成。但是，您的发布块可能依赖于隐式延迟的配置，尤其是如果它依赖于在构建的配置阶段可能更改的值。

例如，在新行为下，以下逻辑假定设置`jar.archiveBaseName`后不会更改`artifactId`：

`Groovy``Kotlin`

build.gradle

subprojects \{ publishing \{ publications \{ mavenJava \{ from components.java artifactId = jar.archiveBaseName \} \} \} \}

 

1

subprojects \{

2

 publishing \{

3

 publications \{

4

 mavenJava \{

5

 from components.java

6

 artifactId \= jar.archiveBaseName

7

 \}

8

 \}

9

 \}

10

\}

如果该假设不正确或将来可能不正确，则`artifactId`必须在一个`afterEvaluate {}`块内进行设置，如下所示：

`Groovy``Kotlin`

build.gradle

subprojects \{ publishing \{ publications \{ mavenJava \{ from components.java afterEvaluate \{ artifactId = jar.archiveBaseName \} \} \} \} \}

 

1

subprojects \{

2

 publishing \{

3

 publications \{

4

 mavenJava \{

5

 from components.java

6

 afterEvaluate \{

7

 artifactId \= jar.archiveBaseName

8

 \}

9

 \}

10

 \}

11

 \}

12

\}

### [](#rel4.8:configure_internal_tasks)[\[4.8\]配置现有`wrapper`和`init`任务](#rel4.8:configure_internal_tasks)

您应该不会再定义自己`wrapper`和`init`任务。而是配置现有任务，例如，通过转换以下内容：

`Groovy``Kotlin`

build.gradle

task wrapper\(type: Wrapper\) \{ ... \}

 

1

task wrapper\(type: Wrapper\) \{

2

 ...

3

\}

对此：

`Groovy``Kotlin`

build.gradle

wrapper \{ ... \}

 

1

wrapper \{

2

 ...

3

\}

### [](#rel4.8:pom_wildcard_exclusions)[\[4.8\] Gradle现在可以在Maven POM排除中使用隐式通配符](#rel4.8:pom_wildcard_exclusions)

如果Maven POM中的排除项缺少`groupId`或`artifactId`，则Gradle会忽略该排除项。现在，丢失的元素被视为隐式通配符-例如`<groupId>*</groupId>`，这意味着您的某些依赖项可能现在被排除在以前没有的地方。

您将需要显式声明所需的任何缺少的依赖项。

### [](#rel4.7:plain_console_output)[\[4.7\]更改了Gradle普通控制台输出的结构](#rel4.7:plain_console_output)

普通控制台模式现在与格式丰富的控制台一致地格式化输出，这意味着输出格式已更改。例如：

* 现在，即使其他任务与其并行执行，由给定任务产生的输出也可以分组在一起。
* 任务执行标头以“>”任务前缀打印。
* 在生成执行过程中产生的所有输出都将写入标准输出文件句柄。这包括写入System.err的消息，除非您要将标准错误重定向到文件或任何其他非控制台目标。

这可能会破坏从普通控制台输出中抓取详细信息的工具。

### [](#rel:4.6:native_task_api_changes)[\[4.6\]与编译，链接和安装相关的本机任务的API更改](#rel:4.6:native_task_api_changes)

与编译，链接和安装本机库和应用程序有关的许多任务已转换为Provider API，以便它们支持[延迟配置]()。此转换对任务的API进行了一些重大更改，以使它们与Provider API的约定相匹配。

以下任务已更改：

[AbstractLinkTask]()及其子类

* `getDestinationDir()`被替换为`getDestinationDirectory()`。
* `getBinaryFile()`，`getOutputFile()`由取代`getLinkedFile()`。
* `setOutputFile(File)`去掉了。使用`Property.set()`代替。
* `setOutputFile(Provider)`去掉了。使用`Property.set()`代替。
* `getTargetPlatform()`已更改为返回`Property`。
* `setTargetPlatform(NativePlatform)`去掉了。使用`Property.set()`代替。
* `getToolChain()`已更改为返回`Property`。
* `setToolChain(NativeToolChain)`去掉了。使用`Property.set()`代替。

[CreateStaticLibrary]()

* `getOutputFile()`已更改为返回`Property`。
* `setOutputFile(File)`去掉了。使用`Property.set()`代替。
* `setOutputFile(Provider)`去掉了。使用`Property.set()`代替。
* `getTargetPlatform()`已更改为返回`Property`。
* `setTargetPlatform(NativePlatform)`去掉了。使用`Property.set()`代替。
* `getToolChain()`已更改为返回`Property`。
* `setToolChain(NativeToolChain)`去掉了。使用`Property.set()`代替。
* `getStaticLibArgs()`已更改为返回`ListProperty`。
* `setStaticLibArgs(List)`去掉了。使用`ListProperty.set()`代替。

[InstallExecutable]()

* `getSourceFile()`被替换为`getExecutableFile()`。
* `getPlatform()`被替换为`getTargetPlatform()`。
* `setTargetPlatform(NativePlatform)`去掉了。使用`Property.set()`代替。
* `getToolChain()`已更改为返回`Property`。
* `setToolChain(NativeToolChain)`去掉了。使用`Property.set()`代替。

以下还看到了类似的更改：

* [组装]()
* [WindowsResourceCompile]()
* [带状符号]()
* [提取符号]()
* [Swift编译]()
* [LinkMachOBundle]()

### [](#rel4.6:visual_studio_single_solution)[\[4.6\] Visual Studio集成仅对构建的所有组件支持单个解决方案文件](#rel4.6:visual_studio_single_solution)

[VisualStudioExtension]()不再具有`solutions`属性。相反，您可以通过根项目中的[VisualStudioRootExtension]()配置单个解决方案，如下所示：

build.gradle

model \{ visualStudio \{ solution \{ solutionFile.location = "vs/\$\{name\}.sln" \} \} \}

 

1

model \{

2

 visualStudio \{

3

 solution \{

4

 solutionFile.location \= "vs/\$\{name\}.sln"

5

 \}

6

 \}

7

\}

此外，不再需要为每个组件生成解决方案文件的单个`visualStudio`任务，而可以生成包含构建中所有组件的解决方案文件的单个任务。

### [](#rel4.5:http_build_cache_no_follow_redirects)[\[4.5\]`HttpBuildCache`不再遵循重定向](#rel4.5:http_build_cache_no_follow_redirects)

当通过连接到HTTP构建缓存后端时`HttpBuildCache`，Gradle不再跟随重定向，而是将其视为错误。从构建缓存后端获取重定向主要是配置错误（例如，使用“ http” URL而不是“ https”），这会对性能产生负面影响。

### [](#rel4.4:security_library_upgrades)[\[4.4\]第三方依赖项升级](#rel4.4:security_library_upgrades)

此版本包括第三方依赖项的多个升级：

* 杰克逊：2.6.6→2.8.9
* plexus-utils：2.0.6→2.1
* xercesImpl：2.9.1→2.11.0
* bsh：2.0b4→2.0b6
* 弹力城堡：1.57→1.58

这可以修复以下安全问题：

* [CVE-2017-7525](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-7525)（关键）
* SONATYPE-2017-0359（关键）
* SONATYPE-2017-0355（严重）
* SONATYPE-2017-0398（关键）
* [CVE-2013-4002](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2013-4002)（严重）
* [CVE-2016-2510](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-2510)（严重）
* SONATYPE-2016-0397（严重）
* [CVE-2009-2625](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2009-2625)（严重）
* SONATYPE-2017-0348（严重）

Gradle没有公开针对这些第三方依赖的公共API，但是那些自定义Gradle的人会希望知道。