# Ivy发布插件


内容

  * [用法](#%E7%94%A8%E6%B3%95)
  * [任务](#%E4%BB%BB%E5%8A%A1)
  * [刊物](#%E5%88%8A%E7%89%A9)
  * [储存库](#%E5%82%A8%E5%AD%98%E5%BA%93)
  * [完整的例子](#%E5%AE%8C%E6%95%B4%E7%9A%84%E4%BE%8B%E5%AD%90)
  * [从旧版Ivy出版物迁移](#%E4%BB%8E%E6%97%A7%E7%89%88Ivy%E5%87%BA%E7%89%88%E7%89%A9%E8%BF%81%E7%A7%BB)

Ivy Publish插件提供了以[Apache Ivy](http://ant.apache.org/ivy/)格式发布构建工件的功能，通常将其发布到存储库以供其他构建或项目使用。发布的内容是由构建创建的一个或多个工件，以及一个Ivy
_模块描述符_ （通常为`ivy.xml`），该描述 _符_ 描述了工件及其相关性（如果有）。

Gradle（请参阅[声明依赖项](/md/声明依赖.md#declaring-dependencies)）和其他了解Ivy格式的工具可以使用已发布的Ivy模块。您可以在[发布概述中](/md/将项目发布为模块.md#publishing_overview)了解发布的基础知识。

## [用法](#%E7%94%A8%E6%B3%95)

要使用Ivy Publish插件，请在构建脚本中包括以下内容：

例子1.应用Ivy Publish插件

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'ivy-publish'
    }

build.gradle.kts

    
    
    plugins {
        `ivy-publish`
    }

Ivy
Publish插件在项目上使用扩展名`publishing`，类型为[PublishingExtension](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.PublishingExtension.html)。此扩展提供了一个命名出版物的容器和一个命名存储库的容器。Ivy
Publish插件可与[IvyPublication](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyPublication.html)出版物和[IvyArtifactRepository](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.repositories.IvyArtifactRepository.html)仓库一起使用。

## [任务](#%E4%BB%BB%E5%8A%A1)

`generateDescriptorFileFor _PubName_ Publication`—
[GenerateIvyDescriptor](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.tasks.GenerateIvyDescriptor.html)

    

为名为 _PubName_ 的发布创建一个Ivy描述符文件，填充已知的元数据，例如项目名称，项目版本和依赖项。描述符文件的默认位置是 _build /
publications / $ pubName / ivy.xml_ 。

`publish _PubName_ PublicationTo _RepoName_ Repository`—
[PublishToIvyRepository](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.tasks.PublishToIvyRepository.html)

    

将 _PubName_ 发布发布到名为 _RepoName_ 的存储库。如果您具有没有明确名称的存储库定义，则 _RepoName_ 将为“ Ivy”。

`publish`

    

_取决于_ ：所有任务`publish _PubName_ PublicationTo _RepoName_ Repository`

将所有定义的发布发布到所有定义的存储库的聚合任务。

## [刊物](#%E5%88%8A%E7%89%A9)

此插件提供[出版物](/md/依赖管理术语.md#sub:terminology_publication)类型的[IvyPublication](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyPublication.html)。要了解如何定义和使用出版物，请参见[基本出版](/md/将项目发布为模块.md#sec:basic_publishing)部分。

您可以在Ivy发布中配置以下四项主要内容：

  * 一个[组件](/md/依赖管理术语.md#sub:terminology_component)—通过[IvyPublication.from（org.gradle.api.component.SoftwareComponent）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyPublication.html#org.gradle.api.publish.ivy.IvyPublication:from\(org.gradle.api.component.SoftwareComponent\))。

  * [自定义工件](/md/定制发布.md#sec:publishing_custom_artifacts_to_maven)-通过[IvyPublication.artifact（java.lang.Object）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyPublication.html#org.gradle.api.publish.ivy.IvyPublication:artifact\(java.lang.Object\))方法。有关自定义Ivy工件的可用配置选项，请参见[IvyArtifact](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyArtifact.html)。

  * 标准元数据一样`module`，`organisation`和`revision`。

  * 模块描述符的其他内容-通过[IvyPublication.descriptor（org.gradle.api.Action）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyPublication.html#org.gradle.api.publish.ivy.IvyPublication:descriptor\(org.gradle.api.Action\))。

您可以在[完整的发布示例中](#%E5%AE%8C%E6%95%B4%E7%9A%84%E4%BE%8B%E5%AD%90)看到所有这些内容。的API文档`IvyPublication`还有其他代码示例。

### [已发布项目的标识值](#%E5%B7%B2%E5%8F%91%E5%B8%83%E9%A1%B9%E7%9B%AE%E7%9A%84%E6%A0%87%E8%AF%86%E5%80%BC)

生成的Ivy模块描述符文件包含一个`<info>`标识模块的元素。默认标识值从以下派生：

  * `organisation`\- [Project.getGroup（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:group)

  * `module`\- [Project.getName（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:name)

  * `revision`\- [Project.getVersion（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:version)

  * `status`\- [Project.getStatus（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:status)

  * `branch` \- （没有设置）

覆盖默认标识值非常简单：只需指定`organisation`，`module`或`revision`配置属性时[IvyPublication](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyPublication.html)。`status`并`branch`可以通过`descriptor`属性设置-
请参见[IvyModuleDescriptorSpec](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyModuleDescriptorSpec.html)。

该`descriptor`属性还可以用于添加其他自定义元素作为该`<info>`元素的子元素，如下所示：

例子2.定制发布标识

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        publications {
            ivy(IvyPublication) {
                organisation = 'org.gradle.sample'
                module = 'project1-sample'
                revision = '1.1'
                descriptor.status = 'milestone'
                descriptor.branch = 'testing'
                descriptor.extraInfo 'http://my.namespace', 'myElement', 'Some value'
    
                from components.java
            }
        }
    }

build.gradle.kts

    
    
    publishing {
        publications {
            create<IvyPublication>("ivy") {
                organisation = "org.gradle.sample"
                module = "project1-sample"
                revision = "1.1"
                descriptor.status = "milestone"
                descriptor.branch = "testing"
                descriptor.extraInfo("http://my.namespace", "myElement", "Some value")
    
                from(components["java"])
            }
        }
    }

╔═════════════════════════════  

某些存储库无法处理所有受支持的字符。例如，`:`当发布到Windows上由文件系统支持的存储库时，该字符不能用作标识符。  
  
╚═════════════════════════════    
  
Gradle将处理任何有效的Unicode字符`organisation`，`module`和`revision`（以及神器的`name`，`extension`和`classifier`）。明确禁止的唯一值是`\`，`/`以及任何ISO控制字符。提供的值在发布过程的早期就得到了验证。

### [自定义生成的模块描述符](#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%94%9F%E6%88%90%E7%9A%84%E6%A8%A1%E5%9D%97%E6%8F%8F%E8%BF%B0%E7%AC%A6)

有时，从项目信息生成的模块描述符文件将需要在发布之前进行调整。Ivy Publish插件为此提供了DSL。请参阅《
DSL参考》中的[IvyModuleDescriptorSpec](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyModuleDescriptorSpec.html)，以获取可用属性和方法的完整文档。

下面的示例演示如何使用DSL最常见的方面：

例子3.定制模块描述符文件

`Groovy``Kotlin`

build.gradle

    
    
        publications {
            ivyCustom(IvyPublication) {
                descriptor {
                    license {
                        name = 'The Apache License, Version 2.0'
                        url = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                    author {
                        name = 'Jane Doe'
                        url = 'http://example.com/users/jane'
                    }
                    description {
                        text = 'A concise description of my library'
                        homepage = 'http://www.example.com/library'
                    }
                }
                versionMapping {
                    usage('java-api') {
                        fromResolutionOf('runtimeClasspath')
                    }
                    usage('java-runtime') {
                        fromResolutionResult()
                    }
                }
            }
        }

build.gradle.kts

    
    
        publications {
            create<IvyPublication>("ivyCustom") {
                descriptor {
                    license {
                        name.set("The Apache License, Version 2.0")
                        url.set("http://www.apache.org/licenses/LICENSE-2.0.txt")
                    }
                    author {
                        name.set("Jane Doe")
                        url.set("http://example.com/users/jane")
                    }
                    description {
                        text.set("A concise description of my library")
                        homepage.set("http://www.example.com/library")
                    }
                }
                versionMapping {
                    usage("java-api") {
                        fromResolutionOf("runtimeClasspath")
                    }
                    usage("java-runtime") {
                        fromResolutionResult()
                    }
                }
            }
        }

在此示例中，我们只是在生成的Ivy依赖项描述符中添加了一个'description'元素，但是该钩子允许您修改生成的描述符的任何方面。例如，您可以将依赖关系的版本范围替换为用于生成内部版本的实际版本。

您还可以通过[IvyModuleDescriptorSpec.withXml（org.gradle.api.Action）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.ivy.IvyModuleDescriptorSpec.html#org.gradle.api.publish.ivy.IvyModuleDescriptorSpec:withXml\(org.gradle.api.Action\))将任意XML添加到描述符文件中，但是不能使用它来修改模块标识符的任何部分（组织，模块，修订版）。

╔═════════════════════════════    
可以以不再是有效的Ivy模块描述符的方式修改描述符，因此在使用此功能时必须小心。   
╚═════════════════════════════    
  
### [自定义依赖版本](#%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BE%9D%E8%B5%96%E7%89%88%E6%9C%AC)

支持两种策略来发布依赖项：

声明的版本（默认）

    

此策略使用`dependencies`块中的依赖项声明发布由构建脚本作者定义的版本。发布不会考虑任何其他类型的处理，例如通过[更改已解决版本的规则](/md/直接自定义依赖项的解析.md)。

解决的版本

    

此策略可能会通过应用解决方案规则和自动冲突解决方案来发布在构建期间已解决的版本。这样做的好处是，已发布的版本对应于已测试发布的工件的版本。

解析版本的用例示例：

  * 项目将动态版本用于依赖项，但更喜欢将给定版本的已解析版本公开给其使用者。

  * 与[依赖项锁定](/md/锁定依赖版本.md)结合使用，您要发布锁定的版本。

  * 一个项目利用了Gradle的丰富版本约束，这些约束对Ivy进行了有损转换。它不依赖转换，而是发布已解决的版本。

这是通过使用`versionMapping`DSL方法完成的，该方法允许配置[VersionMappingStrategy](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/publish/VersionMappingStrategy.html)：

例子4.使用解决的版本

`Groovy``Kotlin`

build.gradle

    
    
        publications {
            ivyCustom(IvyPublication) {
                versionMapping {
                    usage('java-api') {
                        fromResolutionOf('runtimeClasspath')
                    }
                    usage('java-runtime') {
                        fromResolutionResult()
                    }
                }
            }
        }

build.gradle.kts

    
    
        publications {
            create<IvyPublication>("ivyCustom") {
                versionMapping {
                    usage("java-api") {
                        fromResolutionOf("runtimeClasspath")
                    }
                    usage("java-runtime") {
                        fromResolutionResult()
                    }
                }
            }
        }

在上面的示例中，Gradle将使用在中`runtimeClasspath`声明的依赖项上解析的版本，这些依赖项`api`已映射到`compile`Ivy的配置。Gradle还将使用在上`runtimeClasspath`声明的for依赖项上解析的版本，这些版本`implementation`映射到`runtime`Ivy的配置。
`fromResolutionResult()`指示Gradle应该使用变体`runtimeClasspath`的默认类路径，并且是的默认类路径`java-
runtime`。

## [储存库](#%E5%82%A8%E5%AD%98%E5%BA%93)

该插件提供了[IvyArtifactRepository](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.repositories.IvyArtifactRepository.html)类型的[存储库](/md/依赖管理术语.md#sub:terminology_repository)。要了解如何定义和使用存储库进行发布，请参见“[基本发布](/md/将项目发布为模块.md#sec:basic_publishing)”部分。[](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.repositories.IvyArtifactRepository.html)[](/md/将项目发布为模块.md#sec:basic_publishing)

这是定义发布存储库的简单示例：

示例5.声明要发布到的存储库

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        repositories {
            ivy {
                // change to point to your repo, e.g. http://my.org/repo
                url = "$buildDir/repo"
            }
        }
    }

build.gradle.kts

    
    
    publishing {
        repositories {
            ivy {
                // change to point to your repo, e.g. http://my.org/repo
                url = uri("$buildDir/repo")
            }
        }
    }

您将要配置的两个主要内容是存储库：

  * 网址（必填）

  * 名称（可选）

您可以定义多个存储库，只要它们在构建脚本中具有唯一的名称即可。您也可以声明一个（只有一个）没有名称的存储库。该存储库将采用隐式名称“ Ivy”。

您还可以配置连接到存储库所需的任何身份验证详细信息。有关更多详细信息，请参见[IvyArtifactRepository](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.repositories.IvyArtifactRepository.html)。

## [完整的例子](#%E5%AE%8C%E6%95%B4%E7%9A%84%E4%BE%8B%E5%AD%90)

下面的示例演示了使用多项目构建的发布。每个项目都发布一个Java组件，该Java组件配置为还构建和发布Javadoc和源代码工件。定制描述符文件以包括每个项目的项目描述。

例子6.发布一个Java模块

`Groovy``Kotlin`

settings.gradle

    
    
    rootProject.name = 'ivy-publish-java'
    include 'project1', 'project2'

buildSrc / build.gradle

    
    
    plugins {
        id 'groovy-gradle-plugin'
    }

buildSrc / src / main / groovy / myproject.publishing-conventions.gradle

    
    
    plugins {
        id 'java-library'
        id 'ivy-publish'
    }
    
    version = '1.0'
    group = 'org.gradle.sample'
    
    repositories {
        mavenCentral()
    }
    
    java {
        withJavadocJar()
        withSourcesJar()
    }
    
    publishing {
        repositories {
            ivy {
                // change to point to your repo, e.g. http://my.org/repo
                url = "${rootProject.buildDir}/repo"
            }
        }
        publications {
            ivy(IvyPublication) {
                from components.java
                descriptor.description {
                    text = providers.provider({ description })
                }
            }
        }
    }

project1 / build.gradle

    
    
    plugins {
        id 'myproject.publishing-conventions'
    }
    
    description = 'The first project'
    
    dependencies {
        implementation 'junit:junit:4.13'
        implementation project(':project2')
    }

project2 / build.gradle

    
    
    plugins {
        id 'myproject.publishing-conventions'
    }
    
    description = 'The second project'
    
    dependencies {
        implementation 'commons-collections:commons-collections:3.2.2'
    }

settings.gradle.kts

    
    
    rootProject.name = "ivy-publish-java"
    include("project1", "project2")

buildSrc/build.gradle.kts

    
    
    plugins {
        `kotlin-dsl`
    }
    
    repositories {
        gradlePluginPortal()
    }

buildSrc/src/main/kotlin/myproject.publishing-conventions.gradle.kts

    
    
    plugins {
        id("java-library")
        id("ivy-publish")
    }
    
    version = "1.0"
    group = "org.gradle.sample"
    
    repositories {
        mavenCentral()
    }
    
    java {
        withJavadocJar()
        withSourcesJar()
    }
    
    publishing {
        repositories {
            ivy {
                // change to point to your repo, e.g. http://my.org/repo
                url = uri("${rootProject.buildDir}/repo")
            }
        }
        publications {
            create<IvyPublication>("ivy") {
                from(components["java"])
                descriptor.description {
                    text.set(providers.provider({ description }))
                }
            }
        }
    }

project1/build.gradle.kts

    
    
    plugins {
        id("myproject.publishing-conventions")
    }
    
    description = "The first project"
    
    dependencies {
        implementation("junit:junit:4.13")
        implementation(project(":project2"))
    }

project2/build.gradle.kts

    
    
    plugins {
        id("myproject.publishing-conventions")
    }
    
    description = "The second project"
    
    dependencies {
        implementation("commons-collections:commons-collections:3.2.2")
    }

结果是将为每个项目发布以下工件：

  * Gradle模块元数据文件：`project1-1.0.module`。

  * Ivy模块元数据文件：`ivy-1.0.xml`。

  * Java组件的主要JAR工件：`project1-1.0.jar`。

  * Javadoc和Java组件的源JAR工件（因为我们配置了`withJavadocJar()`和`withSourcesJar()`）：`project1-1.0-javadoc.jar`，`project1-1.0-source.jar`。

## [从旧版Ivy出版物迁移](#%E4%BB%8E%E6%97%A7%E7%89%88Ivy%E5%87%BA%E7%89%88%E7%89%A9%E8%BF%81%E7%A7%BB)

如果要迁移以前依赖于[旧版发布](https://docs.gradle.org/6.7.1/userguide/artifact_management.html)支持的项目，则会发现以下两种解决方案之间的差异。

### [标记为不可传递的配置](#%E6%A0%87%E8%AE%B0%E4%B8%BA%E4%B8%8D%E5%8F%AF%E4%BC%A0%E9%80%92%E7%9A%84%E9%85%8D%E7%BD%AE)

当配置标记为时`transitive = false`，它不会映射到Ivy。

Gradle将发出警告，指出已发布此类配置。如果确实需要发布此建议，则是使用依赖级别`transitive = false`。

### [强制依赖关系未映射](#%E5%BC%BA%E5%88%B6%E4%BE%9D%E8%B5%96%E5%85%B3%E7%B3%BB%E6%9C%AA%E6%98%A0%E5%B0%84)

虽然Ivy支持[`force`依赖项](https://ant.apache.org/ivy/history/latest-milestone/ivyfile/dependency.html#_forcing_revision)的概念，但Gradle不会将其已弃用的`force`声明映射到它。

相反，建议将Gradle替换`force`为[`strictly`version](/md/声明丰富版本.md#sec:strict-version)，以提供[更好的语义](/md/降级版本并排除依赖项.md#sec:enforcing_dependency_version)并受Gradle
Module元数据格式支持。

请注意，如果您绝对需要发布强制，则仍然可以[修改产生的`ivy.xml`](#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%94%9F%E6%88%90%E7%9A%84%E6%A8%A1%E5%9D%97%E6%8F%8F%E8%BF%B0%E7%AC%A6)。

