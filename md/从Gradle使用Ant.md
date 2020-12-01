# 从Gradle使用Ant


内容

  * [在构建中使用Ant任务和类型](#在构建中使用Ant任务和类型)
  * [导入Ant构建](#导入Ant构建)
  * [Ant属性和参考](#Ant属性和参考)
  * [Ant记录](#Ant记录)
  * [API](#API)

Gradle提供了与Ant的出色集成。您可以在Gradle构建中使用单个Ant任务或整个Ant构建。实际上，您会发现，在Gradle构建脚本中使用Ant任务比使用Ant的XML格式要容易得多，功能更强大。您甚至可以简单地将Gradle用作强大的Ant任务脚本工具。

Ant可以分为两层。第一层是Ant语言。它提供了`build.xml`文件的语法，目标的处理，特殊的构造（例如macrodef）等等。换句话说，除Ant任务和类型以外的所有内容。Gradle理解这种语言，并允许您将Ant`build.xml`直接导入Gradle项目。然后，您可以像使用Gradle任务一样使用Ant构建的目标。

Ant的第二层是其丰富的Ant任务和类型，如`javac`，`copy`或`jar`。对于这一层，Gradle只需依靠Groovy即可实现集成`AntBuilder`。

最后，由于构建脚本是Groovy脚本，因此您始终可以将Ant构建作为外部进程执行。构建脚本会含有类似的语句：`"ant cleancompile".execute()`。[[1](#_footnotedef_1"查看脚注。") ]

您可以使用Gradle的Ant集成作为将构建从Ant迁移到Gradle的路径。例如，您可以从导入现有的Ant构建开始。然后，您可以将依赖项声明从Ant脚本移至构建文件。最后，您可以将任务移到构建文件中，或将其替换为Gradle的某些插件。随着时间的流逝，此过程可以分几步完成，并且在整个过程中，您都可以使用有效的Gradle构建。

## [在构建中使用Ant任务和类型](#在构建中使用Ant任务和类型)

在您的构建脚本中，`ant`Gradle提供了一个名为的属性。这是对[AntBuilder](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/AntBuilder.html)实例的引用。这`AntBuilder`用于从构建脚本访问Ant任务，类型和属性。从Ant`build.xml`格式到Groovy有一个非常简单的映射，下面将进行解释。

您可以通过在`AntBuilder`实例上调用方法来执行Ant任务。您使用任务名称作为方法名称。例如，您可以`echo`通过调用`ant.echo()`方法来执行Ant任务。Ant任务的属性作为Map参数传递给方法。下面是`echo`任务的示例。注意，我们还可以混合使用Groovy代码和Ant任务标记。这可能非常强大。

示例1.使用Ant任务

`Groovy``Kotlin`

build.gradle

    
    
    task hello {
        doLast {
            String greeting = 'hello from Ant'
            ant.echo(message: greeting)
        }
    }

build.gradle.kts

    
    
    tasks.register("hello") {
        doLast {
            val greeting = "hello from Ant"
            ant.withGroovyBuilder {
                "echo"("message" to greeting)
            }
        }
    }

输出 `gradle hello`

    
    
    > gradle hello
    
    > Task :hello
    [ant:echo] hello from Ant
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

您可以通过将嵌套的文本作为任务方法调用的参数传递给Ant任务。在此示例中，我们`echo`以嵌套文本形式传递任务消息：

示例2.将嵌套文本传递给Ant任务

`Groovy``Kotlin`

build.gradle

    
    
    task hello {
        doLast {
            ant.echo('hello from Ant')
        }
    }

build.gradle.kts

    
    
    tasks.register("hello") {
        doLast {
            ant.withGroovyBuilder {
                "echo"("message" to "hello from Ant")
            }
        }
    }

输出 `gradle hello`

    
    
    > gradle hello
    
    > Task :hello
    [ant:echo] hello from Ant
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

您将嵌套元素传递给闭包内部的Ant任务。通过调用与我们要定义的元素同名的方法，以与任务相同的方式定义嵌套元素。

例子3.将嵌套元素传递给Ant任务

`Groovy``Kotlin`

build.gradle

    
    
    task zip {
        doLast {
            ant.zip(destfile: 'archive.zip') {
                fileset(dir: 'src') {
                    include(name: '**.xml')
                    exclude(name: '**.java')
                }
            }
        }
    }

build.gradle.kts

    
    
    tasks.register("zip") {
        doLast {
            ant.withGroovyBuilder {
                "zip"("destfile" to "archive.zip") {
                    "fileset"("dir" to "src") {
                        "include"("name" to "**.xml")
                        "exclude"("name" to "**.java")
                    }
                }
            }
        }
    }

您可以使用与访问任务相同的方式来访问Ant类型，方法是将类型的名称用作方法名称。方法调用返回Ant数据类型，然后您可以在构建脚本中直接使用它。在以下示例中，我们创建一个Ant`path`对象，然后遍历它的内容。

例子4.使用Ant类型

`Groovy``Kotlin`

build.gradle

    
    
    task list {
        doLast {
            def path = ant.path {
                fileset(dir: 'libs', includes: '*.jar')
            }
            path.list().each {
                println it
            }
        }
    }

build.gradle.kts

    
    
    import org.apache.tools.ant.types.Path
    
    tasks.register("list") {
        doLast {
            val path = ant.withGroovyBuilder {
                "path" {
                    "fileset"("dir" to "libs", "includes" to "*.jar")
                }
            } as Path
            path.list().forEach {
                println(it)
            }
        }
    }

有关更多信息，请`AntBuilder`参见“ Groovy in Action” 8.4或[Groovy Wiki](https://groovy-
lang.org/scripting-ant.html)。

### [在构建中使用定制的Ant任务](#在构建中使用定制的Ant任务)

要使自定义任务在构建中可用，可以像使用文件一样使用`taskdef`（通常更容易）或`typedef`Ant任务`build.xml`。然后，您可以像内置Ant任务一样引用自定义Ant任务。

例子5.使用定制的Ant任务

`Groovy``Kotlin`

build.gradle

    
    
    task check {
        doLast {
            ant.taskdef(resource: 'checkstyletask.properties') {
                classpath {
                    fileset(dir: 'libs', includes: '*.jar')
                }
            }
            ant.checkstyle(config: 'checkstyle.xml') {
                fileset(dir: 'src')
            }
        }
    }

build.gradle.kts

    
    
    tasks.register("check") {
        doLast {
            ant.withGroovyBuilder {
                "taskdef"("resource" to "checkstyletask.properties") {
                    "classpath" {
                        "fileset"("dir" to "libs", "includes" to "*.jar")
                    }
                }
                "checkstyle"("config" to "checkstyle.xml") {
                    "fileset"("dir" to "src")
                }
            }
        }
    }

您可以使用Gradle的依赖关系管理来组装用于自定义任务的类路径。为此，您需要为类路径定义一个自定义配置，然后向该配置中添加一些依赖项。这在[声明依赖项](/md/声明依赖.md)中有更详细的描述。

示例6.声明自定义Ant任务的类路径

`Groovy``Kotlin`

build.gradle

    
    
    configurations {
        pmd
    }
    
    dependencies {
        pmd group: 'pmd', name: 'pmd', version: '4.2.5'
    }

build.gradle.kts

    
    
    val pmd = configurations.create("pmd")
    
    dependencies {
        pmd(group = "pmd", name = "pmd", version = "4.2.5")
    }

要使用类路径配置，请使用`asPath`定制配置的属性。

示例7.一起使用定制Ant任务和依赖项管理

`Groovy``Kotlin`

build.gradle

    
    
    task check {
        doLast {
            ant.taskdef(name: 'pmd',
                        classname: 'net.sourceforge.pmd.ant.PMDTask',
                        classpath: configurations.pmd.asPath)
            ant.pmd(shortFilenames: 'true',
                    failonruleviolation: 'true',
                    rulesetfiles: file('pmd-rules.xml').toURI().toString()) {
                formatter(type: 'text', toConsole: 'true')
                fileset(dir: 'src')
            }
        }
    }

build.gradle.kts

    
    
    tasks.register("check") {
        doLast {
            ant.withGroovyBuilder {
                "taskdef"("name" to "pmd",
                          "classname" to "net.sourceforge.pmd.ant.PMDTask",
                          "classpath" to pmd.asPath)
                "pmd"("shortFilenames" to true,
                      "failonruleviolation" to true,
                      "rulesetfiles" to file("pmd-rules.xml").toURI().toString()) {
                    "formatter"("type" to "text", "toConsole" to "true")
                    "fileset"("dir" to "src")
                }
            }
        }
    }

## [导入Ant构建](#导入Ant构建)

您可以使用该`ant.importBuild()`方法将Ant构建导入到Gradle项目中。导入Ant构建时，每个Ant目标都被视为Gradle任务。这意味着您可以按照与Gradle任务完全相同的方式来操纵和执行Ant目标。

例子8.导入一个Ant版本

`Groovy``Kotlin`

build.gradle

    
    
    ant.importBuild 'build.xml'

build.gradle.kts

    
    
    ant.importBuild("build.xml")

build.xml

    
    
    <project>
        <target name="hello">
            <echo>Hello, from Ant</echo>
        </target>
    </project>

输出 `gradle hello`

    
    
    > gradle hello
    
    > Task :hello
    [ant:echo] Hello, from Ant
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

您可以添加一个依赖于Ant目标的任务：

例子9.依赖于Ant目标的任务

`Groovy``Kotlin`

build.gradle

    
    
    ant.importBuild 'build.xml'
    
    task intro(dependsOn: hello) {
        doLast {
            println 'Hello, from Gradle'
        }
    }

build.gradle.kts

    
    
    ant.importBuild("build.xml")
    
    tasks.register("intro") {
        dependsOn("hello")
        doLast {
            println("Hello, from Gradle")
        }
    }

输出 `gradle intro`

    
    
    > gradle intro
    
    > Task :hello
    [ant:echo] Hello, from Ant
    
    > Task :intro
    Hello, from Gradle
    
    BUILD SUCCESSFUL in 0s
    2 actionable tasks: 2 executed

或者，您可以将行为添加到Ant目标：

例子10.向Ant目标添加行为

`Groovy``Kotlin`

build.gradle

    
    
    ant.importBuild 'build.xml'
    
    hello {
        doLast {
            println 'Hello, from Gradle'
        }
    }

build.gradle.kts

    
    
    ant.importBuild("build.xml")
    
    tasks.named("hello") {
        doLast {
            println("Hello, from Gradle")
        }
    }

输出 `gradle hello`

    
    
    > gradle hello
    
    > Task :hello
    [ant:echo] Hello, from Ant
    Hello, from Gradle
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

Ant目标也有可能依赖Gradle任务：

例子11.依赖于Gradle任务的Ant目标

`Groovy``Kotlin`

build.gradle

    
    
    ant.importBuild 'build.xml'
    
    task intro {
        doLast {
            println 'Hello, from Gradle'
        }
    }

build.gradle.kts

    
    
    ant.importBuild("build.xml")
    
    tasks.register("intro") {
        doLast {
            println("Hello, from Gradle")
        }
    }

build.xml

    
    
    <project>
        <target name="hello" depends="intro">
            <echo>Hello, from Ant</echo>
        </target>
    </project>

输出 `gradle hello`

    
    
    > gradle hello
    
    > Task :intro
    Hello, from Gradle
    
    > Task :hello
    [ant:echo] Hello, from Ant
    
    BUILD SUCCESSFUL in 0s
    2 actionable tasks: 2 executed

有时可能需要“重命名”为Ant目标生成的任务，以避免与现有Gradle任务发生命名冲突。为此，请使用[AntBuilder.importBuild（java.lang.Object，org.gradle.api.Transformer）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/AntBuilder.html#importBuild-
java.lang.Object-org.gradle.api.Transformer-)方法。

例子12.重命名导入的Ant目标

`Groovy``Kotlin`

build.gradle

    
    
    ant.importBuild('build.xml') { antTargetName ->
        'a-' + antTargetName
    }

build.gradle.kts

    
    
    ant.importBuild("build.xml") { antTargetName ->
        "a-" + antTargetName
    }

build.xml

    
    
    <project>
        <target name="hello">
            <echo>Hello, from Ant</echo>
        </target>
    </project>

输出 `gradle a-hello`

    
    
    > gradle a-hello
    
    > Task :a-hello
    [ant:echo] Hello, from Ant
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

请注意，尽管此方法的第二个参数应该是[Transformer](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Transformer.html)，但在Groovy中进行编程时，由于[Groovy支持将闭包自动强制转换为单抽象方法类型，因此](http://mrhaki.blogspot.ie/2013/11/groovy-
goodness-implicit-closure.html)我们可以简单地使用闭包而不是匿名内部类（或类似的内部类）。

## [Ant属性和参考](#Ant属性和参考)

有几种方法可以设置Ant属性，以便Ant任务可以使用该属性。您可以直接在`AntBuilder`实例上设置属性。Ant属性也可以作为Map来使用，您可以对其进行更改。您还可以使用Ant`property`任务。以下是一些有关如何执行此操作的示例。

例子13.设置一个Ant属性

`Groovy``Kotlin`

build.gradle

    
    
    ant.buildDir = buildDir
    ant.properties.buildDir = buildDir
    ant.properties['buildDir'] = buildDir
    ant.property(name: 'buildDir', location: buildDir)

build.gradle.kts

    
    
    ant.setProperty("buildDir", buildDir)
    ant.properties.set("buildDir", buildDir)
    ant.properties["buildDir"] = buildDir
    ant.withGroovyBuilder {
        "property"("name" to "buildDir", "location" to "buildDir")
    }

许多Ant任务在执行时都会设置属性。有几种方法可以获取这些属性的值。您可以直接从`AntBuilder`实例获取属性。Ant属性也可以作为Map使用。以下是一些示例。

例子14.获得一个Ant属性

build.xml

    
    
    <property name="antProp" value="a property defined in an Ant build"/>

`Groovy``Kotlin`

build.gradle

    
    
    println ant.antProp
    println ant.properties.antProp
    println ant.properties['antProp']

build.gradle.kts

    
    
    println(ant.getProperty("antProp"))
    println(ant.properties.get("antProp"))
    println(ant.properties["antProp"])

有几种方法可以设置Ant引用：

例子15.设置一个Ant引用

`Groovy``Kotlin`

build.gradle

    
    
    ant.path(id: 'classpath', location: 'libs')
    ant.references.classpath = ant.path(location: 'libs')
    ant.references['classpath'] = ant.path(location: 'libs')

build.gradle.kts

    
    
    ant.withGroovyBuilder { "path"("id" to "classpath", "location" to "libs") }
    ant.references.set("classpath", ant.withGroovyBuilder { "path"("location" to "libs") })
    ant.references["classpath"] = ant.withGroovyBuilder { "path"("location" to "libs") }

build.xml

    
    
    <path refid="classpath"/>

有几种获取Ant参考的方法：

例子16.获取一个Ant参考

build.xml

    
    
    <path id="antPath" location="libs"/>

`Groovy``Kotlin`

build.gradle

    
    
    println ant.references.antPath
    println ant.references['antPath']

build.gradle.kts

    
    
    println(ant.references.get("antPath"))
    println(ant.references["antPath"])

## [Ant记录](#Ant记录)

Gradle将Ant消息优先级映射到Gradle日志级别，以便从Ant记录的消息出现在Gradle输出中。默认情况下，这些映射如下：

表1. Ant消息优先级映射 

Ant消息优先级 | Gradle日志级别  
---|---  
_VERBOSE_|`DEBUG`  
_DEBUG_|`DEBUG`  
_INFO_|`INFO`  
_WARN_|`WARN`  
_ERROR_|`ERROR`  
  
### [微调Ant日志记录](#微调Ant日志记录)

Ant消息优先级到Gradle日志级别的默认映射有时可能会出现问题。例如，没有消息优先级直接映射到`LIFECYCLE`日志级别，这是Gradle的默认值。许多Ant任务以
_INFO_ 优先级记录消息，这意味着要从Gradle公开这些消息，必须在将日志级别设置为的情况下运行构建`INFO`，从而可能记录比期望更多的输出。

相反，如果Ant任务记录消息的级别过高，则要抑制这些消息，将需要以更高的日志级别运行构建，例如`QUIET`。但是，这可能导致其他期望的输出被抑制。

为了解决这个问题，Gradle允许用户微调Ant日志并控制消息优先级到Gradle日志级别的映射。这是通过`LIFECYCLE`使用[AntBuilder.setLifecycleLogLevel（java.lang.String）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/AntBuilder.html#setLifecycleLogLevel-
java.lang.String-)方法设置应映射到默认Gradle日志级别的优先级来完成的。设置此值后，以配置的优先级或更高优先级记录的任何Ant消息将至少记录在`LIFECYCLE`。低于此优先级记录的任何Ant消息最多将记录为`INFO`。

例如，以下内容更改了映射，以便在日志级别公开Ant _INFO_ 优先级消息`LIFECYCLE`。

例子17.调整Ant日志

`Groovy``Kotlin`

build.gradle

    
    
    ant.lifecycleLogLevel = "INFO"
    
    task hello {
        doLast {
            ant.echo(level: "info", message: "hello from info priority!")
        }
    }

build.gradle.kts

    
    
    ant.lifecycleLogLevel = AntBuilder.AntMessagePriority.INFO
    
    tasks.register("hello") {
        doLast {
            ant.withGroovyBuilder {
                "echo"("level" to "info", "message" to "hello from info priority!")
            }
        }
    }

输出 `gradle hello`

    
    
    > gradle hello
    
    > Task :hello
    [ant:echo] hello from info priority!
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

另一方面，如果将`lifecycleLogLevel`设置为 _ERROR_ ，则将不再以日志级别记录以 _WARN_
优先级记录的Ant消息`WARN`。现在将以该`INFO`级别记录它们，并且默认情况下将其禁止显示。

## [API](#API)

Ant集成由[AntBuilder](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/AntBuilder.html)提供。

* * *

[1](#_footnoteref_1)。在Groovy中，您可以执行字符串。要了解有关使用Groovy执行外部流程的更多信息，请参阅“
Groovy in Action” 9.3.2或Groovy Wiki。

