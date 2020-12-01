# 开发自定义Gradle任务类型


内容

  * [打包任务类](#%E6%89%93%E5%8C%85%E4%BB%BB%E5%8A%A1%E7%B1%BB)
  * [编写一个简单的任务类](#%E7%BC%96%E5%86%99%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84%E4%BB%BB%E5%8A%A1%E7%B1%BB)
  * [一个独立的项目](#%E4%B8%80%E4%B8%AA%E7%8B%AC%E7%AB%8B%E7%9A%84%E9%A1%B9%E7%9B%AE)
  * [增量任务](#%E5%A2%9E%E9%87%8F%E4%BB%BB%E5%8A%A1)
  * [声明和使用命令行选项](#%E5%A3%B0%E6%98%8E%E5%92%8C%E4%BD%BF%E7%94%A8%E5%91%BD%E4%BB%A4%E8%A1%8C%E9%80%89%E9%A1%B9)
  * [工作者API](#%E5%B7%A5%E4%BD%9C%E8%80%85API)
  * [取消和超时](#%E5%8F%96%E6%B6%88%E5%92%8C%E8%B6%85%E6%97%B6)
  * [更多细节](#%E6%9B%B4%E5%A4%9A%E7%BB%86%E8%8A%82)

Gradle支持两种类型的任务。一种这样的类型是简单任务，您可以在其中用动作关闭定义任务。我们已经在[Build Script Basics中](/md/构建脚本基础.md#tutorial_using_tasks)看到了这些。对于这种类型的任务，动作关闭确定任务的行为。这种类型的任务非常适合在构建脚本中实施一次性任务。

任务的另一种类型是增强型任务，其中行为内置于任务中，并且该任务提供了一些可用于配置行为的属性。我们已经在[创作任务中](/md/处理任务.md#more_about_tasks)看到了这些。大多数Gradle插件使用增强的任务。使用增强型任务，您无需像处理简单任务那样实现任务行为。您只需声明任务并使用其属性配置任务。通过这种方式，增强的任务使您可以在许多不同的地方（可能跨不同的构建）重用某种行为。

增强型任务的行为和属性由任务的类定义。声明增强的任务时，可以指定任务的类型或类。

在Gradle中实现自己的自定义任务类很容易。您可以使用任何您喜欢的语言来实现自定义任务类，只要最终将其编译为JVM字节码即可。在我们的示例中，我们将使用Groovy作为实现语言。Groovy，Java或Kotlin都是用于实现任务类的语言，都是不错的选择，因为Gradle
API被设计为可以与这些语言很好地配合使用。通常，使用Java或Kotlin（静态类型）实现的任务将比使用Groovy实现的任务执行得更好。

## [打包任务类](#%E6%89%93%E5%8C%85%E4%BB%BB%E5%8A%A1%E7%B1%BB)

您可以在几个地方放置任务类的源代码。

构建脚本

    

您可以将任务类直接包含在构建脚本中。这样的好处是，任务类将自动编译并包含在构建脚本的类路径中，而无需执行任何操作。但是，任务类在构建脚本之外不可见，因此您不能在定义该任务的构建脚本之外重用该任务类。

`buildSrc` 项目

    

您可以将任务类的源放在` _rootProjectDir_ /buildSrc/src/main/groovy`目录中（` _rootProjectDir_
/buildSrc/src/main/java`或` _rootProjectDir_
/buildSrc/src/main/kotlin`根据您喜欢的语言）。Gradle将负责编译和测试任务类，并使其在构建脚本的类路径中可用。任务类对于构建所使用的每个构建脚本都是可见的。但是，它在构建外部不可见，因此您无法在定义该构建的外部重用任务类。使用`buildSrc`项目方法将任务声明（即任务应执行的操作）与任务实现分开来：是，任务如何完成。

有关[项目](/md/组织Gradle项目.md#organizing_gradle_projects)的更多详细信息，请参见[组织Gradle](/md/组织Gradle项目.md#organizing_gradle_projects)`buildSrc`项目。

独立项目

    

您可以为任务类创建一个单独的项目。这个项目产生并发布了一个JAR，您可以在多个版本中使用它并与他人共享。通常，此JAR可能包含一些自定义插件，或将几个相关的任务类捆绑到一个库中。或两者的某种组合。

在我们的示例中，我们将从构建脚本中的任务类开始，以使事情变得简单。然后，我们将考虑创建一个独立的项目。

## [编写一个简单的任务类](#%E7%BC%96%E5%86%99%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84%E4%BB%BB%E5%8A%A1%E7%B1%BB)

要实现自定义任务类，可以扩展[DefaultTask](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.DefaultTask.html)。

例子1.定义一个自定义任务

`Groovy``Kotlin`

build.gradle

    
    
    class GreetingTask extends DefaultTask {
    }

build.gradle.kts

    
    
    open class GreetingTask : DefaultTask() {
    }

该任务没有做任何有用的事情，因此让我们添加一些行为。为此，我们向任务添加一个方法，并使用[TaskAction](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/TaskAction.html)批注对其进行标记。任务执行时，Gradle将调用该方法。您不必使用方法来定义任务的行为。例如，您可以在任务构造函数中调用`doFirst()`或`doLast()`使用闭包来添加行为。

例子2.你好世界任务

`Groovy``Kotlin`

build.gradle

    
    
    class GreetingTask extends DefaultTask {
        @TaskAction
        def greet() {
            println 'hello from GreetingTask'
        }
    }
    
    // Create a task using the task type
    task hello(type: GreetingTask)

build.gradle.kts

    
    
    open class GreetingTask : DefaultTask() {
        @TaskAction
        fun greet() {
            println("hello from GreetingTask")
        }
    }
    
    // Create a task using the task type
    tasks.register<GreetingTask>("hello")

输出 **`gradle -q hello`**

    
    
    > gradle -q hello
    hello from GreetingTask

让我们为任务添加一个属性，以便我们可以对其进行自定义。任务只是POGO，声明任务时，可以在任务对象上设置属性或调用方法。在这里，我们添加一个`greeting`属性，并在声明`greeting`任务时设置值。

例子3.一个可定制的hello world任务

`Groovy``Kotlin`

build.gradle

    
    
    class GreetingTask extends DefaultTask {
        @Input
        String greeting = 'hello from GreetingTask'
    
        @TaskAction
        def greet() {
            println greeting
        }
    }
    
    // Use the default greeting
    task hello(type: GreetingTask)
    
    // Customize the greeting
    task greeting(type: GreetingTask) {
        greeting = 'greetings from GreetingTask'
    }

build.gradle.kts

    
    
    open class GreetingTask : DefaultTask() {
        @get:Input
        var greeting = "hello from GreetingTask"
    
        @TaskAction
        fun greet() {
            println(greeting)
        }
    }
    
    // Use the default greeting
    tasks.register<GreetingTask>("hello")
    
    // Customize the greeting
    tasks.register<GreetingTask>("greeting") {
        greeting = "greetings from GreetingTask"
    }

输出 **`gradle -q hello greeting`**

    
    
    > gradle -q hello greeting
    hello from GreetingTask
    greetings from GreetingTask

## [一个独立的项目](#%E4%B8%80%E4%B8%AA%E7%8B%AC%E7%AB%8B%E7%9A%84%E9%A1%B9%E7%9B%AE)

现在，我们将把任务移到一个独立的项目中，以便我们可以发布它并与他人共享它。这个项目只是一个Groovy项目，它产生一个包含任务类的JAR。这是该项目的简单构建脚本。它应用了Groovy插件，并将Gradle
API添加为编译时依赖项。

例子4.一个自定义任务的构建

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'groovy'
    }
    
    dependencies {
        implementation gradleApi()
    }

build.gradle.kts

    
    
    plugins {
        groovy
    }
    
    dependencies {
        implementation(gradleApi())
    }

我们只是遵循任务类源应该放在哪里的约定。

### [示例：自定义任务](#%E7%A4%BA%E4%BE%8B%EF%BC%9A%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BB%BB%E5%8A%A1)

src / main / groovy / org / gradle / GreetingTask.groovy

    
    
    package org.gradle
    
    import org.gradle.api.DefaultTask
    import org.gradle.api.tasks.TaskAction
    import org.gradle.api.tasks.Input
    
    class GreetingTask extends DefaultTask {
    
        @Input
        String greeting = 'hello from GreetingTask'
    
        @TaskAction
        def greet() {
            println greeting
        }
    }

### [在另一个项目中使用您的任务类](#%E5%9C%A8%E5%8F%A6%E4%B8%80%E4%B8%AA%E9%A1%B9%E7%9B%AE%E4%B8%AD%E4%BD%BF%E7%94%A8%E6%82%A8%E7%9A%84%E4%BB%BB%E5%8A%A1%E7%B1%BB)

要在构建脚本中使用任务类，您需要将该类添加到构建脚本的类路径中。为此，请使用一个`buildscript {
}`块，如[构建脚本的外部依赖项中所述](/md/构建脚本基础.md#构建脚本的外部依赖关系)。以下示例显示了包含任务类的JAR已发布到本地存储库时如何执行此操作：

例子5.在另一个项目中使用自定义任务

`Groovy``Kotlin`

build.gradle

    
    
    buildscript {
        repositories {
            maven {
                url = uri(repoLocation)
            }
        }
        dependencies {
            classpath 'org.gradle:task:1.0-SNAPSHOT'
        }
    }
    
    task greeting(type: org.gradle.GreetingTask) {
        greeting = 'howdy!'
    }

build.gradle.kts

    
    
    buildscript {
        repositories {
            maven {
                url = uri(repoLocation)
            }
        }
        dependencies {
            classpath("org.gradle:task:1.0-SNAPSHOT")
        }
    }
    
    tasks.register<org.gradle.GreetingTask>("greeting") {
        greeting = "howdy!"
    }

### [为任务类编写测试](#%E4%B8%BA%E4%BB%BB%E5%8A%A1%E7%B1%BB%E7%BC%96%E5%86%99%E6%B5%8B%E8%AF%95)

您可以使用[ProjectBuilder](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testfixtures/ProjectBuilder.html)类创建在测试任务类时要使用的[Project](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html)实例。

### [示例：测试自定义任务](#%E7%A4%BA%E4%BE%8B%EF%BC%9A%E6%B5%8B%E8%AF%95%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BB%BB%E5%8A%A1)

src / test / groovy / org / gradle / GreetingTaskTest.groovy

    
    
    class GreetingTaskTest {
        @Test
        void canAddTaskToProject() {
            Project project = ProjectBuilder.builder().build()
            def task = project.task('greeting', type: GreetingTask)
            assertTrue(task instanceof GreetingTask)
        }
    }

## [增量任务](#%E5%A2%9E%E9%87%8F%E4%BB%BB%E5%8A%A1)

使用Gradle，实现一个在所有输入和输出都是最新的情况下将被跳过的任务非常简单（请参阅“[增量构建”](/md/处理任务.md#最新检查（又称增量构建）)）。但是，自上次执行以来，有时只有少数输入文件已更改，因此您希望避免重新处理所有未更改的输入。这对于将输入文件按1：1转换为输出文件的转换器任务特别有用。

如果您想优化构建，以便仅处理过时的输入文件，则可以使用 _增量任务来完成_ 。

╔═════════════════════════════  

有[IncrementalTask​​Inputs](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/incremental/IncrementalTaskInputs.html)
API，在5.4之前的Gradle版本中可用。使用[IncrementalTask​​Inputs时](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/incremental/IncrementalTaskInputs.html)，只能查询任务输入的所有文件更改。无法查询各个输入文件属性的更改。而且，旧的API不能区分增量任务输入和非增量任务输入，因此任务本身需要确定更改的来源。因此，不建议使用此API，并最终将其删除。本文记录了新的[InputChanges](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html)
API，它取代了旧的API并解决了其缺点。如果需要使用旧的API，请查看[Gradle 5.3.1用户手册](https://docs.gradle.org/5.3.1/userguide/custom_tasks.html#%E5%A2%9E%E9%87%8F%E4%BB%BB%E5%8A%A1)中的文档。  
  
╚═════════════════════════════    
  
### [实施增量任务](#%E5%AE%9E%E6%96%BD%E5%A2%9E%E9%87%8F%E4%BB%BB%E5%8A%A1)

为了使任务增量处理输入，该任务必须包含 _增量任务action_
。这是一个具有单个[InputChanges](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html)参数的任务操作方法。该参数告诉Gradle该操作仅想处理更改的输入。另外，任务需要使用[@Incremental](https://docs.gradle.org/6.7.1/javadoc/org/gradle/work/Incremental.html)或[@SkipWhenEmpty](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/SkipWhenEmpty.html)声明至少一个增量文件输入属性。

╔═════════════════════════════  

要查询输入文件属性的增量更改，该属性始终需要返回相同的实例。完成此操作的最简单方法是对此类属性使用以下类型之一：[RegularFileProperty](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/RegularFileProperty.html)，[DirectoryProperty](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/DirectoryProperty.html)或[ConfigurableFileCollection](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/ConfigurableFileCollection.html)。

您可以详细了解`RegularFileProperty`，并`DirectoryProperty`在[懒惰配置](/md/延迟配置.md#lazy_configuration)一章，特别是在部分[使用只读和可配置性](/md/延迟配置.md#惰性集合)和[懒惰的文件属性](/md/延迟配置.md#处理文件)。  
  
╚═════════════════════════════    
  
增量任务操作可以使用[InputChanges.getFileChanges（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))来找出哪些文件已更改为给定的基于文件的输入特性，是类型的它`RegularFileProperty`，`DirectoryProperty`或`ConfigurableFileCollection`。该方法返回[FileChanges](https://docs.gradle.org/6.7.1/javadoc/org/gradle/work/FileChange.html)`Iterable`类型的，然后可以查询以下类型：[](https://docs.gradle.org/6.7.1/javadoc/org/gradle/work/FileChange.html)

  * 在[受影响的文件](https://docs.gradle.org/6.7.1/javadoc/org/gradle/work/FileChange.html#getFile--)

  * 的[变化类型](https://docs.gradle.org/6.7.1/javadoc/org/gradle/work/FileChange.html#getChangeType--)（`ADDED`，`REMOVED`或`MODIFIED`）

  * 更改文件的[规范化路径](https://docs.gradle.org/6.7.1/javadoc/org/gradle/work/FileChange.html#getNormalizedPath--)

  * 更改文件的[文件类型](https://docs.gradle.org/6.7.1/javadoc/org/gradle/work/FileChange.html#getFileType--)

以下示例演示了具有目录输入的增量任务。假定目录包含文本文件的集合，并将它们复制到输出目录，以反转每个文件中的文本。需要注意的关键事项是`inputDir`属性的类型，其注释以及action（`execute()`）如何`getFileChanges()`处理自上次构建以来实际上已更改的文件子集。如果还删除了相应的输入文件，您还可以查看该操作如何删除目标文件：

例子6.定义一个增量任务动作

`Groovy``Kotlin`

build.gradle

    
    
    abstract class IncrementalReverseTask extends DefaultTask {
        @Incremental
        @PathSensitive(PathSensitivity.NAME_ONLY)
        @InputDirectory
        abstract DirectoryProperty getInputDir()
    
        @OutputDirectory
        abstract DirectoryProperty getOutputDir()
    
        @Input
        abstract Property<String> getInputProperty()
    
        @TaskAction
        void execute(InputChanges inputChanges) {
            println(inputChanges.incremental
                ? 'Executing incrementally'
                : 'Executing non-incrementally'
            )
    
            inputChanges.getFileChanges(inputDir).each { change ->
                if (change.fileType == FileType.DIRECTORY) return
    
                println "${change.changeType}: ${change.normalizedPath}"
                def targetFile = outputDir.file(change.normalizedPath).get().asFile
                if (change.changeType == ChangeType.REMOVED) {
                    targetFile.delete()
                } else {
                    targetFile.text = change.file.text.reverse()
                }
            }
        }
    }

build.gradle.kts

    
    
    abstract class IncrementalReverseTask : DefaultTask() {
        @get:Incremental
        @get:PathSensitive(PathSensitivity.NAME_ONLY)
        @get:InputDirectory
        abstract val inputDir: DirectoryProperty
    
        @get:OutputDirectory
        abstract val outputDir: DirectoryProperty
    
        @get:Input
        abstract val inputProperty: Property<String>
    
        @TaskAction
        fun execute(inputChanges: InputChanges) {
            println(
                if (inputChanges.isIncremental) "Executing incrementally"
                else "Executing non-incrementally"
            )
    
            inputChanges.getFileChanges(inputDir).forEach { change ->
                if (change.fileType == FileType.DIRECTORY) return@forEach
    
                println("${change.changeType}: ${change.normalizedPath}")
                val targetFile = outputDir.file(change.normalizedPath).get().asFile
                if (change.changeType == ChangeType.REMOVED) {
                    targetFile.delete()
                } else {
                    targetFile.writeText(change.file.readText().reversed())
                }
            }
        }
    }

如果由于某种原因（例如，通过使用运行）以非增量方式执行任务，则与先前状态无关`--rerun-
tasks`，所有文件都报告为`ADDED`。在这种情况下，Gradle会自动删除以前的输出，因此增量任务仅需要处理给定的文件。

对于像上述示例这样的简单转换器任务，任务操作仅需要为任何过期输入生成输出文件，并为任何已删除输入删除输出文件。

╔═════════════════════════════  

一个任务只能包含一个增量任务动作。  
  
╚═════════════════════════════    
  
### [哪些输入被认为是过时的？](#%E5%93%AA%E4%BA%9B%E8%BE%93%E5%85%A5%E8%A2%AB%E8%AE%A4%E4%B8%BA%E6%98%AF%E8%BF%87%E6%97%B6%E7%9A%84%EF%BC%9F)

当以前执行任务时，自执行以来唯一的更改是增量输入文件属性，则Gradle能够确定需要处理哪些输入文件（增量执行）。在这种情况下，[InputChanges.getFileChanges（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))方法返回
_添加_ ， _修改_ 或 _删除_ 的给定属性的所有输入文件的详细信息。

但是，在许多情况下，Gradle无法确定需要处理哪些输入文件（非增量执行）。示例包括：

  * 没有上一次执行的历史记录。

  * 您正在使用其他版本的Gradle进行构建。当前，Gradle不使用其他版本的任务历史记录。

  * 一个[`upToDateWhen`](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/TaskOutputs.html#upToDateWhen-groovy.lang.Closure-)标准添加到任务的回报`false`。

  * 自上次执行以来，输入属性已更改。

  * 自上次执行以来，非增量输入文件属性已更改。

  * 自上次执行以来，一个或多个输出文件已更改。

在所有这些情况下，Gradle将报告所有输入文件，`ADDED`并且该`getFileChanges()`方法将返回组成给定输入属性的所有文件的详细信息。

您可以使用[InputChanges.isIncremental（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges.html##org.gradle.work.InputChanges:incremental)方法检查任务执行是否为增量执行。

### [增量任务](#%E5%A2%9E%E9%87%8F%E4%BB%BB%E5%8A%A1)

给出的例子增量任务执行[上面](#taskDefinition)，通过基于它的一些场景，让我们走。

首先，考虑一个实例，`IncrementalReverseTask`它是第一次针对一组输入执行的。在这种情况下，所有输入将被视为已添加，如下所示：

例子7.第一次运行增量任务

`Groovy``Kotlin`

build.gradle

    
    
    task incrementalReverse(type: IncrementalReverseTask) {
        inputDir = file('inputs')
        outputDir = file("$buildDir/outputs")
        inputProperty = project.properties['taskInputProperty'] ?: 'original'
    }

build.gradle.kts

    
    
    tasks.register<IncrementalReverseTask>("incrementalReverse") {
        inputDir.set(file("inputs"))
        outputDir.set(file("$buildDir/outputs"))
        inputProperty.set(project.properties["taskInputProperty"] as String? ?: "original")
    }

构建布局

    
    
    。
    ├──build.gradle
    └──inputs
        ├──1.txt
        ├──2.txt
        └──3.txt

输出 `gradle -q incrementalReverse`

    
    
    > gradle -q incrementalReverse
    Executing non-incrementally
    ADDED: 1.txt
    ADDED: 2.txt
    ADDED: 3.txt

自然地，如果再次执行任务且没有任何更改，则整个任务都是最新的，并且不执行任务动作：

例子8.在输入不变的情况下运行增量任务

输出 `gradle incrementalReverse`

    
    
    > gradle incrementalReverse
    > Task :incrementalReverse UP-TO-DATE
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 up-to-date

当以某种方式修改输入文件或添加新的输入文件时，然后重新执行任务将导致这些文件由[InputChanges.getFileChanges（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))返回。下面的示例在运行增量任务之前修改一个文件的内容并添加另一个文件的内容：

示例9.使用更新的输入文件运行增量任务

`Groovy``Kotlin`

build.gradle

    
    
    task updateInputs() {
        doLast {
            file('inputs/1.txt').text = 'Changed content for existing file 1.'
            file('inputs/4.txt').text = 'Content for new file 4.'
        }
    }

build.gradle.kts

    
    
    tasks.register("updateInputs") {
        doLast {
            file("inputs/1.txt").writeText("Changed content for existing file 1.")
            file("inputs/4.txt").writeText("Content for new file 4.")
        }
    }

输出 `gradle -q updateInputs incrementalReverse`

    
    
    > gradle -q updateInputs incrementalReverse
    Executing incrementally
    MODIFIED: 1.txt
    ADDED: 4.txt

╔═════════════════════════════  
各种变异任务（`updateInputs`，`removeInput`等）仅用于演示增量任务的行为。不应将它们视为您应该在自己的构建脚本中拥有的任务或任务实现类型。  
╚═════════════════════════════    
  
删除现有输入文件后，重新执行任务将导致该文件由[InputChanges.getFileChanges（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))返回为`REMOVED`。下面的示例在执行增量任务之前删除现有文件之一：

例子10.在删除输入文件的情况下运行增量任务

`Groovy``Kotlin`

build.gradle

    
    
    task removeInput() {
        doLast {
            file('inputs/3.txt').delete()
        }
    }

build.gradle.kts

    
    
    tasks.register("removeInput") {
        doLast {
            file("inputs/3.txt").delete()
        }
    }

输出 `gradle -q removeInput incrementalReverse`

    
    
    > gradle -q removeInput incrementalReverse
    Executing incrementally
    REMOVED: 3.txt

当 _输出_
文件被删除（或修改）时，Gradle无法确定哪些输入文件已过期。在这种情况下，[InputChanges.getFileChanges（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))返回给定属性的
_所有_
输入文件的详细信息。以下示例仅从构建目录中删除输出文件之一，但请注意如何将所有输入文件视为：[](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))`ADDED`

示例11.在删除输出文件的情况下运行增量任务

`Groovy``Kotlin`

build.gradle

    
    
    task removeOutput() {
        doLast {
            file("$buildDir/outputs/1.txt").delete()
        }
    }

build.gradle.kts

    
    
    tasks.register("removeOutput") {
        doLast {
            file("$buildDir/outputs/1.txt").delete()
        }
    }

输出 `gradle -q removeOutput incrementalReverse`

    
    
    > gradle -q removeOutput incrementalReverse
    Executing non-incrementally
    ADDED: 1.txt
    ADDED: 2.txt
    ADDED: 3.txt

我们要讨论的最后一个场景涉及修改基于非文件的输入属性时发生的情况。在这种情况下，Gradle无法确定属性如何影响任务输出，因此任务将以非增量方式执行。这意味着给定属性的
_所有_
输入文件都由[InputChanges.getFileChanges（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))返回，并且都被视为`ADDED`。下面的示例`taskInputProperty`在运行`incrementalReverse`任务时将project属性设置为新值，并且该项目属性用于初始化任务的`inputProperty`属性，如[本节](#ex:incremental_task_definition)的[第一个示例所示](#ex:incremental_task_definition)。在这种情况下，您可以期待以下输出：

例子12.在输入属性改变的情况下运行增量任务

输出 `gradle -q -PtaskInputProperty=changed incrementalReverse`

    
    
    > gradle -q -PtaskInputProperty=changed incrementalReverse
    Executing non-incrementally
    ADDED: 1.txt
    ADDED: 2.txt
    ADDED: 3.txt

### [为缓存的任务存储增量状态](#%E4%B8%BA%E7%BC%93%E5%AD%98%E7%9A%84%E4%BB%BB%E5%8A%A1%E5%AD%98%E5%82%A8%E5%A2%9E%E9%87%8F%E7%8A%B6%E6%80%81)

使用Gradle`InputChanges`并不是创建自上次执行以来仅对更改起作用的任务的唯一方法。诸如Kotlin编译器之类的工具将增量性作为内置功能提供。通常的实现方式是该工具将有关先前执行状态的分析数据存储在某个文件中。如果此类状态文件可[重定位](/md/构建缓存.md#声明任务输入和输出)，则可以将其声明为任务的输出。这样，当从缓存加载任务的结果时，下一次执行也可以使用从缓存加载的分析数据。

但是，如果状态文件不可重定位，则无法通过构建缓存共享它们。确实，从高速缓存加载任务时，必须清除所有此类状态文件，以防止过时的状态在下一次执行期间使工具混乱。如果通过[task.localState.register（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/TaskLocalState.html#register-
java.lang.Object...-)声明了旧文件，或者使用[@LocalState](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/LocalState.html)批注标记了属性，Gradle可以确保删除这些旧文件。

## [声明和使用命令行选项](#%E5%A3%B0%E6%98%8E%E5%92%8C%E4%BD%BF%E7%94%A8%E5%91%BD%E4%BB%A4%E8%A1%8C%E9%80%89%E9%A1%B9)

有时，用户希望在命令行而不是构建脚本上声明公开的任务属性的值。如果更频繁地更改属性值，则在命令行上传递它们特别有用。任务API支持一种用于标记属性的机制，以在运行时自动生成具有特定名称的相应命令行参数。

### [声明命令行选项](#%E5%A3%B0%E6%98%8E%E5%91%BD%E4%BB%A4%E8%A1%8C%E9%80%89%E9%A1%B9)

为任务属性公开新的命令行选项非常简单。您只需要使用[Option](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/options/Option.html)注释属性的相应setter方法即可。一个选项需要一个强制标识符。此外，您可以提供可选的描述。任务可以公开与类中可用属性一样多的命令行选项。

让我们看一个示例来说明功能。定制任务`UrlVerify`通过发出HTTP调用并检查响应代码来验证是否可以解析给定的URL。可以通过属性配置要验证的URL
`url`。该属性的setter方法使用[@Option](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/options/Option.html)注释。

### [示例：声明命令行选项](#%E7%A4%BA%E4%BE%8B%EF%BC%9A%E5%A3%B0%E6%98%8E%E5%91%BD%E4%BB%A4%E8%A1%8C%E9%80%89%E9%A1%B9)

UrlVerify.java

    
    
    import org.gradle.api.tasks.options.Option;
    
    public class UrlVerify extends DefaultTask {
        private String url;
    
        @Option(option = "url", description = "Configures the URL to be verified.")
        public void setUrl(String url) {
            this.url = url;
        }
    
        @Input
        public String getUrl() {
            return url;
        }
    
        @TaskAction
        public void verify() {
            getLogger().quiet("Verifying URL '{}'", url);
    
            // verify URL by making a HTTP call
        }
    }

通过运行任务和选项，可以将为任务声明的所有选项[呈现为控制台输出](#%E5%88%97%E5%87%BA%E5%91%BD%E4%BB%A4%E8%A1%8C%E9%80%89%E9%A1%B9)。`help``--task`

### [在命令行上使用选项](#%E5%9C%A8%E5%91%BD%E4%BB%A4%E8%A1%8C%E4%B8%8A%E4%BD%BF%E7%94%A8%E9%80%89%E9%A1%B9)

在命令行上使用选项必须遵守以下规则：

  * 该选项使用双破折号作为前缀，例如`--url`。单个破折号不符合任务选项的有效语法。

  * option参数紧跟在任务声明之后，例如`verifyUrl --url=http://www.google.com/`。

  * 可以在命令行中以任意顺序在任务名称之后声明任务的多个选项。

回到上一个示例，构建脚本创建一个type的任务实例，`UrlVerify`并通过暴露选项从命令行提供一个值。

例子13.使用命令行选项

`Groovy``Kotlin`

build.gradle

    
    
    task verifyUrl(type: UrlVerify)

build.gradle.kts

    
    
    tasks.register<UrlVerify>("verifyUrl")

输出 **`gradle -q verifyUrl --url=http://www.google.com/`**

    
    
    > gradle -q verifyUrl --url=http://www.google.com/
    Verifying URL 'http://www.google.com/'

### [选项支持的数据类型](#%E9%80%89%E9%A1%B9%E6%94%AF%E6%8C%81%E7%9A%84%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)

Gradle限制了可用于声明命令行选项的数据类型集。命令行上的用法因类型而异。

`boolean`，`Boolean`，`Property<Boolean>`

    

描述值为`true`或的选项`false`。在命令行中传递选项会将值视为`true`。例如`--enabled`等于`true`。没有该选项将使用属性的默认值。

`String`， `Property<String>`

    

描述具有任意String值的选项。在命令行上传递选项还需要一个值，例如`--container-id=2x94held`或`--container-id
2x94held`。

`enum`， `Property<enum>`

    

将选项描述为枚举类型。在命令行上传递选项还需要一个值，例如`--log-level=DEBUG`或`--log-level debug`。该值不区分大小写。

`List<String>`， `List<enum>`

    

描述可以采用给定类型的多个值的选项。该选项的值必须作为多个声明提供，例如`--image-id=123 --image-
id=456`。当前不支持其他表示法，例如以逗号分隔的列表或由空格字符分隔的多个值。

### [记录选项的可用值](#%E8%AE%B0%E5%BD%95%E9%80%89%E9%A1%B9%E7%9A%84%E5%8F%AF%E7%94%A8%E5%80%BC)

理论上，属性类型的选项`String`或`List<String>`可以接受任意值。可以在注释[OptionValues](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/options/OptionValues.html)的帮助下以编程方式记录此选项的期望值。可以将此注释分配给任何返回`List`支持的数据类型之一的方法。另外，您必须提供选项标识符以指示选项和可用值之间的关系。

╔═════════════════════════════  

在选项中不支持的命令行上传递值不会使构建失败或引发异常。您必须在任务操作中为此类行为实现自定义逻辑。  
  
╚═════════════════════════════    
  
本示例说明了单个任务使用多个选项的情况。任务实现提供了该选项的可用值列表`output-type`。

### [示例：声明选项的可用值](#%E7%A4%BA%E4%BE%8B%EF%BC%9A%E5%A3%B0%E6%98%8E%E9%80%89%E9%A1%B9%E7%9A%84%E5%8F%AF%E7%94%A8%E5%80%BC)

UrlProcess.java

    
    
    import org.gradle.api.tasks.options.Option;
    import org.gradle.api.tasks.options.OptionValues;
    
    public class UrlProcess extends DefaultTask {
        private String url;
        private OutputType outputType;
    
        @Option(option = "url", description = "Configures the URL to be write to the output.")
        public void setUrl(String url) {
            this.url = url;
        }
    
        @Input
        public String getUrl() {
            return url;
        }
    
        @Option(option = "output-type", description = "Configures the output type.")
        public void setOutputType(OutputType outputType) {
            this.outputType = outputType;
        }
    
        @OptionValues("output-type")
        public List<OutputType> getAvailableOutputTypes() {
            return new ArrayList<OutputType>(Arrays.asList(OutputType.values()));
        }
    
        @Input
        public OutputType getOutputType() {
            return outputType;
        }
    
        @TaskAction
        public void process() {
            getLogger().quiet("Writing out the URL response from '{}' to '{}'", url, outputType);
    
            // retrieve content from URL and write to output
        }
    
        private static enum OutputType {
            CONSOLE, FILE
        }
    }

### [列出命令行选项](#%E5%88%97%E5%87%BA%E5%91%BD%E4%BB%A4%E8%A1%8C%E9%80%89%E9%A1%B9)

使用注释[Option](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/options/Option.html)和[OptionValues的](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/options/OptionValues.html)命令行选项是自记录的。您将在任务的控制台输出中看到[声明的选项](#%E5%A3%B0%E6%98%8E%E5%91%BD%E4%BB%A4%E8%A1%8C%E9%80%89%E9%A1%B9)及其[可用值](#%E8%AE%B0%E5%BD%95%E9%80%89%E9%A1%B9%E7%9A%84%E5%8F%AF%E7%94%A8%E5%80%BC)`help`。输出以字母顺序呈现选项。

### [示例：列出选项的可用值](#%E7%A4%BA%E4%BE%8B%EF%BC%9A%E5%88%97%E5%87%BA%E9%80%89%E9%A1%B9%E7%9A%84%E5%8F%AF%E7%94%A8%E5%80%BC)

输出 **`gradle -q help --task processUrl`**

    
    
    > gradle -q help --task processUrl
    Detailed task information for processUrl
    
    Path
         :processUrl
    
    Type
         UrlProcess (UrlProcess)
    
    Options
         --output-type     Configures the output type.
                           Available values are:
                                CONSOLE
                                FILE
    
         --url     Configures the URL to be write to the output.
    
    Description
         -
    
    Group
         -

### [局限性](#%E5%B1%80%E9%99%90%E6%80%A7)

当前支持声明命令行选项有一些限制。

  * 只能通过注释为自定义任务声明命令行选项。没有用于定义选项的程序化等效项。

  * 选项不能全局声明，例如在项目级别或作为插件的一部分。

  * 在命令行上分配选项时，需要显式地说明公开该选项的任务，例如`gradle check --tests abc`，即使该`check`任务取决于任务，该`test`任务也不起作用。

## [工作者API](#%E5%B7%A5%E4%BD%9C%E8%80%85API)

╔═════════════════════════════  

Worker
API是一个[孵化](/md/功能生命周期.md#feature_lifecycle)功能。  
  
╚═════════════════════════════    
  
从对[增量任务](#%E5%A2%9E%E9%87%8F%E4%BB%BB%E5%8A%A1)的讨论中可以看出，任务执行的工作可以看作是离散的单元（即，将输入的子集转换为特定的输出子集）。很多时候，这些工作单元彼此高度独立，这意味着它们可以以任何顺序执行，并且可以简单地汇总在一起以形成任务的整体动作。在单线程执行中，这些工作单元将按顺序执行，但是，如果我们有多个处理器，则希望同时执行独立的工作单元。这样，我们可以在构建时充分利用可用资源，并更快地完成任务的活动。

Worker API提供了一种完成此操作的机制。它允许在任务动作期间安全，并行地执行多项工作。但是，Worker
API的好处不仅限于并行执行任务。您还可以配置所需的隔离级别，以便可以在隔离的类加载器甚至隔离的进程中执行工作。此外，好处甚至超出了执行单个任务的范围。默认情况下，使用Worker
API，Gradle可以开始并行执行任务。换句话说，一旦任务提交了要异步执行的工作并退出了任务动作，Gradle便可以并行开始执行其他独立任务，即使这些任务在同一项目中也是如此。

### [使用Worker API](#%E4%BD%BF%E7%94%A8Worker+API)

为了将工作提交给Worker API，必须提供两件事：工作单元的实现以及工作单元的参数。

工作单元的参数定义为实现[WorkParameters](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkParameters.html)的接口或抽象类。参数类型必须是[托管类型](/md/开发自定义Gradle类型.md#托管类型)。

您可以在[开发自定义Gradle类型中](/md/开发自定义Gradle类型.md#custom_gradle_types)找到有关实现工作参数的更多信息。

该实现是扩展[WorkAction](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkAction.html)的类。此类应该是抽象的，并且不应实现该`getParameters()`方法。Gradle将在运行时为每个工作单元的参数对象注入此方法的实现。

例子14.定义工作单位参数和实现

`Groovy``Kotlin`

build.gradle

    
    
    // The parameters for a single unit of work
    interface ReverseParameters extends WorkParameters {
        RegularFileProperty getFileToReverse()
        DirectoryProperty getDestinationDir()
    }
    
    // The implementation of a single unit of work.
    abstract class ReverseFile implements WorkAction<ReverseParameters> {
        private final FileSystemOperations fileSystemOperations
    
        @Inject
        public ReverseFile(FileSystemOperations fileSystemOperations) {
            this.fileSystemOperations = fileSystemOperations
        }
    
        @Override
        void execute() {
            fileSystemOperations.copy {
                from parameters.fileToReverse
                into parameters.destinationDir
                filter { String line -> line.reverse() }
            }
        }
    }

build.gradle.kts

    
    
    import javax.inject.Inject
    
    // The parameters for a single unit of work
    interface ReverseParameters : WorkParameters {
        val fileToReverse : RegularFileProperty
        val destinationDir : DirectoryProperty
    }
    
    // The implementation of a single unit of work
    abstract class ReverseFile @Inject constructor(val fileSystemOperations: FileSystemOperations) : WorkAction<ReverseParameters> {
        override fun execute() {
            fileSystemOperations.copy {
                from(parameters.fileToReverse)
                into(parameters.destinationDir)
                filter { line: String -> line.reversed() }
            }
        }
    }

一个`WorkAction`实现可以注入提供工作执行过程中的功能，如服务[FileSystemOperations](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/FileSystemOperations.html)在上面的示例服务。有关注入服务类型的更多信息，请参见[服务注入](/md/开发自定义Gradle类型.md#服务注入)。

为了提交工作单元，必须首先获得[WorkerExecutor](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutor.html)。要做到这一点，一个任务应该有注释构造与`javax.inject.Inject`接受一个[WorkerExecutor](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutor.html)参数。创建任务时，Gradle将在运行时注入[WorkerExecutor](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutor.html)的实例。然后可以创建一个[WorkQueue](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkQueue.html)对象，并可以提交各个工作项。

例子15.提交工作单元以执行

`Groovy``Kotlin`

build.gradle

    
    
    class ReverseFiles extends SourceTask {
        private final WorkerExecutor workerExecutor
    
        @OutputDirectory
        File outputDir
    
        // The WorkerExecutor will be injected by Gradle at runtime
        @Inject
        ReverseFiles(WorkerExecutor workerExecutor) {
            this.workerExecutor = workerExecutor
        }
    
        @TaskAction
        void reverseFiles() {
            // Create a WorkQueue to submit work items
            WorkQueue workQueue = workerExecutor.noIsolation()
    
            // Create and submit a unit of work for each file
            source.each { file ->
                workQueue.submit(ReverseFile.class) { ReverseParameters parameters ->
                    parameters.fileToReverse = file
                    parameters.destinationDir = outputDir
                }
            }
        }
    }

build.gradle.kts

    
    
    // The WorkerExecutor will be injected by Gradle at runtime
    open class ReverseFiles @Inject constructor(private val workerExecutor: WorkerExecutor) : SourceTask() {
        @OutputDirectory
        lateinit var outputDir: File
    
        @TaskAction
        fun reverseFiles() {
            // Create a WorkQueue to submit work items
            val workQueue = workerExecutor.noIsolation()
    
            // Create and submit a unit of work for each file
            source.forEach { file ->
                workQueue.submit(ReverseFile::class) {
                    fileToReverse.set(file)
                    destinationDir.set(outputDir)
                }
            }
        }
    }

提交任务动作的所有工作后，可以安全地退出任务动作。该工作将异步并行执行（直到的设置`max-
workers`）。当然，在完成所有异步工作之前，任何依赖于此任务的任务（以及该任务的任何后续任务动作）都不会开始执行。但是，与此任务无关的其他独立任务可以立即开始执行。

如果在执行异步工作时发生任何故障，该任务将失败，并且将抛出[WorkerExecutionException，](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutionException.html)详细说明每个失败的工作项的故障。这将被视为任务执行期间的任何失败，并且将阻止执行任何从属任务。

但是，在某些情况下，可能希望在退出任务操作之前等待工作完成。使用[WorkQueue.await（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkQueue.html#await--)方法可以实现。如在使工件异步完成的情况下，在执行工作的项目中发生的任何故障将被浮出水面作为[WorkerExecutionException](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutionException.html)从抛出[WorkQueue.await（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkQueue.html#await--)方法。

╔═════════════════════════════  

请注意，当任务退出任务动作并将执行控制权返回给Gradle时，Gradle将仅开始并行运行其他独立任务。使用[WorkQueue.await（）时](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkQueue.html#await--)，执行不会离开任务动作。这意味着Gradle将不允许其他任务开始执行，并且将等待任务动作完成后再执行。  
  
╚═════════════════════════════    
  
例子16.等待异步工作完成

`Groovy``Kotlin`

build.gradle

    
    
            // Create a WorkQueue to submit work items
            WorkQueue workQueue = workerExecutor.noIsolation()
    
            // Create and submit a unit of work for each file
            source.each { file ->
                workQueue.submit(ReverseFile.class) { ReverseParameters parameters ->
                    parameters.fileToReverse = file
                    parameters.destinationDir = outputDir
                }
            }
    
            // Wait for all asynchronous work submitted to this queue to complete before continuing
            workQueue.await()
            logger.lifecycle("Created ${outputDir.listFiles().size()} reversed files in ${projectLayout.projectDirectory.asFile.relativePath(outputDir)}")

build.gradle.kts

    
    
            // Create a WorkQueue to submit work items
            val workQueue = workerExecutor.noIsolation()
    
            // Create and submit a unit of work for each file
            source.forEach { file ->
                workQueue.submit(ReverseFile::class) {
                    fileToReverse.set(file)
                    destinationDir.set(outputDir)
                }
            }
    
            // Wait for all asynchronous work submitted to this queue to complete before continuing
            workQueue.await()
            logger.lifecycle("Created ${outputDir.listFiles().size} reversed files in ${outputDir.toRelativeString(projectLayout.projectDirectory.asFile)}")

### [隔离模式](#%E9%9A%94%E7%A6%BB%E6%A8%A1%E5%BC%8F)

Gradle提供了可以在创建时被配置三种隔离模式[工作队列](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkQueue.html)上使用下列方法中的一个被指定和[WorkerExecutor](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutor.html)：

[WorkerExecutor.noIsolation（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutor.html#noIsolation--)

    

这表明工作应在具有最小隔离度的线程中运行。例如，它将共享从中加载任务的同一类加载器。这是最快的隔离级别。

[WorkerExecutor.classLoaderIsolation（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutor.html#classLoaderIsolation-org.gradle.api.Action-)

    

这表明工作应在具有隔离类加载器的线程中运行。类加载器将具有来自加载了工作单元实现类的类加载器的类路径，以及通过[ClassLoaderWorkerSpec.getClasspath（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/ClassLoaderWorkerSpec.html#getClasspath--)添加的任何其他类路径条目。

[WorkerExecutor.processIsolation（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutor.html#processIsolation-org.gradle.api.Action-)

    

这表明应通过在单独的进程中执行工作来最大程度地隔离工作。流程的类加载器将使用加载了工作单元的类加载器中的类路径，以及通过[ClassLoaderWorkerSpec.getClasspath（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/ClassLoaderWorkerSpec.html#getClasspath--)添加的任何其他类路径条目。此外，该过程将是一个
_工作守护程序_ ，该 _守护程序_
将保持活动状态，并可以重复用于将来可能具有相同要求的工作项。可以使用[ProcessWorkerSpec.forkOptions（org.gradle.api.Action）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/ProcessWorkerSpec.html#forkOptions-org.gradle.api.Action-)使用与Gradle JVM不同的设置来配置此过程。

### [工人守护进程](#%E5%B7%A5%E4%BA%BA%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8B)

使用时`processIsolation()`，gradle将启动一个长期存在的 _Worker Daemon_ 进程，该进程可用于将来的工作项。

例子17.提交要在worker守护进程中运行的工作项

`Groovy``Kotlin`

build.gradle

    
    
            // Create a WorkQueue with process isolation
            WorkQueue workQueue = workerExecutor.processIsolation() { ProcessWorkerSpec spec ->
                // Configure the options for the forked process
                forkOptions { JavaForkOptions options ->
                    options.maxHeapSize = "512m"
                    options.systemProperty "org.gradle.sample.showFileSize", "true"
                }
            }
    
            // Create and submit a unit of work for each file
            source.each { file ->
                workQueue.submit(ReverseFile.class) { ReverseParameters parameters ->
                    parameters.fileToReverse = file
                    parameters.destinationDir = outputDir
                }
            }

build.gradle.kts

    
    
            // Create a WorkQueue with process isolation
            val workQueue = workerExecutor.processIsolation() {
                // Configure the options for the forked process
                forkOptions {
                    maxHeapSize = "512m"
                    systemProperty("org.gradle.sample.showFileSize", "true")
                }
            }
    
            // Create and submit a unit of work for each file
            source.forEach { file ->
                workQueue.submit(ReverseFile::class) {
                    fileToReverse.set(file)
                    destinationDir.set(outputDir)
                }
            }

提交工作程序守护程序的工作单元时，Gradle首先会查看是否存在兼容的空闲守护程序。如果是这样，它将把工作单元发送到空闲的守护程序，将其标记为忙。如果没有，它将启动一个新的守护程序。在评估兼容性时，Gradle会考虑许多标准，所有这些标准都可以通过[ProcessWorkerSpec.forkOptions（org.gradle.api.Action）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/ProcessWorkerSpec.html#forkOptions-org.gradle.api.Action-)进行控制。

默认情况下，辅助守护程序的最大堆启动为512MB。可以通过调整worker fork选项来更改。

可执行文件

    

守护程序仅在使用相同的Java可执行文件时才被认为是兼容的。

类路径

    

如果守护程序的类路径包含所有请求的类路径条目，则该守护程序被认为是兼容的。请注意，只有当类路径与请求的类路径完全匹配时，守护程序才被认为是兼容的。

堆设置

    

如果守护程序至少具有与请求相同的堆大小设置，则认为该守护程序是兼容的。换句话说，具有高于请求的堆设置的守护程序将被视为兼容。

jvm参数

    

如果守护程序已设置所有请求的jvm参数，则该守护程序被认为是兼容的。请注意，如果守护程序除了请求的参数之外还具有其他jvm参数，则该守护程序被认为是兼容的（除了专门处理的参数，例如堆设置，断言，调试等）。

系统属性

    

如果守护程序已将请求的所有系统属性设置为相同的值，则该守护程序被认为是兼容的。请注意，如果守护程序具有除请求的属性之外的其他系统属性，则它被视为兼容。

环境变量

    

如果守护程序已将所有请求的环境变量设置为相同的值，则认为该守护程序是兼容的。请注意，如果守护程序除请求的环境变量之外还具有更多环境变量，则该守护程序被认为是兼容的。

引导类路径

    

如果守护程序包含所有请求的引导类路径条目，则该守护程序被认为是兼容的。请注意，如果守护程序除请求的引导程序类路径条目之外还有更多的引导程序类路径条目，则该守护程序被视为兼容。

调试

    

仅当将debug设置为与请求相同的值（真或假）时，守护程序才被视为兼容。

启用断言

    

仅当启用断言设置为与请求相同的值（true或false）时，守护程序才被视为兼容。

默认字符编码

    

仅当默认字符编码设置为与请求相同的值时，守护程序才被视为兼容。

辅助守护程序将一直运行，直到启动它们的构建守护程序停止或系统内存不足为止。当可用系统内存不足时，Gradle将开始停止工作程序守护程序，以尽量减少内存消耗。

## [取消和超时](#%E5%8F%96%E6%B6%88%E5%92%8C%E8%B6%85%E6%97%B6)

为了支持取消操作（例如，当用户使用CTRL +
C停止构建时）和任务超时，自定义任务应对被中断的执行线程做出反应。通过工作人员API提交的工作项也是如此。如果任务在10秒内未响应中断，则守护程序将关闭以释放系统资源。

## [更多细节](#%E6%9B%B4%E5%A4%9A%E7%BB%86%E8%8A%82)

在自定义Gradle插件中打包自定义任务类型通常是一种好方法。该插件可以为任务类型提供有用的默认值和约定，并提供一种从构建脚本或其他插件使用任务类型的便捷方法。请参阅[开发自定义Gradle插件](/md/开发自定义Gradle插件.md#custom_plugins)以获取更多详细信息。

Gradle提供了许多功能，这些功能在开发Gradle类型（包括任务）时很有用。有关更多详细信息，请参见[开发自定义Gradle类型](/md/开发自定义Gradle类型.md)。

