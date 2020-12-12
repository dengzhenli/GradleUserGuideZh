# 使用Gradle插件


内容

  * [插件做什么](#插件做什么)
  * [插件类型](#插件类型)
  * [使用插件](#使用插件)
  * [二进制插件](#二进制插件)
  * [脚本插件](#脚本插件)
  * [寻找社区插件](#寻找社区插件)
  * [有关插件的更多信息](#有关插件的更多信息)

Gradle的核心故意为现实世界的自动化提供了很少的东西。所有有用的功能（如编译Java代码的功能）都由 _plugins_
添加。插件添加新任务（例如[JavaCompile](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.compile.JavaCompile.html)），域对象（例如[SourceSet](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.SourceSet.html)），约定（例如Java源位于`src/main/java`），以及扩展核心对象和其他插件中的对象。

在本章中，我们讨论如何使用插件以及围绕插件的术语和概念。

## [插件做什么](#插件做什么)

将插件应用于项目可以使插件扩展项目的功能。它可以执行以下操作：

  * 扩展Gradle模型（例如，添加可以配置的新DSL元素）

  * 根据约定配置项目（例如，添加新任务或配置合理的默认值）

  * 应用特定的配置（例如，添加组织存储库或强制执行标准）

通过应用插件，而不是向项目构建脚本添加逻辑，我们可以获得许多好处。应用插件：

  * 促进重用并减少跨多个项目维护相似逻辑的开销

  * 允许更高程度的模块化，增强可理解性和组织性

  * 封装命令性逻辑，并允许构建脚本尽可能地具有声明性

## [插件类型](#插件类型)

Gradle中的插件一般有两种类型，二进制插件和脚本插件。
二进制插件是通过实现[Plugin](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Plugin.html) 接口或使用Gradle的DSL语言之一声明式地以编程方式编写的。
二进制插件可以驻留在构建脚本中，在项目层次结构中或外部的插件jar中。
脚本插件是额外的构建脚本，它进一步配置构建，通常实现了一种声明式的方法来操作构建。
它们通常在构建中使用，尽管它们可以被外部化并从远程位置访问。

插件通常起初是脚本插件（因为它们易于编写），然后，随着代码变得更有价值，它被迁移到可以轻松测试并在多个项目或组织之间共享的二进制插件。

## [使用插件](#使用插件)

要使用封装在插件中的构建逻辑，Gradle需要执行两个步骤。首先，它需要 _解析_ 插件，然后需要 _将_
插件应用于目标（通常是[Project）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html)。

_解决_
插件意味着找到包含给定插件的jar的正确版本，并将其添加为脚本类路径。插件解决后，即可在构建脚本中使用其API。脚本插件是自解析的，因为它们是从应用它们时提供的特定文件路径或URL解析的。作为Gradle发行的一部分提供的核心二进制插件会自动解决。

_应用_
插件意味着在要使用插件增强的项目上实际执行插件的[Plugin.apply（T）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Plugin.html#apply-T-)。应用插件是
_幂等的_ 。也就是说，您可以安全地多次应用任何插件而不会产生副作用。

使用插件的最常见用例是解析插件并将其应用于当前项目。由于这是一个常见用例，因此建议构建作者使用[插件DSL](#通过插件DSL应用插件)一步解决和应用插件。

## [二进制插件](#二进制插件)

您可以通过 _插件的插件ID来_ 应用插件， _插件ID_ 是 _插件_ 的全局唯一标识符或名称。Core
Gradle插件的特殊之处在于它们提供了简短的名称，例如`'java'`用于核心[JavaPlugin的名称](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/plugins/JavaPlugin.html)。所有其他二进制插件都必须使用插件ID的完全限定形式（例如`com.github.foo.bar`），尽管某些旧式插件可能仍使用简短的非限定形式。放置插件ID的位置取决于您使用的是[插件DSL](#通过插件DSL应用插件)还是[buildscript块。](#旧版插件应用)

### [二进制插件的位置](#二进制插件的位置)

插件就是实现了[Plugin](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Plugin.html)接口的任何类。Gradle提供了核心插件（例如`JavaPlugin`）作为其分发的一部分，这意味着它们会自动解决。但是，非核心二进制插件需要先解决，然后才能应用。这可以通过多种方式实现：

  * 使用插件DSL从插件门户或[自定义存储库添加](#自定义插件存储库)插件（请参阅[使用插件DSL应用插件](#通过插件DSL应用插件)）。

  * 包括来自定义为buildscript依赖项的外部jar中的插件（请参阅[使用buildscript块应用插件](#使用带有buildscript块的插件)）。

  * 在项目的buildSrc目录下将插件定义为源文件（请参阅[使用buildSrc提取功能逻辑](/md/组织Gradle项目.md#用`buildSrc`抽象逻辑势在必行)）。

  * 在构建脚本中将插件定义为内联类声明。

有关定义自己的插件的更多信息，请参见[自定义插件](/md/开发自定义Gradle插件.md#custom_plugins)。

### [通过插件DSL应用插件](#通过插件DSL应用插件)

插件DSL提供了一种声明插件依赖关系的简洁方便的方法。它与[Gradle插件门户](http://plugins.gradle.org/)一起使用，以提供对核心插件和社区插件的轻松访问。插件DSL块配置[PluginDependenciesSpec](https://docs.gradle.org/6.7.1/javadoc/org/gradle/plugin/use/PluginDependenciesSpec.html)的实例。

要应用核心插件，可以使用简称：

例子1.应用核心插件

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'java'
    }

build.gradle.kts

    
    
    plugins {
        java
    }

要从门户网站应用社区插件，必须使用完全限定的插件ID：

例子2.应用社区插件

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'com.jfrog.bintray' version '0.4.1'
    }

build.gradle.kts

    
    
    plugins {
        id("com.jfrog.bintray") version "0.4.1"
    }

有关使用插件DSL的更多信息，请参见[PluginDependenciesSpec](https://docs.gradle.org/6.7.1/javadoc/org/gradle/plugin/use/PluginDependenciesSpec.html)。

#### [插件DSL的局限性](#插件DSL的局限性)

这种将插件添加到项目中的方法远不止是更方便的语法。插件DSL的处理方式使Gradle可以非常早，非常迅速地确定正在使用的插件。这使Gradle可以做一些聪明的事情，例如：

  * 优化插件类的加载和重用。

  * 允许不同的插件使用不同版本的依赖项。

  * 向编辑者提供有关构建脚本中潜在属性和值的详细信息，以提供编辑帮助。

这要求在执行其余构建脚本之前，以Gradle可以轻松，快速提取的方式指定插件。它还要求要使用的插件的定义必须是静态的。

`plugins
{}`块机制与“传统”`apply()`方法机制之间存在一些关键差异。还有一些约束，其中一些是暂时的限制，而该机制仍在开发中，而某些则是新方法固有的。

##### [约束语法](#约束语法)

该`plugins {}`块不支持任意代码。它是受约束的，以便具有幂等性（每次都产生相同的结果）并且没有副作用（对于Gradle可以随时执行是安全的）。

形式是：

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id «plugin id»                                            __**( 1)**
        id «plugin id» version «plugin version» [apply «false»]   __**( 2)**
    }
═════════════════════════════  
①  用于核心Gradle插件或构建脚本已可用的插件  
②  用于需要解决的二进制Gradle插件  
╚═════════════════════════════  
build.gradle.kts

    
    
    plugins {
        `«plugin id»`                                             __**( 1)**
        id(«plugin id»)                                           __**( 2)**
        id(«plugin id») version «plugin version» [apply «false»]  __**( 3)**
    }
═════════════════════════════  
①  for core Gradle plugins  
②  for core Gradle plugins or plugins already available to the build script  
③  for binary Gradle plugins that need to be resolved  
╚═════════════════════════════  
在`«plugin id»`并且`«plugin
version»`必须为常数的地方，文字，字符串和`apply`带有的语句`boolean`可用于禁用立即应用插件的默认行为（例如，您仅希望在中应用插件`subprojects`）。不允许其他声明；它们的存在将导致编译错误。


如果要使用变量定义插件版本，请参见[插件版本管理](#插件版本管理)。

该`plugins {}`块还必须是buildscript中的顶级语句。它不能嵌套在另一个构造中（例如，if语句或for循环）。

##### [只能在构建脚本和设置文件中使用](#只能在构建脚本和设置文件中使用)

该`plugins {}`块当前只能在项目的构建脚本和settings.gradle文件中使用。不能在脚本插件或初始化脚本中使用。

_Gradle的未来版本将删除此限制。_

如果该`plugins {}`块的限制令人望而却步，则建议的方法是使用[buildscript
{}块](#使用带有buildscript块的插件)来应用插件。

#### [将具有相同版本的外部插件应用于子项目](#将具有相同版本的外部插件应用于子项目)

如果您具有[多项目构建](/md/Gradle中的多项目构建.md#multi_project_builds)，则可能希望将插件应用于[构建](/md/Gradle中的多项目构建.md#multi_project_builds)中的部分或全部子项目，而不是应用于`root`项目。该`plugins
{}`块的默认行为是立即`resolve` _和_ `apply`插件。但是，您可以使用`apply
false`语法告诉Gradle不要将插件应用于当前项目，然后`plugins {}`在子项目的构建脚本中使用不带版本的块：

例子3.仅在某些子项目上应用插件

`Groovy``Kotlin`

settings.gradle

    
    
    include 'hello-a'
    include 'hello-b'
    include 'goodbye-c'

build.gradle

    
    
    plugins {
        id 'com.example.hello' version '1.0.0' apply false
        id 'com.example.goodbye' version '1.0.0' apply false
    }

你好-a / build.gradle

    
    
    plugins {
        id 'com.example.hello'
    }

hello-b / build.gradle

    
    
    plugins {
        id 'com.example.hello'
    }

再见-c / build.gradle

    
    
    plugins {
        id 'com.example.goodbye'
    }

settings.gradle.kts

    
    
    include("hello-a")
    include("hello-b")
    include("goodbye-c")

build.gradle.kts

    
    
    plugins {
        id("com.example.hello") version "1.0.0" apply false
        id("com.example.goodbye") version "1.0.0" apply false
    }

hello-a/build.gradle.kts

    
    
    plugins {
        id("com.example.hello")
    }

hello-b/build.gradle.kts

    
    
    plugins {
        id("com.example.hello")
    }

goodbye-c/build.gradle.kts

    
    
    plugins {
        id("com.example.goodbye")
    }

更好的是，您可以使用自己的[约定插件](/md/在子项目之间共享构建逻辑.md#约定插件)通过组合构建逻辑来封装外部插件的版本。

#### [从_buildSrc_目录应用插件](#从_buildSrc_目录应用插件)

您可以应用驻留在项目的 _buildSrc_ 目录中的插件，只要它们具有已定义的ID即可。以下示例显示了如何将插件实现类（`my.MyPlugin`在
_buildSrc中_ 定义）与ID“ my-plugin”相关联：

例子4.定义一个带有ID的buildSrc插件

`Groovy``Kotlin`

buildSrc / build.gradle

    
    
    plugins {
        id 'java-gradle-plugin'
    }
    
    gradlePlugin {
        plugins {
            myPlugins {
                id = 'my-plugin'
                implementationClass = 'my.MyPlugin'
            }
        }
    }

buildSrc/build.gradle.kts

    
    
    plugins {
        `java-gradle-plugin`
    }
    
    gradlePlugin {
        plugins {
            create("myPlugins") {
                id = "my-plugin"
                implementationClass = "my.MyPlugin"
            }
        }
    }

然后可以按常规方式通过ID应用插件：

例子5.从buildSrc应用一个插件

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'my-plugin'
    }

build.gradle.kts

    
    
    plugins {
        id("my-plugin")
    }

#### [插件管理](#插件管理)

该`pluginManagement
{}`块只能出现在`settings.gradle`文件中，该文件必须是文件中的第一个块，也可以出现在[初始化脚本中](/md/初始化脚本.md#init_scripts)。

例子6.为每个项目和全局配置pluginManagement

`Groovy``Kotlin`

settings.gradle

    
    
    pluginManagement {
        plugins {
        }
        resolutionStrategy {
        }
        repositories {
        }
    }
    rootProject.name = 'plugin-management'

init.gradle

    
    
    settingsEvaluated { settings ->
        settings.pluginManagement {
            plugins {
            }
            resolutionStrategy {
            }
            repositories {
            }
        }
    }

settings.gradle.kts

    
    
    pluginManagement {
        plugins {
        }
        resolutionStrategy {
        }
        repositories {
        }
    }
    rootProject.name = "plugin-management"

init.gradle.kts

    
    
    settingsEvaluated {
        pluginManagement {
            plugins {
            }
            resolutionStrategy {
            }
            repositories {
            }
        }
    }

##### [自定义插件存储库](#自定义插件存储库)

默认情况下，`plugins
{}`DSL从公共[Gradle插件门户](https://plugins.gradle.org/)解析插件[。](https://plugins.gradle.org/)许多构建作者还希望从私有Maven或Ivy存储库中解析插件，因为这些插件包含专有的实现细节，或者只是为了更好地控制其构建可用的插件。

要指定自定义插件存储库，请使用其中的`repositories {}`块`pluginManagement {}`：

示例7.示例：使用来自自定义插件存储库的插件。

`Groovy``Kotlin`

settings.gradle

    
    
    pluginManagement {
        repositories {
            maven {
                url '../maven-repo'
            }
            gradlePluginPortal()
            ivy {
                url '../ivy-repo'
            }
        }
    }

settings.gradle.kts

    
    
    pluginManagement {
        repositories {
            maven(url = "../maven-repo")
            gradlePluginPortal()
            ivy(url = "../ivy-repo")
        }
    }

这告诉Gradle在`../maven-
repo`解析插件时首先在Maven存储库中查找，然后检查Gradle插件门户是否在Maven存储库中找不到插件。如果您不想搜索Gradle插件门户，请省略该`gradlePluginPortal()`行。最后，`../ivy-
repo`将检查Ivy存储库。

##### [插件版本管理](#插件版本管理)

`plugins {}`内部的一个块`pluginManagement
{}`允许将构建的所有插件版本定义在一个位置。然后可以通过ID将插件按ID应用于任何构建脚本`plugins {}`。

通过这种方式设置插件版本的好处之一是，`pluginManagement.plugins
{}`它们的[语法](#约束语法)与构建脚本`plugins
{}`块的[约束语法不同](#约束语法)。这允许从中获取插件版本`gradle.properties`，或通过其他机制加载。

示例8.示例：通过管理插件版本`pluginManagement`。

`Groovy``Kotlin`

settings.gradle

    
    
    pluginManagement {
      plugins {
            id 'com.example.hello' version "${helloPluginVersion}"
        }
    }

build.gradle

gradle.properties

    
    
    helloPluginVersion=1.0.0

settings.gradle.kts

    
    
    pluginManagement {
      val helloPluginVersion: String by settings
      plugins {
        id("com.example.hello") version "${helloPluginVersion}"
      }
    }

build.gradle.kts

gradle.properties

    
    
    helloPluginVersion=1.0.0

插件版本从`gradle.properties`设置脚本加载并在设置脚本中进行配置，从而允许在不指定版本的情况下将插件添加到任何项目中。

#### [插件解析规则](#插件解析规则)

插件解析规则允许您修改以`plugins {}`块为单位的插件请求，例如，更改请求的版本或显式指定实现工件坐标。

要添加解析规则，请使用`resolutionStrategy {}`内部`pluginManagement {}`块：

示例9.插件解析策略。

`Groovy``Kotlin`

settings.gradle

    
    
    pluginManagement {
        resolutionStrategy {
            eachPlugin {
                if (requested.id.namespace == 'com.example') {
                    useModule('com.example:sample-plugins:1.0.0')
                }
            }
        }
        repositories {
            maven {
                url '../maven-repo'
            }
            gradlePluginPortal()
            ivy {
                url '../ivy-repo'
            }
        }
    }

settings.gradle.kts

    
    
    pluginManagement {
        resolutionStrategy {
            eachPlugin {
                if (requested.id.namespace == "com.example") {
                    useModule("com.example:sample-plugins:1.0.0")
                }
            }
        }
        repositories {
            maven {
                url = uri("../maven-repo")
            }
            gradlePluginPortal()
            ivy {
                url = uri("../ivy-repo")
            }
        }
    }

这告诉Gradle使用指定的插件实现构件，而不是使用其从插件ID到Maven / Ivy坐标的内置默认映射。

自定义Maven和Ivy插件存储库除了实际实现插件的[工件外](#插件标记工件)，还必须包含[插件标记工件](#插件标记工件)。有关将插件发布到自定义存储库的更多信息，请阅读[Gradle
Plugin Development
Plugin](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html#java_gradle_plugin)。

有关使用该块的完整文档，请参见[PluginManagementSpec](https://docs.gradle.org/6.7.1/javadoc/org/gradle/plugin/management/PluginManagementSpec.html)`pluginManagement
{}`。

### [插件标记工件](#插件标记工件)

由于`plugins{}`DSL块仅允许通过其全局唯一的插件`id`和`version`属性来声明插件，因此Gradle需要一种方法来查找插件实现工件的坐标。为此，Gradle将寻找具有坐标的插件标记工件`plugin.id:plugin.id.gradle.plugin:plugin.version`。该标记需要依赖于实际的插件实现。这些标记的发布由[java-gradle-plugin](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html#java_gradle_plugin)自动执行。

例如，下面的`sample-plugins`项目完整示例显示了如何使用[java-gradle-plugin](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html#java_gradle_plugin)，[maven-publish](/md/Maven发布插件.md#publishing_maven)插件和[ivy-publish](/md/Ivy发布插件.md#publishing_ivy)插件的组合将`com.example.hello`插件和`com.example.goodbye`插件发布到Ivy和Maven存储库。[](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html#java_gradle_plugin)[](/md/Maven发布插件.md#publishing_maven)[](/md/Ivy发布插件.md#publishing_ivy)

例子10.完整的插件发布样本

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'java-gradle-plugin'
        id 'maven-publish'
        id 'ivy-publish'
    }
    
    group 'com.example'
    version '1.0.0'
    
    gradlePlugin {
        plugins {
            hello {
                id = 'com.example.hello'
                implementationClass = 'com.example.hello.HelloPlugin'
            }
            goodbye {
                id = 'com.example.goodbye'
                implementationClass = 'com.example.goodbye.GoodbyePlugin'
            }
        }
    }
    
    publishing {
        repositories {
            maven {
                url '../../consuming/maven-repo'
            }
            ivy {
                url '../../consuming/ivy-repo'
            }
        }
    }

build.gradle.kts

    
    
    plugins {
        `java-gradle-plugin`
        `maven-publish`
        `ivy-publish`
    }
    
    group = "com.example"
    version = "1.0.0"
    
    gradlePlugin {
        plugins {
            create("hello") {
                id = "com.example.hello"
                implementationClass = "com.example.hello.HelloPlugin"
            }
            create("goodbye") {
                id = "com.example.goodbye"
                implementationClass = "com.example.goodbye.GoodbyePlugin"
            }
        }
    }
    
    publishing {
        repositories {
            maven {
                url = uri("../../consuming/maven-repo")
            }
            ivy {
                url = uri("../../consuming/ivy-repo")
            }
        }
    }

`gradle publish`在示例目录中运行会导致以下存储库布局存在：

![pluginMarkers](img/pluginMarkers.png)

### [旧版插件应用](#旧版插件应用)

随着[插件DSL](#通过插件DSL应用插件)的引入，用户几乎没有理由使用应用插件的旧方法。如果构建作者由于当前工作方式的限制而无法使用插件DSL，则在此进行记录。

#### [应用二进制插件](#应用二进制插件)

例子11.应用一个二进制插件

`Groovy``Kotlin`

build.gradle

    
    
    apply plugin: 'java'

build.gradle.kts

    
    
    apply(plugin = "java")

可以使用 _插件ID_ 来应用 _插件_ 。在上述情况下，我们使用简称'
`java`'来应用[JavaPlugin](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/plugins/JavaPlugin.html)。

除了使用插件ID，还可以通过简单地指定插件的类来应用插件：

例子12.按类型应用二进制插件

`Groovy``Kotlin`

build.gradle

    
    
    apply plugin: JavaPlugin

build.gradle.kts

    
    
    apply<JavaPlugin>()

`JavaPlugin`上面示例中的符号引用[JavaPlugin](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/plugins/JavaPlugin.html)。绝对不需要导入此类，因为会`org.gradle.api.plugins`在所有构建脚本中自动导入软件包（请参见[默认导入](/md/编写构建脚本.md#script-
default-imports)）。

此外，不需要`.class`像在Java中那样在Groovy中附加以标识类文字。

Furthermore, one need to append the `::class` suffix to identify a class
literal in Kotlin instead of `.class` in Java.

#### [使用带有buildscript块的插件](#使用带有buildscript块的插件)

通过将插件添加到构建脚本类路径中，然后应用该插件，可以将已发布为外部jar文件的二进制插件添加到项目中。可以使用构建脚本的[外部依赖项中所述](/md/构建脚本基础.md#构建脚本的外部依赖关系)的`buildscript
{}`块将外部jar添加到构建脚本类路径中。[](/md/构建脚本基础.md#构建脚本的外部依赖关系)

例子13.使用带有buildscript块的插件

`Groovy``Kotlin`

build.gradle

    
    
    buildscript {
        repositories {
            jcenter()
        }
        dependencies {
            classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:0.4.1'
        }
    }
    
    apply plugin: 'com.jfrog.bintray'

build.gradle.kts

    
    
    buildscript {
        repositories {
            jcenter()
        }
        dependencies {
            classpath("com.jfrog.bintray.gradle:gradle-bintray-plugin:0.4.1")
        }
    }
    
    apply(plugin = "com.jfrog.bintray")

## [脚本插件](#脚本插件)

例子14.应用脚本插件

`Groovy``Kotlin`

build.gradle

    
    
    apply from: 'other.gradle'

build.gradle.kts

    
    
    apply(from = "other.gradle.kts")

脚本插件会自动解决，可以从本地文件系统或远程位置的脚本中应用。文件系统位置是相对于项目目录的，而远程脚本位置是通过HTTP
URL指定的。可以将多个脚本插件（任意一种形式）应用于给定目标。

## [寻找社区插件](#寻找社区插件)

Gradle有一个充满活力的插件开发人员社区，他们为各种功能贡献插件。Gradle[插件门户](https://plugins.gradle.org/)提供了一个用于搜索和浏览社区插件的界面。

## [有关插件的更多信息](#有关插件的更多信息)

本章旨在作为插件和Gradle以及它们扮演的角色的介绍。有关插件内部工作的更多信息，请参见“[自定义插件”](/md/开发自定义Gradle插件.md#custom_plugins)。

