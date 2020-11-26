# 从Gradle使用Ant

内容

* [在构建中使用Ant任务和类型](#sec:using_ant_tasks)
* [导入Ant构建](#sec:import_ant_build)
* [蚂蚁属性和参考](#sec:ant_properties)
* [蚂蚁记录](#sec:ant_logging)
* [API](#sec:ant_api)

Gradle提供了与Ant的出色集成。您可以在Gradle构建中使用单个Ant任务或整个Ant构建。实际上，您会发现，在Gradle构建脚本中使用Ant任务比使用Ant的XML格式要容易得多，功能更强大。您甚至可以简单地将Gradle用作强大的Ant任务脚本工具。

蚂蚁可以分为两层。第一层是Ant语言。它提供了`build.xml`文件的语法，目标的处理，诸如macrodefs之类的特殊构造，等等。换句话说，除Ant任务和类型以外的所有内容。Gradle理解这种语言，并允许您将Ant`build.xml`直接导入Gradle项目。然后，您可以像使用Gradle任务一样使用Ant构建的目标。

蚂蚁的第二层是其丰富的Ant任务和类型，如`javac`，`copy`或`jar`。对于这一层，Gradle只需依靠Groovy即可实现集成`AntBuilder`。

最后，由于构建脚本是Groovy脚本，因此您始终可以将Ant构建作为外部进程执行。构建脚本会含有类似的语句：`"ant clean compile".execute()`。\[[1](#_footnotedef_1 "查看脚注。")\]

您可以使用Gradle的Ant集成作为将构建从Ant迁移到Gradle的路径。例如，您可以从导入现有的Ant构建开始。然后，您可以将依赖项声明从Ant脚本移至构建文件。最后，您可以将任务移到构建文件中，或用Gradle的某些插件替换它们。随着时间的流逝，该过程可以分几步完成，并且在整个过程中，您都可以使用有效的Gradle构建。

## [](#sec:using_ant_tasks)[在构建中使用Ant任务和类型](#sec:using_ant_tasks)

在您的构建脚本中，`ant`Gradle提供了一个名为的属性。这是对[AntBuilder]()实例的引用。这`AntBuilder`用于从构建脚本访问Ant任务，类型和属性。从Ant`build.xml`格式到Groovy有一个非常简单的映射，下面将进行解释。

您可以通过在`AntBuilder`实例上调用方法来执行Ant任务。您使用任务名称作为方法名称。例如，您可以`echo`通过调用`ant.echo()`方法来执行Ant任务。Ant任务的属性作为Map参数传递给方法。下面是`echo`任务的示例。注意，我们还可以混合使用Groovy代码和Ant任务标记。这可能非常强大。

示例1.使用Ant任务

`Groovy``Kotlin`

build.gradle

task hello \{ doLast \{ String greeting = 'hello from Ant' ant.echo\(message: greeting\) \} \}

 

1

task hello \{

2

 doLast \{

3

 String greeting \= 'hello from Ant'

4

 ant.echo\(message: greeting\)

5

 \}

6

\}

输出`gradle hello`

\> gradle你好
 >任务：你好 \[ant：echo\]来自Ant的问好
 在0秒内成功建立 1个可执行的任务：1个已执行

您可以通过将嵌套的文本作为任务方法调用的参数传递给Ant任务。在此示例中，我们`echo`以嵌套文本形式传递任务消息：

示例2.将嵌套文本传递给Ant任务

`Groovy``Kotlin`

build.gradle

task hello \{ doLast \{ ant.echo\('hello from Ant'\) \} \}

 

1

task hello \{

2

 doLast \{

3

 ant.echo\('hello from Ant'\)

4

 \}

5

\}

输出`gradle hello`

\> gradle你好
 >任务：你好 \[ant：echo\]来自Ant的问好
 在0秒内成功建立 1个可执行的任务：1个已执行

您将嵌套元素传递给闭包内部的Ant任务。通过调用与我们要定义的元素同名的方法，以与任务相同的方式定义嵌套元素。

例子3.将嵌套元素传递给Ant任务

`Groovy``Kotlin`

build.gradle

task zip \{ doLast \{ ant.zip\(destfile: 'archive.zip'\) \{ fileset\(dir: 'src'\) \{ include\(name: '\*\*.xml'\) exclude\(name: '\*\*.java'\) \} \} \} \}

 

1

task zip \{

2

 doLast \{

3

 ant.zip\(destfile: 'archive.zip'\) \{

4

 fileset\(dir: 'src'\) \{

5

 include\(name: '\*\*.xml'\)

6

 exclude\(name: '\*\*.java'\)

7

 \}

8

 \}

9

 \}

10

\}

您可以使用与访问任务相同的方式来访问Ant类型，方法是将类型的名称用作方法名称。方法调用返回Ant数据类型，然后可以在构建脚本中直接使用它。在以下示例中，我们创建一个Ant`path`对象，然后遍历它的内容。

例子4.使用Ant类型

`Groovy``Kotlin`

build.gradle

task list \{ doLast \{ def path = ant.path \{ fileset\(dir: 'libs', includes: '\*.jar'\) \} path.list\(\).each \{ println it \} \} \}

 

1

task list \{

2

 doLast \{

3

 def path \= ant.path \{

4

 fileset\(dir: 'libs', includes: '\*.jar'\)

5

 \}

6

 path.list\(\).each \{

7

 println it

8

 \}

9

 \}

10

\}

有关更多信息，请`AntBuilder`参见“ Groovy in Action” 8.4或[Groovy Wiki中的内容](https://groovy-lang.org/scripting-ant.html)。

### [](#sec:using_custom_ant_tasks)[在构建中使用定制的Ant任务](#sec:using_custom_ant_tasks)

要使自定义任务在构建中可用，可以像使用文件一样使用`taskdef`（通常更容易）或`typedef`Ant任务`build.xml`。然后，您可以像内置Ant任务一样引用自定义Ant任务。

例子5.使用定制的Ant任务

`Groovy``Kotlin`

build.gradle

task check \{ doLast \{ ant.taskdef\(resource: 'checkstyletask.properties'\) \{ classpath \{ fileset\(dir: 'libs', includes: '\*.jar'\) \} \} ant.checkstyle\(config: 'checkstyle.xml'\) \{ fileset\(dir: 'src'\) \} \} \}

 

1

task check \{

2

 doLast \{

3

 ant.taskdef\(resource: 'checkstyletask.properties'\) \{

4

 classpath \{

5

 fileset\(dir: 'libs', includes: '\*.jar'\)

6

 \}

7

 \}

8

 ant.checkstyle\(config: 'checkstyle.xml'\) \{

9

 fileset\(dir: 'src'\)

10

 \}

11

 \}

12

\}

您可以使用Gradle的依赖关系管理来组装用于自定义任务的类路径。为此，您需要为类路径定义一个自定义配置，然后向该配置中添加一些依赖项。这在[声明依赖项]()中有更详细的描述。

示例6.声明自定义Ant任务的类路径

`Groovy``Kotlin`

build.gradle

configurations \{ pmd \} dependencies \{ pmd group: 'pmd', name: 'pmd', version: '4.2.5' \}

 

1

configurations \{

2

 pmd

3

\}

4

5

dependencies \{

6

 pmd group: 'pmd', name: 'pmd', version: '4.2.5'

7

\}

要使用类路径配置，请使用`asPath`定制配置的属性。

示例7.一起使用定制Ant任务和依赖项管理

`Groovy``Kotlin`

build.gradle

task check \{ doLast \{ ant.taskdef\(name: 'pmd', classname: 'net.sourceforge.pmd.ant.PMDTask', classpath: configurations.pmd.asPath\) ant.pmd\(shortFilenames: 'true', failonruleviolation: 'true', rulesetfiles: file\('pmd-rules.xml'\).toURI\(\).toString\(\)\) \{ formatter\(type: 'text', toConsole: 'true'\) fileset\(dir: 'src'\) \} \} \}

 

1

task check \{

2

 doLast \{

3

 ant.taskdef\(name: 'pmd',

4

 classname: 'net.sourceforge.pmd.ant.PMDTask',

5

 classpath: configurations.pmd.asPath\)

6

 ant.pmd\(shortFilenames: 'true',

7

 failonruleviolation: 'true',

8

 rulesetfiles: file\('pmd-rules.xml'\).toURI\(\).toString\(\)\) \{

9

 formatter\(type: 'text', toConsole: 'true'\)

10

 fileset\(dir: 'src'\)

11

 \}

12

 \}

13

\}

## [](#sec:import_ant_build)[导入Ant构建](#sec:import_ant_build)

您可以使用该`ant.importBuild()`方法将Ant构建导入到Gradle项目中。导入Ant构建时，每个Ant目标都被视为Gradle任务。这意味着您可以按照与Gradle任务完全相同的方式来操纵和执行Ant目标。

例子8.导入一个Ant版本

`Groovy``Kotlin`

build.gradle

ant.importBuild 'build.xml'

 

1

ant.importBuild 'build.xml'

build.xml

\<project> \<target name="hello"> \<echo>Hello, from Ant\</echo> \</target> \</project>

 

1

\<project\>

2

 \<target name\="hello"\>

3

 \<echo\>Hello, from Ant\</echo\>

4

 \</target\>

5

\</project\>

输出`gradle hello`

\> gradle你好
 >任务：你好 \[ant：echo\]您好，来自蚂蚁
 在0秒内成功建立 1个可执行的任务：1个已执行

您可以添加一个依赖于Ant目标的任务：

例子9.依赖于Ant目标的任务

`Groovy``Kotlin`

build.gradle

ant.importBuild 'build.xml' task intro\(dependsOn: hello\) \{ doLast \{ println 'Hello, from Gradle' \} \}

 

1

ant.importBuild 'build.xml'

2

3

task intro\(dependsOn: hello\) \{

4

 doLast \{

5

 println 'Hello, from Gradle'

6

 \}

7

\}

输出`gradle intro`

\> gradle介绍
 >任务：你好 \[ant：echo\]您好，来自蚂蚁
 >任务：简介 你好，来自Gradle
 在0秒内成功建立 2个可执行任务：2个已执行

或者，您可以将行为添加到Ant目标：

例子10.向Ant目标添加行为

`Groovy``Kotlin`

build.gradle

ant.importBuild 'build.xml' hello \{ doLast \{ println 'Hello, from Gradle' \} \}

 

1

ant.importBuild 'build.xml'

2

3

hello \{

4

 doLast \{

5

 println 'Hello, from Gradle'

6

 \}

7

\}

输出`gradle hello`

\> gradle你好
 >任务：你好 \[ant：echo\]您好，来自蚂蚁 你好，来自Gradle
 在0秒内成功建立 1个可执行的任务：1个已执行

Ant目标也有可能依赖Gradle任务：

例子11.依赖于Gradle任务的Ant目标

`Groovy``Kotlin`

build.gradle

ant.importBuild 'build.xml' task intro \{ doLast \{ println 'Hello, from Gradle' \} \}

 

1

ant.importBuild 'build.xml'

2

3

task intro \{

4

 doLast \{

5

 println 'Hello, from Gradle'

6

 \}

7

\}

build.xml

\<project> \<target name="hello" depends="intro"> \<echo>Hello, from Ant\</echo> \</target> \</project>

 

1

\<project\>

2

 \<target name\="hello" depends\="intro"\>

3

 \<echo\>Hello, from Ant\</echo\>

4

 \</target\>

5

\</project\>

输出`gradle hello`

\> gradle你好
 >任务：简介 你好，来自Gradle
 >任务：你好 \[ant：echo\]您好，来自蚂蚁
 在0秒内成功建立 2个可执行任务：2个已执行

有时可能需要“重命名”为Ant目标生成的任务，以避免与现有Gradle任务发生命名冲突。为此，请使用[AntBuilder.importBuild（java.lang.Object，org.gradle.api.Transformer）]()方法。

例子12.重命名导入的Ant目标

`Groovy``Kotlin`

build.gradle

ant.importBuild\('build.xml'\) \{ antTargetName -> 'a-' + antTargetName \}

 

1

ant.importBuild\('build.xml'\) \{ antTargetName \->

2

 'a-' + antTargetName

3

\}

build.xml

\<project> \<target name="hello"> \<echo>Hello, from Ant\</echo> \</target> \</project>

 

1

\<project\>

2

 \<target name\="hello"\>

3

 \<echo\>Hello, from Ant\</echo\>

4

 \</target\>

5

\</project\>

输出`gradle a-hello`

\>摇一摇你好
 >任务：你好 \[ant：echo\]您好，来自蚂蚁
 在0秒内成功建立 1个可执行的任务：1个已执行

请注意，虽然此方法的第二个参数应该是[Transformer]()，但在Groovy中进行编程时，由于[Groovy支持将闭包自动强制转换为单抽象方法类型，因此](http://mrhaki.blogspot.ie/2013/11/groovy-goodness-implicit-closure.html)我们可以简单地使用闭包而不是匿名内部类（或类似的内部类）。

## [](#sec:ant_properties)[蚂蚁属性和参考](#sec:ant_properties)

有几种方法可以设置Ant属性，以便Ant任务可以使用该属性。您可以直接在`AntBuilder`实例上设置属性。Ant属性也可以作为Map来使用，您可以对其进行更改。您还可以使用Ant`property`任务。下面是一些有关如何执行此操作的示例。

例子13.设置一个Ant属性

`Groovy``Kotlin`

build.gradle

ant.buildDir = buildDir ant.properties.buildDir = buildDir ant.properties\['buildDir'\] = buildDir ant.property\(name: 'buildDir', location: buildDir\)

 

1

ant.buildDir \= buildDir

2

ant.properties.buildDir \= buildDir

3

ant.properties\['buildDir'\] \= buildDir

4

ant.property\(name: 'buildDir', location: buildDir\)

许多Ant任务在执行时都会设置属性。有几种方法可以获取这些属性的值。您可以直接从`AntBuilder`实例获取属性。Ant属性也可以作为Map使用。以下是一些示例。

例子14.获得一个Ant属性

build.xml

\<property name="antProp" value="a property defined in an Ant build"/>

 

1

\<property name\="antProp" value\="a property defined in an Ant build"/>

`Groovy``Kotlin`

build.gradle

println ant.antProp println ant.properties.antProp println ant.properties\['antProp'\]

 

1

println ant.antProp

2

println ant.properties.antProp

3

println ant.properties\['antProp'\]

有几种方法可以设置Ant引用：

例子15.设置一个Ant引用

`Groovy``Kotlin`

build.gradle

ant.path\(id: 'classpath', location: 'libs'\) ant.references.classpath = ant.path\(location: 'libs'\) ant.references\['classpath'\] = ant.path\(location: 'libs'\)

 

1

ant.path\(id: 'classpath', location: 'libs'\)

2

ant.references.classpath \= ant.path\(location: 'libs'\)

3

ant.references\['classpath'\] \= ant.path\(location: 'libs'\)

build.xml

\<path refid="classpath"/>

 

1

\<path refid\="classpath"/>

有几种获取Ant参考的方法：

例子16.获取一个Ant参考

build.xml

\<path id="antPath" location="libs"/>

 

1

\<path id\="antPath" location\="libs"/>

`Groovy``Kotlin`

build.gradle

println ant.references.antPath println ant.references\['antPath'\]

 

1

println ant.references.antPath

2

println ant.references\['antPath'\]

## [](#sec:ant_logging)[蚂蚁记录](#sec:ant_logging)

Gradle将Ant消息优先级映射到Gradle日志级别，以便从Ant记录的消息出现在Gradle输出中。默认情况下，这些映射如下：

表1. Ant消息优先级映射
| 蚂蚁消息优先级 | 摇篮日志级别 |
| --- | --- |
| 
_详细_





 | 

`DEBUG`





 |
| 

_调试_





 | 

`DEBUG`





 |
| 

_信息_





 | 

`INFO`





 |
| 

_警告_





 | 

`WARN`





 |
| 

_错误_





 | 

`ERROR`





 |

### [](#sec:fine_tuning_ant_logging)[微调Ant日志记录](#sec:fine_tuning_ant_logging)

Ant消息优先级到Gradle日志级别的默认映射有时可能会出现问题。例如，没有消息优先级直接映射到`LIFECYCLE`日志级别，这是Gradle的默认值。许多Ant任务以_INFO_优先级记录消息，这意味着要从Gradle公开这些消息，必须在将日志级别设置为的情况下运行构建`INFO`，从而可能记录比期望更多的输出。

相反，如果Ant任务记录消息的级别过高，则要抑制这些消息，将需要以更高的日志级别运行构建，例如`QUIET`。但是，这可能导致抑制其他期望的输出。

为了解决这个问题，Gradle允许用户微调Ant日志并控制消息优先级到Gradle日志级别的映射。这是通过`LIFECYCLE`使用[AntBuilder.setLifecycleLogLevel（java.lang.String）]()方法设置应映射到默认Gradle日志级别的优先级来完成的。设置此值后，以配置的优先级或更高优先级记录的任何Ant消息将至少记录在`LIFECYCLE`。低于此优先级记录的任何Ant消息最多将记录为`INFO`。

例如，以下内容更改了映射，以便在日志级别公开Ant_INFO_优先级消息`LIFECYCLE`。

例子17.调整Ant日志

`Groovy``Kotlin`

build.gradle

ant.lifecycleLogLevel = "INFO" task hello \{ doLast \{ ant.echo\(level: "info", message: "hello from info priority\!"\) \} \}

 

1

ant.lifecycleLogLevel \= "INFO"

2

3

task hello \{

4

 doLast \{

5

 ant.echo\(level: "info", message: "hello from info priority\!"\)

6

 \}

7

\}

输出`gradle hello`

\> gradle你好
 >任务：你好 \[ant：echo\]信息优先！
 在0秒内成功建立 1个可执行的任务：1个已执行

另一方面，如果将`lifecycleLogLevel`设置为_ERROR_，则将不再以日志级别记录以_WARN_优先级记录的Ant消息`WARN`。现在，它们将以该`INFO`级别记录，并且默认情况下将被禁止。

## [](#sec:ant_api)[API](#sec:ant_api)

Ant集成由[AntBuilder]()提供。

* * *

[1](#_footnoteref_1)。在Groovy中，您可以执行字符串。要了解有关使用Groovy执行外部流程的更多信息，请参阅“ Groovy in Action” 9.3.2或Groovy Wiki。