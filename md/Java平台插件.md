# Java平台插件


内容

  * [用法](#用法)
  * [API和运行时分离](#API和运行时分离)
  * [本地项目约束](#本地项目约束)
  * [来自其他平台的采购约束](#来自其他平台的采购约束)
  * [发布平台](#发布平台)
  * [消费平台](#消费平台)

Java Platform插件带来了声明Java生态系统平台的能力。平台可用于不同目的：

  * 一起发布的模块的描述（例如，共享相同的版本）

  * 异构库的一组推荐版本。一个典型的例子包括[Spring Boot BOM](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-build-systems.html#using-boot-dependency-management)

  * [](#消费平台)在子项目之间[共享一组依赖版本](#消费平台)

平台是一种特殊的软件组件，不包含任何资源：它仅用于引用其他库，因此它们在依赖关系解析期间可以很好地协同工作。

平台可以发布为[Gradle模块元数据](https://github.com/gradle/gradle/blob/master/subprojects/docs/src/docs/design/gradle-module-metadata-latest-specification.md)和[MavenBOM](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Dependency_Management)。

╔═════════════════════════════  

该`java-platform`插件不能与给定项目中的`java`或`java-
library`插件结合使用。从概念上一个项目或者是一个平台，没有二进制文件， _或_ 产生二进制文件。  
  
╚═════════════════════════════    
  
## [用法](#用法)

要使用Java Platform插件，请在构建脚本中包括以下内容：

例子1.使用Java Platform插件

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'java-platform'
    }

build.gradle.kts

    
    
    plugins {
        `java-platform`
    }

## [API和运行时分离](#API和运行时分离)

Maven
BOM和Java平台之间的主要区别在于，在Gradle中，依赖项和[约束](/md/依赖管理术语.md#依赖（dependency）_constraint)被声明并确定范围为配置以及扩展配置。尽管许多用户只关心声明
_编译时_ 相关性的约束，这样才可以由运行时和测试继承，但它允许声明依赖项或仅适用于运行时或测试的约束。

为了这个目的，该插件公开了两个[配置](/md/声明依赖.md#什么是依赖项配置)可用于声明的依赖性：`api`和`runtime`。该`api`配置应用于声明在针对平台进行编译时应使用的约束和依赖关系，而`runtime`配置应用于声明在运行时可见的约束或依赖关系。

示例2.声明API和运行时约束

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        constraints {
            api 'commons-httpclient:commons-httpclient:3.1'
            runtime 'org.postgresql:postgresql:42.2.5'
        }
    }

build.gradle.kts

    
    
    dependencies {
        constraints {
            api("commons-httpclient:commons-httpclient:3.1")
            runtime("org.postgresql:postgresql:42.2.5")
        }
    }

请注意，此示例使用 _约束_
而非依赖关系。通常，这就是您要执行的操作：仅当将这样的组件直接或传递地添加到依赖关系图时，约束才适用。这意味着平台中列出的所有约束都不会添加依赖项，除非另一个组件将其引入：它们可以视为
_建议_ 。

╔═════════════════════════════  

例如，如果平台在上声明了约束`org:foo:1.1`，并且没有其他任何对的依赖`foo`，`foo`则 _不会_
出现在图中。但是，如果`foo`出现，则通常会解决冲突。如果引入依赖项`org:foo:1.0`，则我们将选择`org:foo:1.1`满足平台约束。  
  
╚═════════════════════════════    
  
默认情况下，为了避免在平台上添加依赖而不是约束的常见错误，如果尝试这样做，Gradle将失败。如果出于某种原因，除了约束之外，您还想添加 _依赖项_
，则需要显式启用它：

例子3.允许声明依赖

`Groovy``Kotlin`

build.gradle

    
    
    javaPlatform {
        allowDependencies()
    }

build.gradle.kts

    
    
    javaPlatform {
        allowDependencies()
    }

## [本地项目约束](#本地项目约束)

如果您有一个多项目构建，并希望发布一个链接到子项目的平台，则可以通过声明属于该平台的子项目的约束来做到这一点，如下例所示：

例子4.声明子项目的约束

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        constraints {
            api project(":core")
            api project(":lib")
        }
    }

build.gradle.kts

    
    
    dependencies {
        constraints {
            api(project(":core"))
            api(project(":lib"))
        }
    }

项目符号将成为`group:name:version`发布的元数据中的经典符号。

## [来自其他平台的采购约束](#来自其他平台的采购约束)

有时，您定义的平台是另一个现有平台的扩展。

为了使您的平台包含来自该第三方平台的约束，需要将其作为`platform` _依赖项_ 导入：

例子5.导入平台

`Groovy``Kotlin`

build.gradle

    
    
    javaPlatform {
        allowDependencies()
    }
    
    dependencies {
        api platform('com.fasterxml.jackson:jackson-bom:2.9.8')
    }

build.gradle.kts

    
    
    javaPlatform {
        allowDependencies()
    }
    
    dependencies {
        api(platform("com.fasterxml.jackson:jackson-bom:2.9.8"))
    }

## [发布平台](#发布平台)

通过应用`maven-publish`插件并配置使用该`javaPlatform`组件的Maven发布来完成Java平台的发布：

例子6.发布为BOM

`Groovy``Kotlin`

build.gradle

    
    
    publishing {
        publications {
            myPlatform(MavenPublication) {
                from components.javaPlatform
            }
        }
    }

build.gradle.kts

    
    
    publishing {
        publications {
            create<MavenPublication>("myPlatform") {
                from(components["javaPlatform"])
            }
        }
    }

这将为平台生成一个BOM表文件，并在`<dependencyManagement>`其中包含一个`<dependencies>`与平台模块中定义的约束相对应的块。

## [消费平台](#消费平台)

因为Java平台是一种特殊的组件，所以必须使用`platform`or`enforcedPlatform`关键字声明对Java平台的依赖关系，如[管理传递依赖关系](/md/在项目之间共享依赖版本.md#导入Maven%20BOM)部分中所述。例如，如果要在子项目之间共享依赖版本，则可以定义一个平台模块来声明所有版本：

示例7.平台模块中的推荐版本

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        constraints {
            // Platform declares some versions of libraries used in subprojects
            api 'commons-httpclient:commons-httpclient:3.1'
            api 'org.apache.commons:commons-lang3:3.8.1'
        }
    }

build.gradle.kts

    
    
    dependencies {
        constraints {
            // Platform declares some versions of libraries used in subprojects
            api("commons-httpclient:commons-httpclient:3.1")
            api("org.apache.commons:commons-lang3:3.8.1")
        }
    }

然后让子项目依赖平台来获取建议：

示例8.从平台获取建议

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        // get recommended versions from the platform project
        api platform(project(':platform'))
        // no version required
        api 'commons-httpclient:commons-httpclient'
    }

build.gradle.kts

    
    
    dependencies {
        // get recommended versions from the platform project
        api(platform(project(":platform")))
        // no version required
        api("commons-httpclient:commons-httpclient")
    }

