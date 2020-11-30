# 了解Gradle模块元数据


内容

  * [与其他格式的映射](#sub:mapping-with-other-formats)
  * [与其他构建工具的交互](#sub:interactions-other-build-tools)
  * [Gradle模块元数据验证](#sub:gmm-validation)
  * [使Gradle模块元数据可重现](#sub:gmm-reproducible)
  * [禁用Gradle模块元数据发布](#sub:disabling-gmm-publication)

Gradle模块元数据是用于序列化Gradle组件模型的格式。它类似于[Apache Maven™的POM文件](https://maven.apache.org/pom.html)
或[Apache Ivy™ivy.xml](http://ant.apache.org/ivy/)文件。元数据文件的目标是向 _消费者_ 提供存储库中发布内容的合理模型。

Gradle模块元数据是一种独特的格式，旨在通过使其具有多平台和变体感知功能来提高依赖性解析。

特别是，Gradle模块元数据支持：

  * [丰富的版本限制](/md/声明丰富版本.md#rich-version-constraints)

  * [依赖约束](/md/升级传递依赖的版本.md#sec:adding-constraints-transitive-deps)

  * [组件功能](/md/声明Library的能力.md#declaring-component-capabilities)

  * [变体感知分辨率](/md/使用变体属性.md#understanding-variant-selection)

发布Gradle模块元数据将为您的使用者提供更好的依赖关系管理：

  * 通过检测[不兼容的模块来](/md/声明Library的能力.md#declaring-component-capabilities)尽早发现问题[](/md/声明Library的能力.md#declaring-component-capabilities)

  * 一致选择[特定于平台的依赖项](/md/在项目之间共享输出.md#targeting-different-platforms)

  * 本机[依赖版本对齐](/md/对齐依赖版本.md#version_alignment)

  * 自动获取[库](/md/建模功能变体和可选依赖项.md)特定[功能的](/md/建模功能变体和可选依赖项.md)依赖关系[](/md/建模功能变体和可选依赖项.md)

使用[Maven Publish插件](/md/Maven发布插件.md)或[Ivy Publish插件](/md/Ivy发布插件.md)时，Gradle模块元数据会自动[发布](/md/Ivy发布插件.md)。它
_不_ 支持传统`maven`和`ivy`插件。

可以在[此处](https://github.com/gradle/gradle/blob/master/subprojects/docs/src/docs/design/gradle-module-metadata-latest-specification.md)找到Gradle模块元数据规范的规范。

<h2 id = '#sub:mapping-with-other-formats'> <a href = '#sub:mapping-with-other-formats'>与其他格式的映射</a> </h2>

Gradle模块元数据会自动发布在Maven或Ivy存储库中。但是，它不会替代 _pom.xml_ 或 _ivy.xml_
文件：它与这些文件一起发布。这样做是为了最大程度地提高与第三方构建工具的兼容性。

Gradle尽其所能将Gradle特定的概念映射到Maven或Ivy。当构建文件使用只能在Gradle模块元数据中表示的功能时，Gradle会在发布时警告您。下表总结了如何将某些Gradle特定功能映射到Maven和Ivy：

表1. Gradle特定概念到Maven和Ivy的映射 Gradle | 马文 | Ivy | 描述  
---|---  |---|---    
[依赖约束](/md/升级传递依赖的版本.md#sec:adding-constraints-transitive-deps)|`<dependencyManagement>` 依存关系|未发表|Gradle依赖项约束是可 _传递的_ ，而Maven的依赖项管理块 _不是_  
[丰富的版本限制](/md/声明丰富版本.md#rich-version-constraints)|发布 _需求_ 版本|发布了 _需求_ 版本|  
[组件功能](/md/声明Library的能力.md#declaring-component-capabilities)|未发表|未发表|组件功能是Gradle独有的  
[功能变体](/md/建模功能变体和可选依赖项.md)|上载变异工件，依赖性发布为_optional依赖性|已上传变体工件，未发布依赖项|功能变体很好地替代了可选的依赖项  
[自定义组件类型](/md/定制发布.md#sec:publishing-custom-components)|工件已上传，依赖项是映射所描述的依赖项|工件已上传，相关性被忽略|在任何情况下，自定义组件类型都可能无法从Maven或Ivy中使用。它们通常存在于自定义生态系统中。  
  
<h3 id = '#disabling_metadata_compatibility_publication_warnings'> <a href = '#disabling_metadata_compatibility_publication_warnings'>禁用元数据兼容性发布警告</a> </h3>

如果要禁止显示警告，可以使用以下API来禁止显示：

  * 对于Maven，请参见[MavenPublication中](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPublication.html#org.gradle.api.publish.maven.MavenPublication:suppressAllPomMetadataWarnings\(\))的`suppress*`方法。[](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPublication.html#org.gradle.api.publish.maven.MavenPublication:suppressAllPomMetadataWarnings\(\))

  * 对于Ivy，请参见[IvyPublication中](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyPublication.html#org.gradle.api.publish.ivy.IvyPublication:suppressAllIvyMetadataWarnings\(\))的`suppress*`方法[](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyPublication.html#org.gradle.api.publish.ivy.IvyPublication:suppressAllIvyMetadataWarnings\(\))

例子1.禁用发布警告

`Groovy``Kotlin`

build.gradle

    
    
        publications {
            maven(MavenPublication) {
                from components.java
                suppressPomMetadataWarningsFor('runtimeElements')
            }
        }

build.gradle.kts

    
    
        publications {
            register<MavenPublication>("maven") {
                from(components["java"])
                suppressPomMetadataWarningsFor("runtimeElements")
            }
        }

<h2 id = '#sub:interactions-other-build-tools'> <a href = '#sub:interactions-other-build-tools'>与其他构建工具的交互</a> </h2>

由于Gradle模块元数据并未广泛传播，并且其目的是[最大程度地提高与其他工具的兼容性](#sub:mapping-with-other-formats)，因此Gradle做以下几件事：

  * Gradle模块元数据与给定存储库（Maven或Ivy）的常规描述符一起系统发布

  * 的`pom.xml`或`ivy.xml`文件将包含一个 _标记评论_ 它告诉该Gradle模块Gradle元数据存在该模块

标记的目的 _不是_ 其他工具来解析模块元数据：仅用于Gradle用户。它向Gradle解释说存在 _更好的_
模块元数据文件，并且应该使用它。这并不意味着Maven或Ivy的消耗也将被打破，仅意味着它可以在[降级模式下工作](#sub:mapping-with-other-formats)。

╔═════════════════════════════  

必须将其视为一种 _性能优化_ ：不必执行2个网络请求，而是先获取Gradle模块元数据，然后再获取一个POM /
Ivy文件，而Gradle将首先查看该文件中最重要的文件。可能存在，则仅在模块与Gradle Module Metadata实际发布时才执行第二个请求。  
  
╚═════════════════════════════    
  
如果您知道依赖的模块始终与Gradle Module Metadata一起发布，则可以优化网络调用，为存储库配置元数据源：

示例2.仅解决Gradle模块元数据

`Groovy``Kotlin`

build.gradle

    
    
        repositories {
            maven {
                url "http://repo.mycompany.com/repo"
                metadataSources {
                    gradleMetadata()
                }
            }
        }

build.gradle.kts

    
    
        repositories {
            maven {
                setUrl("http://repo.mycompany.com/repo")
                metadataSources {
                    gradleMetadata()
                }
            }
        }

<h2 id = '#sub:gmm-validation'> <a href = '#sub:gmm-validation'>Gradle模块元数据验证</a> </h2>

Gradle模块元数据在发布之前先经过验证。

强制执行以下规则：

  * 变体名称必须唯一，

  * 每个变体必须至少具有[一个属性](/md/了解变体选择.md)，

  * 两个变体不能具有[完全相同的属性和功能](/md/使用变体属性.md)，

  * 如果存在依赖性，则所有变体中的至少一个必须携带[版本信息](/md/声明丰富版本.md)。

这些规则可确保生成的元数据的质量，并有助于确认使用不会有问题。

<h2 id = '#sub:gmm-reproducible'> <a href = '#sub:gmm-reproducible'>使Gradle模块元数据可重现</a> </h2>

默认情况下，Gradle模块元数据文件包含生成它的构建中的唯一ID。这意味着文件将始终是不同的。

用户可以选择在其禁用以下唯一标识符`publication`：

例子3.配置发布的构建标识符

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        publications {
            main(MavenPublication) {
                from components.java
                withoutBuildIdentifier()
            }
        }
    }

build.gradle.kts

    
    
    publishing {
        publications {
            create<MavenPublication>("main") {
                from(components["java"])
                withoutBuildIdentifier()
            }
        }
    }

通过上述更改，生成的Gradle Module元数据文件将始终相同，从而使下游任务可以将其视为最新文件。

╔═════════════════════════════  

`UP-TO-
DATE`由于实现方式，生成模块元数据文件的任务目前从未被Gradle标记。但是，如果跳过了构建标识符，并且未更改构建输入或构建脚本，则该任务实际上是最新的（它始终产生相同的输出）。  
  
╚═════════════════════════════    
  
<h2 id = '#sub:disabling-gmm-publication'> <a href = '#sub:disabling-gmm-publication'>禁用Gradle模块元数据发布</a> </h2>

在某些情况下，您可能希望禁用Gradle模块元数据的发布：

  * 您要上传到的存储库拒绝元数据文件（未知格式）

  * 您正在使用未正确映射到Gradle模块元数据的Maven或Ivy特定概念

在这种情况下，只需禁用生成元数据文件的任务即可禁用Gradle Module元数据的发布：

例子4.禁用发布Gradle模块元数据

`Groovy``Kotlin`

build.gradle

    
    
        tasks.withType(GenerateModuleMetadata) {
            enabled = false
        }

build.gradle.kts

    
    
        tasks.withType<GenerateModuleMetadata> {
            enabled = false
        }

