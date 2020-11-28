# Maven发布插件


内容

  * [用法](#publishing_maven:usage)
  * [任务](#publishing_maven:tasks)
  * [刊物](#publishing_maven:publications)
  * [储存库](#publishing_maven:repositories)
  * [发布到Maven Local](#publishing_maven:install)
  * [完整的例子](#publishing_maven:complete_example)
  * [删除延迟的配置行为](#publishing_maven:deferred_configuration)

Maven发布插件提供了将构建工件发布到[Apache Maven](http://maven.apache.org/)存储库的功能。发布到Maven存储库的模块可由Maven，Gradle（请参阅[声明依赖项](/md/声明依赖.md)）和其他了解Maven存储库格式的工具使用。您可以在[发布概述中](/md/将项目发布为模块.md#publishing_overview)了解发布的基础知识。

## [](#publishing_maven:usage)[用法](#publishing_maven:usage)

要使用Maven Publish插件，请在构建脚本中包括以下内容：

示例1.应用Maven发布插件

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'maven-publish'
    }

build.gradle.kts

    
    
    plugins {
        `maven-publish`
    }

Maven
Publish插件在项目上使用扩展名`publishing`，类型为[PublishingExtension](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.PublishingExtension.html)。此扩展提供了一个命名出版物的容器和一个命名存储库的容器。Maven
Publish插件可与[MavenPublication](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPublication.html)出版物和[MavenArtifactRepository](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.repositories.MavenArtifactRepository.html)存储库一起使用。

## [](#publishing_maven:tasks)[任务](#publishing_maven:tasks)

`generatePomFileFor _PubName_ Publication`—
[GenerateMavenPom](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.tasks.GenerateMavenPom.html)

    

为名为 _PubName_ 的发布创建一个POM文件，填充已知的元数据，例如项目名称，项目版本和依赖项。POM文件的默认位置是 _build /
publications / $ pubName / pom-default.xml_ 。

`publish _PubName_ PublicationTo _RepoName_ Repository`—
[PublishToMaven资源库](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.tasks.PublishToMavenRepository.html)

    

将 _PubName_ 发布发布到名为 _RepoName_ 的存储库。如果您具有没有明确名称的存储库定义，则 _RepoName_ 将为“ Maven”。

`publish _PubName_ PublicationToMavenLocal`—
[PublishToMavenLocal](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/publish/maven/tasks/PublishToMavenLocal.html)

    

将 _PubName_ 发布以及本地发布的POM文件和其他元数据复制到本地Maven缓存中（通常 _为$ USER_HOME / .m2 /
repository）_ 。

`publish`

    

_取决于_ ：所有任务`publish _PubName_ PublicationTo _RepoName_ Repository`

将所有定义的发布发布到所有定义的存储库的聚合任务。它 _不_ 包括复制出版物本地Maven缓存。

`publishToMavenLocal`

    

_取决于_ ：所有任务`publish _PubName_ PublicationToMavenLocal`

将所有已定义的发布（包括其元数据（POM文件等））复制到本地Maven缓存中。

## [](#publishing_maven:publications)[刊物](#publishing_maven:publications)

此插件提供[出版物](/md/依赖管理术语.md#sub:terminology_publication)类型的[MavenPublication](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPublication.html)。要了解如何定义和使用出版物，请参见[基本出版](/md/将项目发布为模块.md#sec:basic_publishing)部分。

您可以在Maven发布中配置以下四项主要内容：

  * 一个[组件](/md/依赖管理术语.md#sub:terminology_component)—通过[MavenPublication.from（org.gradle.api.component.SoftwareComponent）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPublication.html#org.gradle.api.publish.maven.MavenPublication:from\(org.gradle.api.component.SoftwareComponent\))。

  * [自定义工件](/md/定制发布.md#sec:publishing_custom_artifacts_to_maven)-通过[MavenPublication.artifact（java.lang.Object）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPublication.html#org.gradle.api.publish.maven.MavenPublication:artifact\(java.lang.Object\))方法。有关自定义Maven工件的可用配置选项，请参见[MavenArtifact](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenArtifact.html)。

  * 标准元数据一样`artifactId`，`groupId`和`version`。

  * POM文件的其他内容-通过[MavenPublication.pom（org.gradle.api.Action）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPublication.html#org.gradle.api.publish.maven.MavenPublication:pom\(org.gradle.api.Action\))。

您可以在[完整的发布示例中](#publishing_maven:complete_example)看到所有这些内容。的API文档`MavenPublication`还有其他代码示例。

### [](#sec:identity_values_in_the_generated_pom)[生成的POM中的标识值](#sec:identity_values_in_the_generated_pom)

生成的POM文件的属性将包含从以下项目属性派生的标识值：

  * `groupId`\- [Project.getGroup（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:group)

  * `artifactId`\- [Project.getName（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:name)

  * `version`\- [Project.getVersion（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:version)

覆盖默认标识值非常简单：只需指定`groupId`，`artifactId`或者`version`在配置时属性[MavenPublication](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPublication.html)。

示例2.定制发布标识

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        publications {
            maven(MavenPublication) {
                groupId = 'org.gradle.sample'
                artifactId = 'library'
                version = '1.1'
    
                from components.java
            }
        }
    }

build.gradle.kts

    
    
    publishing {
        publications {
            create<MavenPublication>("maven") {
                groupId = "org.gradle.sample"
                artifactId = "library"
                version = "1.1"
    
                from(components["java"])
            }
        }
    }

╔═════════════════════════════  

某些存储库将无法处理所有受支持的字符。例如，`:`当发布到Windows上由文件系统支持的存储库时，该字符不能用作标识符。  
  
╚═════════════════════════════    
  
Maven的限制`groupId`和`artifactId`有限字符集（`[A-Za-z0-9_\\-.]+`）和Gradle实施了这一约束。对于`version`（以及工件`extension`和`classifier`属性），Gradle将处理任何有效的Unicode字符。

被明令禁止，唯一的Unicode值`\`，`/`以及任何ISO控制字符。提供的值在发布早期就得到了验证。

### [](#sec:modifying_the_generated_pom)[自定义生成的POM](#sec:modifying_the_generated_pom)

可以在发布之前自定义生成的POM文件。例如，将库发布到Maven Central时，您需要设置某些元数据。Maven发布插件为此提供了DSL。请参阅《
DSL参考》中的[MavenPom](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPom.html)，以获取可用属性和方法的完整文档。下面的示例演示如何使用最常见的方法：

例子3.定制POM文件

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        publications {
            mavenJava(MavenPublication) {
                pom {
                    name = 'My Library'
                    description = 'A concise description of my library'
                    url = 'http://www.example.com/library'
                    properties = [
                        myProp: "value",
                        "prop.with.dots": "anotherValue"
                    ]
                    licenses {
                        license {
                            name = 'The Apache License, Version 2.0'
                            url = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                        }
                    }
                    developers {
                        developer {
                            id = 'johnd'
                            name = 'John Doe'
                            email = 'john.doe@example.com'
                        }
                    }
                    scm {
                        connection = 'scm:git:git://example.com/my-library.git'
                        developerConnection = 'scm:git:ssh://example.com/my-library.git'
                        url = 'http://example.com/my-library/'
                    }
                }
            }
        }
    }

build.gradle.kts

    
    
    publishing {
        publications {
            create<MavenPublication>("mavenJava") {
                pom {
                    name.set("My Library")
                    description.set("A concise description of my library")
                    url.set("http://www.example.com/library")
                    properties.set(mapOf(
                        "myProp" to "value",
                        "prop.with.dots" to "anotherValue"
                    ))
                    licenses {
                        license {
                            name.set("The Apache License, Version 2.0")
                            url.set("http://www.apache.org/licenses/LICENSE-2.0.txt")
                        }
                    }
                    developers {
                        developer {
                            id.set("johnd")
                            name.set("John Doe")
                            email.set("john.doe@example.com")
                        }
                    }
                    scm {
                        connection.set("scm:git:git://example.com/my-library.git")
                        developerConnection.set("scm:git:ssh://example.com/my-library.git")
                        url.set("http://example.com/my-library/")
                    }
                }
            }
        }
    }

### [](#publishing_maven:resolved_dependencies)[自定义依赖版本](#publishing_maven:resolved_dependencies)

支持两种策略来发布依赖项：

声明的版本（默认）

    

此策略使用`dependencies`块中的依赖项声明发布由构建脚本作者定义的版本。发布不会考虑任何其他类型的处理，例如通过[更改已解决版本的规则](/md/直接自定义依赖项的解析.md)。

解决的版本

    

此策略可能会通过应用解决方案规则和自动冲突解决方案来发布在构建期间已解决的版本。这样做的好处是，已发布的版本对应于已测试发布的工件的版本。

解析版本的用例示例：

  * 项目将动态版本用于依赖项，但更喜欢将给定版本的已解析版本公开给其使用者。

  * 与[依赖项锁定](/md/锁定依赖版本.md)结合使用，您要发布锁定的版本。

  * 一个项目利用了Gradle的丰富版本约束，这些约束对Maven进行了有损转换。它不依赖转换，而是发布已解决的版本。

这是通过使用`versionMapping`DSL方法完成的，该方法允许配置[VersionMappingStrategy](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/publish/VersionMappingStrategy.html)：

例子4.使用解决的版本

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        publications {
            mavenJava(MavenPublication) {
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
    }

build.gradle.kts

    
    
    publishing {
        publications {
            create<MavenPublication>("mavenJava") {
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
    }

在上面的示例中，Gradle将使用在中`runtimeClasspath`声明的依赖项上解析的版本，这些依赖项`api`已映射到`compile`Maven的范围。Gradle还将使用在中`runtimeClasspath`声明的依赖项上解析的版本`implementation`，这些版本被映射到`runtime`Maven的范围。
`fromResolutionResult()`指示Gradle应该使用变体`runtimeClasspath`的默认类路径，并且是的默认类路径`java-
runtime`。

## [](#publishing_maven:repositories)[储存库](#publishing_maven:repositories)

该插件提供了[MavenArtifactRepository](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.repositories.MavenArtifactRepository.html)类型的[存储库](/md/依赖管理术语.md#sub:terminology_repository)。要了解如何定义和使用存储库进行发布，请参见“[基本发布](/md/将项目发布为模块.md#sec:basic_publishing)”部分。[](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.repositories.MavenArtifactRepository.html)[](/md/将项目发布为模块.md#sec:basic_publishing)

这是定义发布存储库的简单示例：

示例5.声明要发布到的存储库

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        repositories {
            maven {
                // change to point to your repo, e.g. http://my.org/repo
                url = "$buildDir/repo"
            }
        }
    }

build.gradle.kts

    
    
    publishing {
        repositories {
            maven {
                // change to point to your repo, e.g. http://my.org/repo
                url = uri("$buildDir/repo")
            }
        }
    }

您将要配置的两个主要内容是存储库：

  * 网址（必填）

  * 名称（可选）

您可以定义多个存储库，只要它们在构建脚本中具有唯一的名称即可。您也可以声明一个（只有一个）没有名称的存储库。该存储库将采用隐式名称“ Maven”。

您还可以配置连接到存储库所需的任何身份验证详细信息。有关更多详细信息，请参见[MavenArtifactRepository](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.repositories.MavenArtifactRepository.html)。

### [](#publishing_maven:snapshot_and_release_repositories)[快照和版本库](#publishing_maven:snapshot_and_release_repositories)

将快照和发行版发布到不同的Maven存储库是一种常见的做法。一种简单的方法是根据项目版本配置存储库URL。下面的示例对以“
SNAPSHOT”结尾的版本使用一个URL，对其余版本使用不同的URL：

例子6.根据项目版本配置存储库URL

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        repositories {
            maven {
                def releasesRepoUrl = "$buildDir/repos/releases"
                def snapshotsRepoUrl = "$buildDir/repos/snapshots"
                url = version.endsWith('SNAPSHOT') ? snapshotsRepoUrl : releasesRepoUrl
            }
        }
    }

build.gradle.kts

    
    
    publishing {
        repositories {
            maven {
                val releasesRepoUrl = "$buildDir/repos/releases"
                val snapshotsRepoUrl = "$buildDir/repos/snapshots"
                url = uri(if (version.toString().endsWith("SNAPSHOT")) snapshotsRepoUrl else releasesRepoUrl)
            }
        }
    }

同样，您可以使用[项目或系统属性](/md/Gradle环境搭建.md#build_environment)来决定发布到哪个存储库。如果`release`设置了项目属性，例如在用户运行时，以下示例将使用发行版存储库`gradle
-Prelease publish`：

例子7.根据项目属性配置存储库URL

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        repositories {
            maven {
                def releasesRepoUrl = "$buildDir/repos/releases"
                def snapshotsRepoUrl = "$buildDir/repos/snapshots"
                url = project.hasProperty('release') ? releasesRepoUrl : snapshotsRepoUrl
            }
        }
    }

build.gradle.kts

    
    
    publishing {
        repositories {
            maven {
                val releasesRepoUrl = "$buildDir/repos/releases"
                val snapshotsRepoUrl = "$buildDir/repos/snapshots"
                url = uri(if (project.hasProperty("release")) releasesRepoUrl else snapshotsRepoUrl)
            }
        }
    }

## [](#publishing_maven:install)[发布到Maven Local](#publishing_maven:install)

为了与本地Maven安装集成，有时将模块及其POM文件和其他元数据发布到Maven本地存储库（通常位于 _$ USER_HOME / .m2 /
repository中_ ） _很有_ 用。用Maven的话来说，这就是“安装”模块。

Maven的发布插件使得这很容易做到通过自动创建[PublishToMavenLocal](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/publish/maven/tasks/PublishToMavenLocal.html)每个任务[MavenPublication](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.publish.maven.MavenPublication.html)在`publishing.publications`容器中。任务名称遵循的模式。这些任务中的每一个都连接到聚合任务中。您不需要在您的部分中。`publish
_PubName_
PublicationToMavenLocal``publishToMavenLocal``mavenLocal()``publishing.repositories`

## [](#publishing_maven:complete_example)[完整的例子](#publishing_maven:complete_example)

以下示例演示如何签名和发布Java库，包括源，Javadoc和自定义的POM：

例子8.发布一个Java库

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'java-library'
        id 'maven-publish'
        id 'signing'
    }
    
    group = 'com.example'
    version = '1.0'
    
    java {
        withJavadocJar()
        withSourcesJar()
    }
    
    publishing {
        publications {
            mavenJava(MavenPublication) {
                artifactId = 'my-library'
                from components.java
                versionMapping {
                    usage('java-api') {
                        fromResolutionOf('runtimeClasspath')
                    }
                    usage('java-runtime') {
                        fromResolutionResult()
                    }
                }
                pom {
                    name = 'My Library'
                    description = 'A concise description of my library'
                    url = 'http://www.example.com/library'
                    properties = [
                        myProp: "value",
                        "prop.with.dots": "anotherValue"
                    ]
                    licenses {
                        license {
                            name = 'The Apache License, Version 2.0'
                            url = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                        }
                    }
                    developers {
                        developer {
                            id = 'johnd'
                            name = 'John Doe'
                            email = 'john.doe@example.com'
                        }
                    }
                    scm {
                        connection = 'scm:git:git://example.com/my-library.git'
                        developerConnection = 'scm:git:ssh://example.com/my-library.git'
                        url = 'http://example.com/my-library/'
                    }
                }
            }
        }
        repositories {
            maven {
                // change URLs to point to your repos, e.g. http://my.org/repo
                def releasesRepoUrl = "$buildDir/repos/releases"
                def snapshotsRepoUrl = "$buildDir/repos/snapshots"
                url = version.endsWith('SNAPSHOT') ? snapshotsRepoUrl : releasesRepoUrl
            }
        }
    }
    
    signing {
        sign publishing.publications.mavenJava
    }
    
    
    javadoc {
        if(JavaVersion.current().isJava9Compatible()) {
            options.addBooleanOption('html5', true)
        }
    }

build.gradle.kts

    
    
    plugins {
        `java-library`
        `maven-publish`
        signing
    }
    
    group = "com.example"
    version = "1.0"
    
    java {
        withJavadocJar()
        withSourcesJar()
    }
    
    publishing {
        publications {
            create<MavenPublication>("mavenJava") {
                artifactId = "my-library"
                from(components["java"])
                versionMapping {
                    usage("java-api") {
                        fromResolutionOf("runtimeClasspath")
                    }
                    usage("java-runtime") {
                        fromResolutionResult()
                    }
                }
                pom {
                    name.set("My Library")
                    description.set("A concise description of my library")
                    url.set("http://www.example.com/library")
                    properties.set(mapOf(
                        "myProp" to "value",
                        "prop.with.dots" to "anotherValue"
                    ))
                    licenses {
                        license {
                            name.set("The Apache License, Version 2.0")
                            url.set("http://www.apache.org/licenses/LICENSE-2.0.txt")
                        }
                    }
                    developers {
                        developer {
                            id.set("johnd")
                            name.set("John Doe")
                            email.set("john.doe@example.com")
                        }
                    }
                    scm {
                        connection.set("scm:git:git://example.com/my-library.git")
                        developerConnection.set("scm:git:ssh://example.com/my-library.git")
                        url.set("http://example.com/my-library/")
                    }
                }
            }
        }
        repositories {
            maven {
                // change URLs to point to your repos, e.g. http://my.org/repo
                val releasesRepoUrl = uri("$buildDir/repos/releases")
                val snapshotsRepoUrl = uri("$buildDir/repos/snapshots")
                url = if (version.toString().endsWith("SNAPSHOT")) snapshotsRepoUrl else releasesRepoUrl
            }
        }
    }
    
    signing {
        sign(publishing.publications["mavenJava"])
    }
    
    tasks.javadoc {
        if (JavaVersion.current().isJava9Compatible) {
            (options as StandardJavadocDocletOptions).addBooleanOption("html5", true)
        }
    }

结果是将发布以下工件：

  * POM： `my-library-1.0.pom`

  * Java组件的主要JAR工件： `my-library-1.0.jar`

  * 已明确配置的源JAR工件： `my-library-1.0-sources.jar`

  * 已明确配置的Javadoc JAR工件： `my-library-1.0-javadoc.jar`

该[签名插件](https://docs.gradle.org/6.7.1/userguide/signing_plugin.html#signing_plugin)被用来生成每个工件的签名文件。此外，还将为所有工件和签名文件生成校验和文件。

## [](#publishing_maven:deferred_configuration)[删除延迟的配置行为](#publishing_maven:deferred_configuration)

在Gradle 5.0之前，`publishing
{}`（默认情况下）对块进行隐式处理，就好像在评估项目后已执行了其中的所有逻辑一样。此行为引起了很多混乱，在Gradle
4.8中已弃用，因为它是唯一以这种方式运行的块。

根据延迟的配置行为，您的发布块中或插件中可能有一些逻辑。例如，以下逻辑假定在设置artifactId时将评估子项目：

`Groovy``Kotlin`

build.gradle

    
    
    subprojects {
        publishing {
            publications {
                mavenJava(MavenPublication) {
                    from components.java
                    artifactId = jar.archiveBaseName
                }
            }
        }
    }

build.gradle.kts

    
    
    subprojects {
        publishing {
            publications {
                create<MavenPublication>("mavenJava") {
                    from(components["java"])
                    artifactId = tasks.jar.get().archiveBaseName.get()
                }
            }
        }
    }

现在必须将这种逻辑包装在一个`afterEvaluate {}`块中。

`Groovy``Kotlin`

build.gradle

    
    
    subprojects {
        publishing {
            publications {
                mavenJava(MavenPublication) {
                    from components.java
                    afterEvaluate {
                        artifactId = jar.archiveBaseName
                    }
                }
            }
        }
    }

build.gradle.kts

    
    
    subprojects {
        publishing {
            publications {
                create<MavenPublication>("mavenJava") {
                    from(components["java"])
                    afterEvaluate {
                        artifactId = tasks.jar.get().archiveBaseName.get()
                    }
                }
            }
        }
    }

