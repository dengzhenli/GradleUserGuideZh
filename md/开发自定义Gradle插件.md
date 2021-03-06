> 本文未勘误，请参考原文阅读[https://docs.gradle.org/6.7.1/userguide/custom_plugins.html](https://docs.gradle.org/6.7.1/userguide/custom_plugins.html)

# 开发自定义Gradle插件


内容

  * [打包插件](#打包插件)
  * [编写一个简单的插件](#编写一个简单的插件)
  * [使插件可配置](#使插件可配置)
  * [在自定义任务和插件中处理文件](#在自定义任务和插件中处理文件)
  * [将扩展属性映射到任务属性](#将扩展属性映射到任务属性)
  * [一个独立的项目](#一个独立的项目)
  * [预编译脚本插件](#预编译脚本插件)
  * [为您的插件编写测试](#为您的插件编写测试)
  * [更多细节](#更多细节)
  * [幕后花絮](#幕后花絮)

Gradle插件打包了可重用的构建逻辑，可在许多不同的项目和构建中使用。Gradle允许您实现自己的插件，因此您可以重复使用构建逻辑，并与他人共享。

您可以使用任何喜欢的语言来实现Gradle插件，前提是该实现最终被编译为JVM字节码。在我们的示例中，我们将使用Java作为独立插件项目的实现语言，并在buildscript插件示例中使用Groovy或Kotlin。通常，使用Java或Kotlin（静态类型）实现的插件比使用Groovy实施的插件性能更好。

## [打包插件](#打包插件)

您可以在几个地方放置插件的源代码。

构建脚本

    

您可以直接在构建脚本中包含插件的源代码。这样的好处是，无需执行任何操作即可自动编译插件并将其包含在构建脚本的类路径中。但是，该插件在构建脚本之外不可见，因此您不能在定义该构建脚本的外部重用该插件。

`buildSrc` 项目

    

您可以将插件的源代码放在` _rootProjectDir_ /buildSrc/src/main/java`目录中（` _rootProjectDir_
/buildSrc/src/main/groovy`或` _rootProjectDir_
/buildSrc/src/main/kotlin`根据您喜欢的语言）。Gradle将负责编译和测试插件，并使其在构建脚本的类路径中可用。该插件对构建使用的每个构建脚本都是可见的。但是，它在构建外部不可见，因此您不能在定义该构建的外部重用该插件。

有关[项目](/md/组织Gradle项目.md#organizing_gradle_projects)的更多详细信息，请参见[组织Gradle](/md/组织Gradle项目.md#organizing_gradle_projects)`buildSrc`项目。

独立项目

    

您可以为插件创建一个单独的项目。这个项目产生并发布了一个JAR，您可以在多个版本中使用它并与他人共享。通常，此JAR可能包含一些插件，或将几个相关的任务类捆绑到一个库中。或两者的某种组合。

在我们的示例中，我们将从构建脚本中的插件开始，以使事情变得简单。然后，我们将考虑创建一个独立的项目。

## [编写一个简单的插件](#编写一个简单的插件)

要创建Gradle插件，您需要编写一个实现[Plugin](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Plugin.html)接口的类。将插件应用于项目时，Gradle将创建插件类的实例，并调用该实例的[Plugin.apply（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Plugin.html#apply-T-)方法。项目对象作为参数传递，插件可以使用该参数配置项目，但需要这样做。下面的示例包含一个Greeting插件，该插件将一个`hello`任务添加到项目中。

例子1.一个自定义插件

`Groovy``Kotlin`

build.gradle

    
    
    class GreetingPlugin implements Plugin<Project> {
        void apply(Project project) {
            project.task('hello') {
                doLast {
                    println 'Hello from the GreetingPlugin'
                }
            }
        }
    }
    
    // Apply the plugin
    apply plugin: GreetingPlugin

build.gradle.kts

    
    
    class GreetingPlugin : Plugin<Project> {
        override fun apply(project: Project) {
            project.task("hello") {
                doLast {
                    println("Hello from the GreetingPlugin")
                }
            }
        }
    }
    
    // Apply the plugin
    apply<GreetingPlugin>()

输出 **`gradle -q hello`**

    
    
    > gradle -q hello
    Hello from the GreetingPlugin

需要注意的一件事是，将为每个应用插件的项目创建一个插件的新实例。另请注意，[Plugin](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Plugin.html)类是泛型类型。此示例接收[项目](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html)类型作为类型参数。插件可以改为接收类型为[Settings](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.initialization.Settings.html)的参数，在这种情况下，可以将其应用在设置脚本中，或者可以将类型为[Gradle](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.invocation.Gradle.html)的参数应用在初始化脚本中。

## [使插件可配置](#使插件可配置)

大多数插件为构建脚本提供了一些配置选项，其他插件则用于自定义插件的工作方式。插件使用 _扩展对象_
执行此操作。Gradle[项目](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html)具有关联的[ExtensionContainer](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/plugins/ExtensionContainer.html)对象，该对象包含已应用于项目的插件的所有设置和属性。您可以通过向该容器添加扩展对象来为您的插件提供配置。扩展对象只是具有表示配置的Java
Bean属性的对象。

让我们向项目添加一个简单的扩展对象。在这里，我们`greeting`向项目添加一个扩展对象，使您可以配置问候语。

例子2.一个自定义插件扩展

`Groovy``Kotlin`

build.gradle

    
    
    class GreetingPluginExtension {
        String message = 'Hello from GreetingPlugin'
    }
    
    class GreetingPlugin implements Plugin<Project> {
        void apply(Project project) {
            // Add the 'greeting' extension object
            def extension = project.extensions.create('greeting', GreetingPluginExtension)
            // Add a task that uses configuration from the extension object
            project.task('hello') {
                doLast {
                    println extension.message
                }
            }
        }
    }
    
    apply plugin: GreetingPlugin
    
    // Configure the extension
    greeting.message = 'Hi from Gradle'

build.gradle.kts

    
    
    open class GreetingPluginExtension {
        var message = "Hello from GreetingPlugin"
    }
    
    class GreetingPlugin : Plugin<Project> {
        override fun apply(project: Project) {
            // Add the 'greeting' extension object
            val extension = project.extensions.create<GreetingPluginExtension>("greeting")
            // Add a task that uses configuration from the extension object
            project.task("hello") {
                doLast {
                    println(extension.message)
                }
            }
        }
    }
    
    apply<GreetingPlugin>()
    
    // Configure the extension
    the<GreetingPluginExtension>().message = "Hi from Gradle"

输出 **`gradle -q hello`**

    
    
    > gradle -q hello
    Hi from Gradle

在此示例中，`GreetingPluginExtension`是一个对象，其属性名为`message`。扩展对象将添加到名称为的项目中`greeting`。然后，该对象可用作与扩展对象同名的项目属性。

通常，您需要在单个插件上指定多个相关属性。Gradle为每个扩展对象添加一个配置块，因此您可以将设置分组在一起。以下示例向您展示了它是如何工作的。

例子3.一个带有配置块的自定义插件

`Groovy``Kotlin`

build.gradle

    
    
    class GreetingPluginExtension {
        String message
        String greeter
    }
    
    class GreetingPlugin implements Plugin<Project> {
        void apply(Project project) {
            def extension = project.extensions.create('greeting', GreetingPluginExtension)
            project.task('hello') {
                doLast {
                    println "${extension.message} from ${extension.greeter}"
                }
            }
        }
    }
    
    apply plugin: GreetingPlugin
    
    // Configure the extension using a DSL block
    greeting {
        message = 'Hi'
        greeter = 'Gradle'
    }

build.gradle.kts

    
    
    open class GreetingPluginExtension {
        var message: String? = null
        var greeter: String? = null
    }
    
    class GreetingPlugin : Plugin<Project> {
        override fun apply(project: Project) {
            val extension = project.extensions.create<GreetingPluginExtension>("greeting")
            project.task("hello") {
                doLast {
                    println("${extension.message} from ${extension.greeter}")
                }
            }
        }
    }
    
    apply<GreetingPlugin>()
    
    // Configure the extension using a DSL block
    configure<GreetingPluginExtension> {
        message = "Hi"
        greeter = "Gradle"
    }

输出 **`gradle -q hello`**

    
    
    > gradle -q hello
    Hi from Gradle

在此示例中，可以在`greeting`闭包内将几个设置分组在一起。构建脚本（`greeting`）中的闭包块名称需要与扩展对象名称匹配。然后，当执行闭包时，扩展对象上的字段将基于标准Groovy闭包委托功能映射到闭包内的变量。


这样，使用扩展对象可以 _扩展_ Gradle
DSL，从而为插件添加项目属性和DSL块。并且由于扩展对象只是一个常规对象，因此可以通过向扩展对象添加属性和方法来提供嵌套在插件块中的自己的DSL。

### [开发项目扩展](#开发项目扩展)

您可以在[开发自定义Gradle类型中](/md/开发自定义Gradle类型.md#custom_gradle_types)找到有关实现项目扩展的更多信息。

## [在自定义任务和插件中处理文件](#在自定义任务和插件中处理文件)

开发自定义任务和插件时，在接受文件位置的输入配置时非常灵活是一个好主意。为此，您可以利用[Project.file（java.lang.Object）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:file\(java.lang.Object\))方法尽可能晚地将值解析为文件。

例子4.懒惰地评估文件属性

`Groovy``Kotlin`

build.gradle

    
    
    class GreetingToFileTask extends DefaultTask {
    
        def destination
    
        @OutputFile
        File getDestination() {
            project.file(destination)
        }
    
        @TaskAction
        def greet() {
            def file = getDestination()
            file.parentFile.mkdirs()
            file.write 'Hello!'
        }
    }
    
    task greet(type: GreetingToFileTask) {
        destination = { project.greetingFile }
    }
    
    task sayGreeting(dependsOn: greet) {
        doLast {
            println file(greetingFile).text
        }
    }
    
    ext.greetingFile = "$buildDir/hello.txt"

build.gradle.kts

    
    
    open class GreetingToFileTask : DefaultTask() {
    
        var destination: Any? = null
    
        @OutputFile
        fun getDestination(): File {
            return project.file(destination!!)
        }
    
        @TaskAction
        fun greet() {
            val file = getDestination()
            file.parentFile.mkdirs()
            file.writeText("Hello!")
        }
    }
    
    tasks.register<GreetingToFileTask>("greet") {
        destination = { project.extra["greetingFile"]!! }
    }
    
    tasks.register("sayGreeting") {
        dependsOn("greet")
        doLast {
            println(file(project.extra["greetingFile"]!!).readText())
        }
    }
    
    extra["greetingFile"] = "$buildDir/hello.txt"

输出 **`gradle -q sayGreeting`**

    
    
    > gradle -q sayGreeting
    Hello!

在此示例中，我们将`greet`task`destination`属性配置为闭包/提供者，使用[Project.file（java.lang.Object）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html#org.gradle.api.Project:file\(java.lang.Object\))方法对其进行评估，`File`以在最后一刻将闭包/提供者的返回值转换为对象。您会注意到，在上面的示例中，`greetingFile`在配置为任务使用属性值之后，我们指定了属性值。这种惰性评估的主要好处是在设置文件属性时接受任何值，然后在读取属性时解析该值。

## [将扩展属性映射到任务属性](#将扩展属性映射到任务属性)

从构建脚本通过扩展捕获用户输入并将其映射到自定义任务的输入/输出属性是一种有用的模式。构建脚本作者仅与扩展定义的DSL交互。命令式逻辑隐藏在插件实现中。

Gradle提供了一些类型，您可以在任务实现和扩展中使用这些类型来帮助您。有关更多信息，请参考[惰性配置](/md/延迟配置.md#lazy_configuration)。

## [一个独立的项目](#一个独立的项目)

现在，我们将插件移至独立项目，以便我们可以发布它并与他人共享。这个项目只是一个Java项目，它产生包含插件类的JAR。打包和发布插件的最简单且推荐的方法是使用[Java Gradle插件开发插件](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html#java_gradle_plugin)。该插件将自动应用[Java插件](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#java_plugin)，将`gradleApi()`依赖项添加到api配置中，在生成的JAR文件中生成所需的插件描述符，并配置要在发布时使用的[插件标记工件](/md/使用Gradle插件.md#插件标记工件)。这是该项目的简单构建脚本。

例子5.一个自定义插件的构建

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'java-gradle-plugin'
    }
    
    gradlePlugin {
        plugins {
            simplePlugin {
                id = 'org.samples.greeting'
                implementationClass = 'org.gradle.GreetingPlugin'
            }
        }
    }

build.gradle.kts

    
    
    plugins {
        `java-gradle-plugin`
    }
    
    gradlePlugin {
        plugins {
            create("simplePlugin") {
                id = "org.samples.greeting"
                implementationClass = "org.gradle.GreetingPlugin"
            }
        }
    }

### [创建一个插件ID](#创建一个插件ID)

插件ID以类似于Java包的方式（即反向域名）完全合格。这有助于避免冲突，并提供了一种将具有相似所有权的插件分组的方法。

您的插件ID应该是反映名称空间（指向您或您的组织的合理指针）及其提供的插件名称的组件的组合。例如，如果您有一个名为“
foo”的Github帐户，而您的插件名为“
bar”，则合适的插件ID可能为`com.github.foo.bar`。同样，如果插件是由baz组织开发的，则插件ID可能为`org.baz.bar`。

插件ID应符合以下条件：

  * 可以包含任何字母数字字符“。”和“-”。

  * 必须至少包含一个“。”。分隔命名空间和插件名称的字符。

  * 按照惯例，对名称空间使用小写的反向域名约定。

  * 通常，名称中仅使用小写字符。

  * `org.gradle`并且`com.gradleware`不能使用名称空间。

  * 不能以“。”开头或结尾。字符。

  * 不能包含连续的“。” 字符（即“ ..”）。

尽管插件ID与程序包名称之间存在常规的相似之处，但通常包名称比插件ID所需的更为详细。例如，在您的插件ID中添加“
gradle”作为组件似乎是合理的，但是由于插件ID仅用于Gradle插件，因此这是多余的。通常，一个良好的插件ID只需要一个用于标识所有权和名称的名称空间。

### [发布您的插件](#发布您的插件)

如果要在内部发布插件供组织内部使用，则可以像其他任何代码工件一样发布。请参阅有关发布工件的[Ivy](/md/Ivy发布插件.md#publishing_ivy)和[Maven](/md/Maven发布插件.md#publishing_maven)章节。

如果您有兴趣发布供更广泛的Gradle社区使用的插件，则可以将其发布到[Gradle插件门户](http://plugins.gradle.org/)。该站点提供了搜索和收集有关Gradle社区贡献的插件的信息的功能。
请参阅相应的[指南，](https://guides.gradle.org/publishing-plugins-to-gradle-plugin-portal/)以了解如何在此站点上使用您的插件。

### [在另一个项目中使用您的插件](#在另一个项目中使用您的插件)

要在构建脚本中使用插件，您需要`pluginManagement {}`在项目的设置文件中配置存储库。以下示例显示了将插件发布到本地存储库后如何执行此操作：

例子6.在另一个项目中使用一个自定义插件

`Groovy``Kotlin`

settings.gradle

    
    
    pluginManagement {
        repositories {
            maven {
                url = uri(repoLocation)
            }
        }
    }

build.gradle

    
    
    plugins {
        id 'org.samples.greeting' version '1.0-SNAPSHOT'
    }

settings.gradle.kts

    
    
    pluginManagement {
        repositories {
            maven {
                url = uri(repoLocation)
            }
        }
    }

build.gradle.kts

    
    
    plugins {
        id("org.samples.greeting") version "1.0-SNAPSHOT"
    }

#### [不带插件发布的注释`java-gradle-plugin`](#不带插件发布的注释`java-gradle-plugin`)

如果您的插件是在未使用[Java Gradle插件开发插件](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html#java_gradle_plugin)的情况下发布的，
则该出版物将缺少[PluginMarker Artifact](/md/使用Gradle插件.md#插件标记工件)，这是[插件DSL](/md/使用Gradle插件.md#通过插件DSL应用插件)查找插件所需的。在这种情况下，建议在另一个项目中解析该插件的方法是在该项目的设置文件`resolutionStrategy`的`pluginManagement
{}`块中添加一个部分，如下所示。

例子7.没有插件标记工件的插件的解析策略

`Groovy``Kotlin`

settings.gradle

    
    
        resolutionStrategy {
            eachPlugin {
                if (requested.id.namespace == 'org.samples') {
                    useModule("org.gradle:custom-plugin:${requested.version}")
                }
            }
        }

settings.gradle.kts

    
    
        resolutionStrategy {
            eachPlugin {
                if (requested.id.namespace == "org.samples") {
                    useModule("org.gradle:custom-plugin:${requested.version}")
                }
            }
        }

## [预编译脚本插件](#预编译脚本插件)

除了作为独立项目编写的插件之外，Gradle还允许您提供以Groovy或Kotlin
DSL编写的构建逻辑作为预编译脚本插件。您可以将它们写为`*.gradle`目录中的`src/main/groovy`
文件或`*.gradle.kts`目录中的`src/main/kotlin`文件。

预编译的脚本插件被编译成类文件，然后打包到jar中。出于所有目的和目的，它们是二进制插件，可以通过插件ID进行应用，经过测试并作为二进制插件发布。实际上，它们的插件元数据是使用[Gradle插件开发插件](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html#java_gradle_plugin)生成的。

╔═════════════════════════════  

Gradle 6.0内置的Kotlin DSL预编译脚本插件不能与Gradle的早期版本一起使用。在Gradle的未来版本中将取消此限制。

Groovy DSL预编译脚本插件可从Gradle 6.4开始使用。  
  
╚═════════════════════════════    
  
要应用预编译的脚本插件，您需要知道其ID，该ID由插件脚本的文件名（减去`.gradle`扩展名）派生而来。


例如，脚本`src/main/groovy/java-library-convention.gradle`的插件ID为`java-library-
convention`。同样，`src/main/groovy/my.java-library-convention.gradle`将产生的插件ID
`my.java-library-convention`。


为了演示如何实现和使用预编译的脚本插件，让我们来看一个基于`buildSrc`项目的示例。

首先，您需要一个`buildSrc/build.gradle`应用`groovy-gradle-plugin`插件的文件：


例子8.启用预编译脚本插件

`Groovy``Kotlin`

buildSrc / build.gradle

    
    
    plugins {
        id 'groovy-gradle-plugin'
    }

buildSrc/build.gradle.kts

    
    
    plugins {
        `kotlin-dsl`
    }
    
    repositories {
        jcenter()
    }

我们建议您还创建一个`buildSrc/settings.gradle`文件，您可以将其保留为空。



接下来，`java-library-
convention.gradle`在`buildSrc/src/main/groovy`目录中创建一个新文件，并将其内容设置为以下内容：


例子9.创建一个简单的脚本插件

`Groovy``Kotlin`

buildSrc / src / main / groovy / java-library-convention.gradle

    
    
    plugins {
        id 'java-library'
        id 'checkstyle'
    }
    
    java {
        sourceCompatibility = JavaVersion.VERSION_11
        targetCompatibility = JavaVersion.VERSION_11
    }
    
    checkstyle {
        maxWarnings = 0
        // ...
    }
    
    tasks.withType(JavaCompile) {
        options.warnings = true
        // ...
    }
    
    dependencies {
        testImplementation("junit:junit:4.13")
        // ...
    }

buildSrc/src/main/kotlin/java-library-convention.gradle.kts

    
    
    plugins {
        `java-library`
        checkstyle
    }
    
    java {
        sourceCompatibility = JavaVersion.VERSION_11
        targetCompatibility = JavaVersion.VERSION_11
    }
    
    checkstyle {
        maxWarnings = 0
        // ...
    }
    
    tasks.withType<JavaCompile> {
        options.isWarnings = true
        // ...
    }
    
    dependencies {
        testImplementation("junit:junit:4.13")
        // ...
    }

该脚本插件仅应用Java库和Checkstyle插件并对其进行配置。请注意，这实际上会将插件应用到主项目，即应用预编译脚本插件的插件。

最后，将脚本插件应用于根项目，如下所示：

例子10.将预编译的脚本插件应用到主项目

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'java-library-convention'
    }

build.gradle.kts

    
    
    plugins {
        `java-library-convention`
    }

### [在预编译的脚本插件中应用外部插件](#在预编译的脚本插件中应用外部插件)

为了将外部插件应用到预编译的脚本插件中，必须将其添加到插件的构建文件中的插件项目的实现类路径中。

`Groovy``Kotlin`

buildSrc / build.gradle

    
    
    plugins {
        id 'groovy-gradle-plugin'
    }
    
    repositories {
        jcenter()
    }
    
    dependencies {
        implementation 'com.bmuschko:gradle-docker-plugin:6.4.0'
    }

buildSrc/build.gradle.kts

    
    
    plugins {
        `kotlin-dsl`
    }
    
    repositories {
        jcenter()
    }
    
    dependencies {
        implementation("com.bmuschko:gradle-docker-plugin:6.4.0")
    }

然后可以将其应用在预编译的脚本插件中。

`Groovy``Kotlin`

buildSrc / src / main / groovy / my-plugin.gradle

    
    
    plugins {
        id 'com.bmuschko.docker-remote-api'
    }

buildSrc/src/main/kotlin/my-plugin.gradle.kts

    
    
    plugins {
        id("com.bmuschko.docker-remote-api")
    }

在这种情况下，插件版本在依赖项声明中定义。

在将来的Gradle版本中将删除此限制。

## [为您的插件编写测试](#为您的插件编写测试)

您可以使用[ProjectBuilder](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testfixtures/ProjectBuilder.html)类创建要在测试插件实现时使用的[Project](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html)实例。

### [示例：测试自定义插件](#示例：测试自定义插件)

src / test / java / org / gradle / GreetingPluginTest.java

    
    
    public class GreetingPluginTest {
        @Test
        public void greeterPluginAddsGreetingTaskToProject() {
            Project project = ProjectBuilder.builder().build();
            project.getPluginManager().apply("org.samples.greeting");
    
            assertTrue(project.getTasks().getByName("hello") instanceof GreetingTask);
        }
    }

## [更多细节](#更多细节)

插件通常还提供自定义任务类型。有关更多详细信息，请参见[开发自定义Gradle任务类型](/md/开发自定义Gradle任务类型.md#custom_tasks)。

Gradle提供了许多在开发Gradle类型（包括插件）时有用的功能。有关更多详细信息，请参见[开发自定义Gradle类型](/md/开发自定义Gradle类型.md#custom_gradle_types)。

╔═════════════════════════════  
在开发Gradle插件时，将信息记录到构建日志中时请务必小心。记录敏感信息（例如凭据，令牌，某些环境变量）被[视为安全漏洞](/md/使用记录.md#记录敏感信息)。公共持续集成服务的构建日志在世界范围内可见，并且可以公开此敏感信息。  
╚═════════════════════════════    
  
## [幕后花絮](#幕后花絮)

那么Gradle如何找到[插件](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Plugin.html)实现？答案是-
您需要在JAR的`META-INF/gradle-plugins`目录中提供一个与您的插件ID相匹配的属性文件，该文件由[Java
Gradle插件开发插件处理](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html#java_gradle_plugin)。

### [示例：接线自定义插件](#示例：接线自定义插件)

src / main / resources / META-INF / gradle-plugins /
org.samples.greeting.properties

    
    
    implementation-class=org.gradle.GreetingPlugin

请注意，属性文件名与插件ID匹配，并放置在资源文件夹中，并且该`implementation-
class`属性标识[插件](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Plugin.html)实现类。

