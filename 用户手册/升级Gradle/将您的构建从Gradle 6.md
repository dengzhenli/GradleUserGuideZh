# 将您的构建从Gradle 6.x升级到最新版本

内容

* [从6.6升级](#changes_6.7)
* [从6.5升级](#changes_6.6)
* [从6.4升级](#changes_6.5)
* [从6.3升级](#changes_6.4)
* [从6.2升级](#changes_6.3)
* [从6.1升级](#changes_6.2)
* [从6.0或更早版本升级](#changes_6.1)

本章提供了将Gradle 6.x构建迁移到最新的Gradle版本所需的信息。要从Gradle 4.x或5.x进行迁移，请首先参阅[旧的迁移指南]()。

我们建议所有用户执行以下步骤：

1.  尝试运行`gradle help --scan`并查看生成的构建扫描的[弃用视图](https://gradle.com/enterprise/releases/2018.4/#identify-usages-of-deprecated-gradle-functionality)。  
    ![](../../img/deprecations.png)
    这样一来，您就可以看到适用于您的构建的所有弃用警告。

    或者，您可以运行`gradle help \--warning-mode=all`在控制台中查看弃用项，尽管它可能不会报告太多详细信息。
2.  更新您的插件。  

    一些插件将随新版本的Gradle一起中断，例如，因为它们使用已被删除或更改的内部API。上一步将通过在插件确实尝试使用API弃用的部分时发出弃用警告来帮助您识别潜在的问题。
3.  运行`gradle wrapper --gradle-version 6.7.1`以将项目更新为6.7.1。
4.  尝试使用《[故障排除指南》]()运行项目并调试所有错误。

## [](#changes_6.7)[从6.6升级](#changes_6.7)

### [](#potential_breaking_changes)[潜在的重大变化](#potential_breaking_changes)

#### [](#buildsrc_can_now_see_included_builds_from_the_root)[现在，buildSrc可以从根目录查看包含的构建](#buildsrc_can_now_see_included_builds_from_the_root)

以前，`buildSrc`以这样的方式构建，即从根构建中忽略包含构建。

从Gradle 6.7开始，`buildSrc`可以从根版本中看到任何包含的版本。这可能会导致从包含的内置项中替换依赖项`buildSrc`。如果所需的包含的构建，这也可能会更改执行某些构建的顺序`buildSrc`。

#### [](#updates_to_default_tool_integration_versions)[更新到默认工具集成版本](#updates_to_default_tool_integration_versions)

* PMD已更新至[PMD 6.26.0](https://github.com/pmd/pmd/releases/tag/pmd_releases%2F6.26.0)。
* Checkstyle已更新至[Checkstyle 8.35](https://checkstyle.sourceforge.io/releasenotes.html#Release_8.35)。
* CodeNarc已更新为[CodeNarc 1.6.1](https://github.com/CodeNarc/CodeNarc/blob/v1.6.1/CHANGELOG.md)。

### [](#deprecations)[弃用](#deprecations)

#### [](#changing_default_excludes_during_the_execution_phase)[在执行阶段更改默认值排除](#changing_default_excludes_during_the_execution_phase)

为了方便起见，Gradle的文件树应用了一些默认的排除模式-实际上与Ant相同。有关更多信息，请参见[用户手册]()。有时，Ant的默认排除会证明存在问题，例如，当您想将其包括`.gitignore`在存档文件中时。

在执行阶段更改Gradle的默认排除项可能导致最新检查的正确性问题，因此不建议使用。仅允许您在设置脚本中更改Gradle的默认排除项，有关示例，请参见[用户手册]()。

#### [](#using_a_configuration_directly_as_a_dependency)[直接使用配置作为依赖项](#using_a_configuration_directly_as_a_dependency)

Gradle允许将的实例`Configuration`直接用作依赖项：
```java
dependencies {
    implementation(configurations.myConfiguration)
}

```

现在不赞成使用此行为，因为它令人困惑：有时候期待 "依赖性配置 "首先被解析，并将解析结果作为依赖性添加到包括配置中，但事实并非如此。不推荐使用的版本可以替换为实际行为，即配置继承：

```java
configurations.implementation.extendsFrom(configurations.myConfiguration)
```


## [](#changes_6.6)[从6.5升级](#changes_6.6)

### [](#potential_breaking_changes_2)[潜在的重大变化](#potential_breaking_changes_2)

#### [](#updates_to_bundled_gradle_dependencies)[更新捆绑的Gradle依赖项](#updates_to_bundled_gradle_dependencies)

* Ant已更新至[1.10.8](https://downloads.apache.org/ant/RELEASE-NOTES-1.10.8.html)。
* Groovy已更新为[Groovy 2.5.12](https://groovy-lang.org/changelogs/changelog-2.5.12.html)。

#### [](#dependency_substitutions_and_variant_aware_dependency_resolution)[依赖替换和变体感知依赖解析](#dependency_substitutions_and_variant_aware_dependency_resolution)

在添加支持以表示依赖替换中的[变体支持的同时]()，错误修复引入了某些构建可能依赖的行为更改。以前，替换的依赖项仍将使用原始选择器的[属性]()，而不是替换选择器的[属性]()。

有了这一更改，用丰富的选择器围绕依赖项进行的现有替换（例如平台依赖项）将不再像以前那样起作用。必须在目标选择器中定义变量识别部分。

如果发生以下情况，您可能会受到此更改的影响：

* 对平台有依赖性，例如`implementation platform("org:platform:1.0")`
* 指定依赖项的属性，
* 在这些依赖项上使用[解析规则]()。

如果您受到影响，请参阅[文档]()以解决问题。

### [](#deprecations_2)[弃用](#deprecations_2)

Gradle 6.6中未弃用。

## [](#changes_6.5)[从6.4升级](#changes_6.5)

### [](#potential_breaking_changes_3)[潜在的重大变化](#potential_breaking_changes_3)

#### [](#updates_to_bundled_gradle_dependencies_2)[更新捆绑的Gradle依赖项](#updates_to_bundled_gradle_dependencies_2)

* Kotlin已更新至[Kotlin 1.3.72](https://github.com/JetBrains/kotlin/releases/tag/v1.3.72)。
* Groovy已更新为[Groovy 2.5.11](https://groovy-lang.org/changelogs/changelog-2.5.11.html)。

#### [](#updates_to_default_tool_integration_versions_2)[更新到默认工具集成版本](#updates_to_default_tool_integration_versions_2)

* PMD已更新至[PMD 6.23.0](https://github.com/pmd/pmd/releases/tag/pmd_releases%2F6.23.0)。

### [](#deprecations_3)[弃用](#deprecations_3)

#### [](#abstract_task_deprecated)[内部类AbstractTask已弃用](#abstract_task_deprecated)

`AbstractTask`是一个内部类，在公共API上可见，作为public type的超类`DefaultTask`。`AbstractTask`将在Gradle 7.0中删除，而在Gradle 6.5中不推荐使用以下内容：

* 注册类型为`AbstractTask`或的任务`TaskInternal`。您可以从任务注册中删除任务类型，而Gradle将`DefaultTask`代替使用。
* 注册类型为的子类`AbstractTask`但不是的子类的任务`DefaultTask`。您可以更改任务类型以扩展`DefaultTask`。
* 使用`AbstractTask`插件代码或构建脚本中的类。您可以更改代码以`DefaultTask`代替使用。

## [](#changes_6.4)[从6.3升级](#changes_6.4)

### [](#potential_breaking_changes_4)[潜在的重大变化](#potential_breaking_changes_4)

#### [](#upgrade:pmd_expects_6)[PMD插件默认需要PMD 6.0.0或更高版本](#upgrade:pmd_expects_6)

Gradle 6.4默认情况下启用了增量分析。增量分析仅在PMD 6.0.0或更高版本中可用。如果要使用较旧的PMD版本，则需要禁用增量分析：
```java


pmd {
    incrementalAnalysis = false
}


```


#### [](#changes_in_dependency_locking)[依赖项锁定的更改](#changes_in_dependency_locking)

使用Gradle 6.4，用于[依赖项锁定`LockMode`]()的孵化API已更改。现在通过a设置值，`Property<LockMode>`而不是直接设置器。这意味着必须为Kotlin DSL更新设置值的表示法：
```java


dependencyLocking {
    lockMode.set(LockMode.STRICT)
}


```


Groovy DSL的用户不会受到影响，因为该符号`lockMode = LockMode.STRICT`仍然有效。

#### [](#java_versions_in_published_metadata)[发布的元数据中的Java版本](#java_versions_in_published_metadata)

如果Java库与Gradle Module元数据一起发布，则它支持的Java版本信息将编码在`org.gradle.jvm.version`属性中。默认情况下，此属性设置为您在中配置的属性`java.targetCompatibility`。如果未配置，则将其设置为运行Gradle的当前Java版本。例如，更改特定编译任务的版本`javaCompile.targetCompatibility`对该属性没有影响，如果未手动调整该属性，则会导致错误信息。现在，此问题已修复，并且属性默认为与构建发布jar的源相关联的编译任务的设置。

#### [](#ivy_repositories_with_custom_layouts)[具有自定义布局的常春藤存储库](#ivy_repositories_with_custom_layouts)

在具有自定义存储库布局的Ivy存储库上发布时，包括的6.0至6.3.x的Gradle版本可能会生成错误的Gradle模块元数据。从6.4开始，如果Gradle检测到您正在使用自定义存储库布局，它将不再发布Gradle模块元数据。

#### [](#new_properties_may_shadow_variables_in_build_scripts)[新属性可能会遮盖构建脚本中的变量](#new_properties_may_shadow_variables_in_build_scripts)

此版本引入了一些新特性- ， ，`mainClass` \-在不同的地方。由于这些是非常通用的名称，因此您有可能在构建脚本中使用其中之一作为变量名称。然后，新属性可能会以不希望的方式遮盖您的变量中的一个，从而导致构建失败，在该构建中，访问属性而不是使用具有相同名称的局部变量。您可以通过在构建脚本中重命名相应的变量来修复它。`mainModule``modularity`

受影响的是内部配置码`application {}`和`java {}`配置块，一个Java执行设置与内部`project.javaexec {}`，和内部的各种任务配置（`JavaExec`，`CreateStartScripts`，`JavaCompile`，`Test`，`Javadoc`）。

#### [](#updates_to_bundled_gradle_dependencies_3)[更新捆绑的Gradle依赖项](#updates_to_bundled_gradle_dependencies_3)

* Kotlin已更新至[Kotlin 1.3.71](https://github.com/JetBrains/kotlin/releases/tag/v1.3.71)。

### [](#deprecations_4)[弃用](#deprecations_4)

Gradle 6.3和6.4之间没有弃用。

## [](#changes_6.3)[从6.2升级](#changes_6.3)

### [](#potential_breaking_changes_5)[潜在的重大变化](#potential_breaking_changes_5)

#### [](#fewer_dependencies_available_in_idea)[IDEA中可用的依赖项更少](#fewer_dependencies_available_in_idea)

Gradle不再将注释处理器类路径包括为IDEA中提供的依赖项。IDEA在编译时看到的依赖性与Gradle在解决编译类路径（名为的配置`compileClasspath`）后看到的依赖性相同。这样可以防止注释处理器依赖项泄漏到项目代码中。

在Gradle引入[增量注释处理支持之前]()，IDEA要求所有注释处理器都在编译类路径上，以便在IDEA中进行编译时能够运行注释处理。这不再是必需的，因为Gradle具有单独的[注释处理器classpath]()。导入带有注释处理器的Gradle项目时，注释处理器的依赖项不会添加到IDEA模块的类路径中。

#### [](#updates_to_bundled_gradle_dependencies_4)[更新捆绑的Gradle依赖项](#updates_to_bundled_gradle_dependencies_4)

* Kotlin已更新至[Kotlin 1.3.70](https://blog.jetbrains.com/kotlin/2020/03/kotlin-1-3-70-released/)。
* Groovy已更新至[Groovy 2.5.10](http://groovy-lang.org/changelogs/changelog-2.5.10.html)。

#### [](#updates_to_default_tool_integration_versions_3)[更新到默认工具集成版本](#updates_to_default_tool_integration_versions_3)

* PMD已更新至[PMD 6.21.0](https://pmd.github.io/pmd-6.21.0/pmd_release_notes.html#24-january-2020---6210)。
* CodeNarc已更新为[CodeNarc 1.5](https://github.com/CodeNarc/CodeNarc/blob/v1.5/CHANGELOG.md#version-15----nov-2019)。

#### [](#rich_console_support_removed_for_some_32_bit_operating_systems)[对某些32位操作系统删除了丰富的控制台支持](#rich_console_support_removed_for_some_32_bit_operating_systems)

对于32位Unix系统和旧的FreeBSD版本（早于FreeBSD 10），Gradle 6.3不支持[丰富的控制台]()。Microsoft Windows 32位不受影响。

Gradle将继续在32位系统上构建项目，但不再显示丰富的控制台。

### [](#deprecations_5)[弃用](#deprecations_5)

#### [](#using_default_and_archives_configurations)[使用默认配置和存档配置](#using_default_and_archives_configurations)

几乎每个Gradle项目都有_默认_配置和_存档_配置，这些配置是由_基本_插件添加的。这些配置不再在使用[变体感知依赖管理]()和[新发布插件的]()现代Gradle构建中使用。

虽然目前这些配置将保留在Gradle中以实现向后兼容，但现在不建议使用它们来声明依赖项或解析依赖项。

解决这些配置从来都不是预期的用例，仅是可能的，因为在较早的Gradle版本中，_每个_配置都是可以解决的。要声明依赖关系，请使用您使用的插件提供的配置，例如[Java库插件]()。

## [](#changes_6.2)[从6.1升级](#changes_6.2)

### [](#potential_breaking_changes_6)[潜在的重大变化](#potential_breaking_changes_6)

#### [](#compile_and_runtime_classpath_now_request_library_variants_by_default)[现在，编译和运行时类路径默认情况下请求库变体](#compile_and_runtime_classpath_now_request_library_variants_by_default)

现在，JVM项目中的类路径显式请求该`org.gradle.category=library`属性。如果无法使用某些库，这将导致更清晰的错误消息。例如，当库不支持所需的Java版本时。实际的效果是，现在所有[平台依赖项]()都必须这样声明。以前，当`platform()`本地平台或使用Gradle Module Metadata发布的平台省略了关键字时，平台依赖性也偶然起作用。

#### [](#properties_from_project_root_gradle_properties_leaking_into_buildsrc_and_included_builds)[项目根目录中的属性`gradle.properties`泄漏到`buildSrc`包含的构建中](#properties_from_project_root_gradle_properties_leaking_into_buildsrc_and_included_builds)

Gradle 6.2和Gradle 6.2.1中进行了回归，这导致在项目根`gradle.properties`文件中设置的Gradle属性泄漏到内部`buildSrc`版本以及该根所包含的所有内部版本中。

如果该`buildSrc`构建或包含的构建突然发现来自项目根`gradle.properties`文件的属性具有意外或不兼容的值，则可能导致构建开始失败。

回归已在Gradle 6.2.2中修复。

### [](#deprecations_6)[弃用](#deprecations_6)

Gradle 6.1和6.2之间没有弃用。

## [](#changes_6.1)[从6.0或更早版本升级](#changes_6.1)

### [](#deprecations_7)[弃用](#deprecations_7)

#### [](#querying_a_mapped_output_property_of_a_task_before_the_task_has_completed)[在任务完成之前查询任务的映射输出属性](#querying_a_mapped_output_property_of_a_task_before_the_task_has_completed)

在任务完成之前查询映射的输出属性的值可能会导致奇怪的构建失败，因为这表明过时或不存在的输出可能会被错误使用。此行为已弃用，并将发出弃用警告。这将成为Gradle 7.0中的错误。

下面的示例演示了此问题，其中在生产者执行之前分析了生产者的输出文件：
```java


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


```

`consumer.threadPoolSize`如果在完成之前进行查询，则查询的值将产生弃用警告`producer`，因为尚未生成输出文件。

#### [](#discontinued_methods)[停产方法](#discontinued_methods)

以下方法已终止，不应再使用。它们将在Gradle 7.0中删除。

* `BasePluginConvention.setProject(ProjectInternal)`
* `BasePluginConvention.getProject()`
* `StartParameter.useEmptySettings()`
* `StartParameter.isUseEmptySettings()`

#### [](#upgrading_jvm_plugins)[备用JVM插件（也称为“软件模型”）](#upgrading_jvm_plugins)

Gradle 2.x中引入了一组用于Java和Scala开发的替代插件，作为基于“软件模型”的实验。这些插件现已弃用，最终将被删除。如果你还在使用这些老的插件之一（`java-lang`，`scala-lang`，`jvm-component`，`jvm-resources`，`junit-test-suite`）请查阅文档[构建Java和JVM的项目]()，以确定其稳定的JVM插件是适合您的项目。

### [](#potential_breaking_changes_7)[潜在的重大变化](#potential_breaking_changes_7)

#### [](#projectlayout_is_no_longer_available_to_worker_actions_as_a_service)[`ProjectLayout`不再可用于工作者作为服务的行为](#projectlayout_is_no_longer_available_to_worker_actions_as_a_service)

在Gradle 6.0中，`ProjectLayout`通过服务注入使该服务可用于工作人员操作。该服务允许可变状态泄漏到工作程序动作中，并为工作程序动作中未声明的依赖项提供了一种方法。

`ProjectLayout`已从可用服务中删除。正在使用的工作程序动作`ProjectLayout`应改为注入`projectDirectory`或`buildDirectory`作为参数。

#### [](#updates_to_bundled_gradle_dependencies_5)[更新捆绑的Gradle依赖项](#updates_to_bundled_gradle_dependencies_5)

* Kotlin已更新至[Kotlin 1.3.61](https://blog.jetbrains.com/kotlin/2019/11/kotlin-1-3-60-released/)。

#### [](#updates_to_default_tool_integration_versions_4)[更新到默认工具集成版本](#updates_to_default_tool_integration_versions_4)

* Checkstyle已更新至[Checkstyle 8.27](https://checkstyle.org/releasenotes.html#Release_8.27)。
* PMD已更新至[PMD 6.20.0](https://pmd.github.io/pmd-6.20.0/pmd_release_notes.html#29-november-2019---6200)。

#### [](#publishing_spring_boot_applications)[发布Spring Boot应用程序](#publishing_spring_boot_applications)

从Gradle 6.2开始，Gradle会在上传之前执行健全性检查，以确保您没有上传陈旧的文件（由另一个构建生成的文件）。这会导致使用该`components.java`组件上传的Spring Boot应用程序出现问题：


```java


Artifact my-application-0.0.1-SNAPSHOT.jar wasn't produced by this build.


```

这是由于主要`jar`任务已被Spring Boot应用程序禁用，并且组件希望该任务存在。因为默认情况下该`bootJar`任务使用_与_主`jar`任务_相同的文件_，所以Gradle的早期版本将：

* 发布陈旧的`bootJar`工件
* 或如果`bootJar`以前未调用过任务则失败

一种解决方法是告诉Gradle上传什么。如果要上传`bootJar`，则需要配置传出配置以执行此操作：
```java


configurations {
   [apiElements, runtimeElements].each {
       it.outgoing.artifacts.removeIf { it.buildDependencies.getDependencies(null).contains(jar) }
       it.outgoing.artifact(bootJar)
   }
}


```



或者，您可能想重新启用`jar`任务，并`bootJar`使用其他分类器添加。
```java

jar {
   enabled = true
}

bootJar {
   classifier = 'application'
}

```