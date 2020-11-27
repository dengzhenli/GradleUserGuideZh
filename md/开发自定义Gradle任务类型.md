

[ Gradle ](https://docs.gradle.org "Gradle Docs")

User Manual

  * Community Open Community Menu

[ Community Home ](https://gradle.org/)

[ Community Forums ](https://discuss.gradle.org/)

[ Community Plugins ](https://plugins.gradle.org)

  * [Training](https://gradle.com/training/)
  * News Open Community Menu

[ Newsletter ](https://newsletter.gradle.com)

[ Blog ](https://blog.gradle.org)

[ Twitter ](https://twitter.com/gradle)

  * [Enterprise](https://gradle.com)
  * [github](https://github.com/gradle/gradle "Gradle on GitHub")

  * [Docs Home](../userguide/userguide.html)
  * [Samples](../samples/index.html)
  * [Release Notes](../release-notes.html)
  * Gradle API
    * [Javadoc](../javadoc/index.html?overview-summary.html)
    * [Groovy DSL Reference](../dsl/index.html)
    * [Groovy DSL Primer](../userguide/groovy_build_script_primer.html)
    * [Kotlin DSL API](https://gradle.github.io/kotlin-dsl-docs/api/)
    * [Kotlin DSL Primer](../userguide/kotlin_dsl.html)

### User Manual

[ PDF ](../userguide/userguide.pdf)

  * [What is Gradle?](../userguide/what_is_gradle.html)
  * [Getting Started](../userguide/getting_started.html)
  * [Installing Gradle](../userguide/installation.html)
  * Upgrading Gradle...
    * [version 6.X to latest](../userguide/upgrading_version_6.html)
    * [version 5.X to 6.0](../userguide/upgrading_version_5.html)
    * [version 4.X to 5.0](../userguide/upgrading_version_4.html)
    * [Gradle's Feature Lifecycle](../userguide/feature_lifecycle.html)
  * Migrating to Gradle...
    * [from Maven](../userguide/migrating_from_maven.html)
    * [from Ant](../userguide/migrating_from_ant.html)
  * [Compatibility Notes](../userguide/compatibility.html)
  * [Troubleshooting Builds](../userguide/troubleshooting.html)

### Running Gradle Builds

  * Customizing Execution
    * [Configuring the Build Environment](../userguide/build_environment.html)
    * [Configuring the Gradle Daemon](../userguide/gradle_daemon.html)
    * [Using Initialization Scripts](../userguide/init_scripts.html)
  * [Executing Multi-Project Builds](../userguide/intro_multi_project_builds.html)
  * [Inspecting Gradle Builds](https://scans.gradle.com/)
  * Optimizing Build Times
    * [Build Performance Guide](https://guides.gradle.org/performance/)
    * [Enabling and Configuring the Build Cache](../userguide/build_cache.html)
    * [Using the Configuration Cache](../userguide/configuration_cache.html)

### Authoring Gradle Builds

  * Learning the Basics
    * [Introducing the Basics of Build Scripts](../userguide/tutorial_using_tasks.html)
    * [Working with Tasks](../userguide/more_about_tasks.html)
    * [Learning More About Build Scripts](../userguide/writing_build_scripts.html)
    * [Working with Files](../userguide/working_with_files.html)
    * [Using Gradle Plugins](../userguide/plugins.html)
    * [Understanding the Build Lifecycle](../userguide/build_lifecycle.html)
    * [Working with Logging](../userguide/logging.html)
    * [Avoiding Traps](../userguide/potential_traps.html)
  * Authoring Multi-Project Builds
    * [Creating a Basic Multi-Project Build](../userguide/multi_project_builds.html)
    * [Declaring Dependencies between Subprojects](../userguide/declaring_dependencies_between_subprojects.html)
    * [Sharing Build Logic between Subprojects](../userguide/sharing_build_logic_between_subprojects.html)
    * [Fine Tuning the Project Layout](../userguide/fine_tuning_project_layout.html)
    * [Understanding Configuration and Execution](../userguide/multi_project_configuration_and_execution.html)
  * Authoring Sustainable Builds
    * [Organizing Build Logic](../userguide/organizing_gradle_projects.html)
    * [Following Best Practices](../userguide/authoring_maintainable_build_scripts.html)
  * Advanced Techniques
    * [Developing Parallel Tasks](https://guides.gradle.org/using-the-worker-api/)
    * [Testing a Build with TestKit](../userguide/test_kit.html)
    * [Using Ant from Gradle](../userguide/ant.html)

### Authoring JVM Builds

  * [Building Java & JVM projects](../userguide/building_java_projects.html)
  * [Testing Java & JVM projects](../userguide/java_testing.html)
  * [Toolchains for Java projects](../userguide/toolchains.html)
  * [Managing Dependencies](../userguide/dependency_management_for_java_projects.html)
  * JVM Plugins
    * [Java Library Plugin](../userguide/java_library_plugin.html)
    * [Java Application Plugin](../userguide/application_plugin.html)
    * [Java Platform Plugin](../userguide/java_platform_plugin.html)
    * [Groovy Plugin](../userguide/groovy_plugin.html)
    * [Scala Plugin](../userguide/scala_plugin.html)

### Authoring C++/Swift Builds

  * [Building C++ projects](../userguide/building_cpp_projects.html)
  * [Testing C++ projects](../userguide/cpp_testing.html)
  * [Building Swift projects](../userguide/building_swift_projects.html)
  * [Testing Swift projects](../userguide/swift_testing.html)

### Working with Dependencies

  * Learning the Basics
    * [What is Dependency Management?](../userguide/core_dependency_management.html)
    * [Declaring Repositories](../userguide/declaring_repositories.html)
    * [Declaring Dependencies](../userguide/declaring_dependencies.html)
    * [Understanding Library and Application Differences](../userguide/library_vs_application.html)
    * [Viewing and Debugging Dependencies](../userguide/viewing_debugging_dependencies.html)
    * [Understanding Resolution](../userguide/dependency_resolution.html)
    * [Verifying dependencies](../userguide/dependency_verification.html)
  * Declaring Versions
    * [Declaring Versions and Ranges](../userguide/single_versions.html)
    * [Declaring Rich Versions](../userguide/rich_versions.html)
    * [Handling Changing Versions](../userguide/dynamic_versions.html)
    * [Locking Versions](../userguide/dependency_locking.html)
  * Controlling Transitives
    * [Upgrading Versions](../userguide/dependency_constraints.html)
    * [Downgrading and Excluding](../userguide/dependency_downgrade_and_exclude.html)
    * [Sharing Versions](../userguide/platforms.html)
    * [Aligning Dependencies](../userguide/dependency_version_alignment.html)
    * [Handling Mutually Exclusive Dependencies](../userguide/dependency_capability_conflict.html)
    * [Fixing Metadata](../userguide/component_metadata_rules.html)
    * [Customizing Resolution](../userguide/resolution_rules.html)
    * [Preventing accidental upgrades](../userguide/resolution_strategy_tuning.html)
  * Producing and Consuming Variants of Libraries
    * [Declaring Capabilities of a Library](../userguide/component_capabilities.html)
    * [Modeling Feature Variants and Optional Dependencies](../userguide/feature_variants.html)
    * [Understanding Variant Selection](../userguide/variant_model.html)
    * [Declaring Variant Attributes](../userguide/variant_attributes.html)
    * [Sharing Outputs of Projects](../userguide/cross_project_publications.html)
    * [Transforming Artifacts](../userguide/artifact_transforms.html)
  * Working in a Multi-repo Environment
    * [Composing Builds](../userguide/composite_builds.html)
  * Publishing Libraries
    * [Setting up Publishing](../userguide/publishing_setup.html)
    * [Understanding Gradle Module Metadata](../userguide/publishing_gradle_module_metadata.html)
    * [Signing Artifacts](../userguide/publishing_signing.html)
    * [Customizing Publishing](../userguide/publishing_customization.html)
    * [Maven Publish Plugin](../userguide/publishing_maven.html)
    * [Ivy Publish Plugin](../userguide/publishing_ivy.html)
  * [Terminology](../userguide/dependency_management_terminology.html)

### Extending Gradle

  * [Plugin Development Tutorials](https://gradle.org/guides/?q=Plugin%20Development)
  * [Writing Gradle Task Types](../userguide/custom_tasks.html)
  * [Writing Gradle Plugins](../userguide/custom_plugins.html)
  * [Writing Custom Gradle Types](../userguide/custom_gradle_types.html)
  * [Configuring Tasks Lazily](../userguide/lazy_configuration.html)
  * [Using Task Configuration Avoidance](../userguide/task_configuration_avoidance.html)

### Reference

  * [Core Plugins](../userguide/plugin_reference.html)
  * [Command-Line Interface](../userguide/command_line_interface.html)
  * [Gradle & Third-party Tools](../userguide/third_party_integration.html)
  * [The Gradle Wrapper](../userguide/gradle_wrapper.html)
  * [Gradle-managed Directories](../userguide/directory_layout.html)

# Developing Custom Gradle Task Types

version 6.7.1

Contents

  * Packaging a task class
  * Writing a simple task class
  * A standalone project
  * Incremental tasks
  * Declaring and Using Command Line Options
  * The Worker API
  * Cancellation and timeouts
  * More details

Gradle supports two types of task. One such type is the simple task, where you
define the task with an action closure. We have seen these in [Build Script
Basics](tutorial_using_tasks.html#tutorial_using_tasks). For this type of
task, the action closure determines the behaviour of the task. This type of
task is good for implementing one-off tasks in your build script.

The other type of task is the enhanced task, where the behaviour is built into
the task, and the task provides some properties which you can use to configure
the behaviour. We have seen these in [Authoring
Tasks](more_about_tasks.html#more_about_tasks). Most Gradle plugins use
enhanced tasks. With enhanced tasks, you don't need to implement the task
behaviour as you do with simple tasks. You simply declare the task and
configure the task using its properties. In this way, enhanced tasks let you
reuse a piece of behaviour in many different places, possibly across different
builds.

The behaviour and properties of an enhanced task are defined by the task's
class. When you declare an enhanced task, you specify the type, or class of
the task.

Implementing your own custom task class in Gradle is easy. You can implement a
custom task class in pretty much any language you like, provided it ends up
compiled to JVM bytecode. In our examples, we are going to use Groovy as the
implementation language. Groovy, Java or Kotlin are all good choices as the
language to use to implement a task class, as the Gradle API has been designed
to work well with these languages. In general, a task implemented using Java
or Kotlin, which are statically typed, will perform better than the same task
implemented using Groovy.

## Packaging a task class

There are several places where you can put the source for the task class.

Build script

    

You can include the task class directly in the build script. This has the
benefit that the task class is automatically compiled and included in the
classpath of the build script without you having to do anything. However, the
task class is not visible outside the build script, and so you cannot reuse
the task class outside the build script it is defined in.

`buildSrc` project

    

You can put the source for the task class in the `_rootProjectDir_
/buildSrc/src/main/groovy` directory (or `_rootProjectDir_
/buildSrc/src/main/java` or `_rootProjectDir_ /buildSrc/src/main/kotlin`
depending on which language you prefer). Gradle will take care of compiling
and testing the task class and making it available on the classpath of the
build script. The task class is visible to every build script used by the
build. However, it is not visible outside the build, and so you cannot reuse
the task class outside the build it is defined in. Using the `buildSrc`
project approach separates the task declaration — that is, what the task
should do — from the task implementation — that is, how the task does it.

See [Organizing Gradle
Projects](organizing_gradle_projects.html#organizing_gradle_projects) for more
details about the `buildSrc` project.

Standalone project

    

You can create a separate project for your task class. This project produces
and publishes a JAR which you can then use in multiple builds and share with
others. Generally, this JAR might include some custom plugins, or bundle
several related task classes into a single library. Or some combination of the
two.

In our examples, we will start with the task class in the build script, to
keep things simple. Then we will look at creating a standalone project.

## Writing a simple task class

To implement a custom task class, you extend
[DefaultTask](../dsl/org.gradle.api.DefaultTask.html).

Example 1. Defining a custom task

build.gradle

    
    
    class GreetingTask extends DefaultTask {
    }

build.gradle.kts

    
    
    open class GreetingTask : DefaultTask() {
    }

This task doesn't do anything useful, so let's add some behaviour. To do so,
we add a method to the task and mark it with the
[TaskAction](../javadoc/org/gradle/api/tasks/TaskAction.html) annotation.
Gradle will call the method when the task executes. You don't have to use a
method to define the behaviour for the task. You could, for instance, call
`doFirst()` or `doLast()` with a closure in the task constructor to add
behaviour.

Example 2. A hello world task

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

Output of **`gradle -q hello`**

    
    
    > gradle -q hello
    hello from GreetingTask

Let's add a property to the task, so we can customize it. Tasks are simply
POGOs, and when you declare a task, you can set the properties or call methods
on the task object. Here we add a `greeting` property, and set the value when
we declare the `greeting` task.

Example 3. A customizable hello world task

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

Output of **`gradle -q hello greeting`**

    
    
    > gradle -q hello greeting
    hello from GreetingTask
    greetings from GreetingTask

## A standalone project

Now we will move our task to a standalone project, so we can publish it and
share it with others. This project is simply a Groovy project that produces a
JAR containing the task class. Here is a simple build script for the project.
It applies the Groovy plugin, and adds the Gradle API as a compile-time
dependency.

Example 4. A build for a custom task

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

We just follow the convention for where the source for the task class should
go.

### Example: A custom task

src/main/groovy/org/gradle/GreetingTask.groovy

    
    
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

### Using your task class in another project

To use a task class in a build script, you need to add the class to the build
script's classpath. To do this, you use a `buildscript { }` block, as
described in [External dependencies for the build
script](tutorial_using_tasks.html#sec:build_script_external_dependencies). The
following example shows how you might do this when the JAR containing the task
class has been published to a local repository:

Example 5. Using a custom task in another project

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

### Writing tests for your task class

You can use the
[ProjectBuilder](../javadoc/org/gradle/testfixtures/ProjectBuilder.html) class
to create [Project](../dsl/org.gradle.api.Project.html) instances to use when
you test your task class.

### Example: Testing a custom task

src/test/groovy/org/gradle/GreetingTaskTest.groovy

    
    
    class GreetingTaskTest {
        @Test
        void canAddTaskToProject() {
            Project project = ProjectBuilder.builder().build()
            def task = project.task('greeting', type: GreetingTask)
            assertTrue(task instanceof GreetingTask)
        }
    }

## Incremental tasks

With Gradle, it's very simple to implement a task that is skipped when all of
its inputs and outputs are up to date (see [Incremental
Builds](more_about_tasks.html#sec:up_to_date_checks)). However, there are
times when only a few input files have changed since the last execution, and
you'd like to avoid reprocessing all of the unchanged inputs. This can be
particularly useful for a transformer task that converts input files to output
files on a 1:1 basis.

If you'd like to optimize your build so that only out-of-date input files are
processed, you can do so with an _incremental task_.

__ |

There is the
[IncrementalTaskInputs](../javadoc/org/gradle/api/tasks/incremental/IncrementalTaskInputs.html)
API, which is available in Gradle versions before 5.4. When using
[IncrementalTaskInputs](../javadoc/org/gradle/api/tasks/incremental/IncrementalTaskInputs.html),
it is only possible to query for all file changes of the task inputs. It is
not possible to query for changes of individual input file properties.
Moreover, the old API does not distinguish between incremental and non-
incremental task inputs, so the task itself needs to determine where the
changes originated from. Therefore, the usage of this API is deprecated, and
it will be removed eventually. The new
[InputChanges](../dsl/org.gradle.work.InputChanges.html) API, which is
documented here, replaces the old API and addresses its shortcomings. If you
need to use the old API, have a look at the documentation in the [user manual
for Gradle
5.3.1](https://docs.gradle.org/5.3.1/userguide/custom_tasks.html#incremental_tasks).  
  
---|---  
  
### Implementing an incremental task

For a task to process inputs incrementally, that task must contain an
_incremental task action_. This is a task action method that has a single
[InputChanges](../dsl/org.gradle.work.InputChanges.html) parameter. That
parameter tells Gradle that the action only wants to process the changed
inputs. In addition, the task needs to declare at least one incremental file
input property by using either
[@Incremental](../javadoc/org/gradle/work/Incremental.html) or
[@SkipWhenEmpty](../javadoc/org/gradle/api/tasks/SkipWhenEmpty.html).

__ |

To query incremental changes for an input file property, that property always
needs to return the same instance. The easiest way to accomplish this is to
use one of the following types for such properties:
[RegularFileProperty](../javadoc/org/gradle/api/file/RegularFileProperty.html),
[DirectoryProperty](../javadoc/org/gradle/api/file/DirectoryProperty.html) or
[ConfigurableFileCollection](../javadoc/org/gradle/api/file/ConfigurableFileCollection.html).

You can learn more about `RegularFileProperty` and `DirectoryProperty` in the
[Lazy Configuration](lazy_configuration.html#lazy_configuration) chapter,
especially the sections on [using read-only and configurable
properties](lazy_configuration.html#lazy_properties) and [lazy file
properties](lazy_configuration.html#working_with_files_in_lazy_properties).  
  
---|---  
  
The incremental task action can use
[InputChanges.getFileChanges()](../dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))
to find out what files have changed for a given file-based input property, be
it of type `RegularFileProperty`, `DirectoryProperty` or
`ConfigurableFileCollection`. The method returns an `Iterable` of type
[FileChanges](../javadoc/org/gradle/work/FileChange.html), which in turn can
be queried for the following:

  * the [affected file](../javadoc/org/gradle/work/FileChange.html#getFile--)

  * the [change type](../javadoc/org/gradle/work/FileChange.html#getChangeType--) (`ADDED`, `REMOVED` or `MODIFIED`)

  * the [normalized path](../javadoc/org/gradle/work/FileChange.html#getNormalizedPath--) of the changed file

  * the [file type](../javadoc/org/gradle/work/FileChange.html#getFileType--) of the changed file

The following example demonstrates an incremental task that has a directory
input. It assumes that the directory contains a collection of text files and
copies them to an output directory, reversing the text within each file. The
key things to note are the type of the `inputDir` property, its annotations,
and how the action (`execute()`) uses `getFileChanges()` to process the subset
of files that have actually changed since the last build. You can also see how
the action deletes a target file if the corresponding input file has been
removed:

Example 6. Defining an incremental task action

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

If for some reason the task is executed non-incrementally, for example by
running with `--rerun-tasks`, all files are reported as `ADDED`, irrespective
of the previous state. In this case, Gradle automatically removes the previous
outputs, so the incremental task only needs to process the given files.

For a simple transformer task like the above example, the task action simply
needs to generate output files for any out-of-date inputs and delete output
files for any removed inputs.

__ |

A task may only contain a single incremental task action.  
  
---|---  
  
### Which inputs are considered out of date?

When there is a previous execution of the task, and the only changes since
that execution are to incremental input file properties, then Gradle is able
to determine which input files need to be processed (incremental execution).
In this case, the
[InputChanges.getFileChanges()](../dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))
method returns details for all input files for the given property that were
_added_ , _modified_ or _removed_.

However, there are many cases where Gradle is unable to determine which input
files need to be processed (non-incremental execution). Examples include:

  * There is no history available from a previous execution.

  * You are building with a different version of Gradle. Currently, Gradle does not use task history from a different version.

  * An [`upToDateWhen`](../javadoc/org/gradle/api/tasks/TaskOutputs.html#upToDateWhen-groovy.lang.Closure-) criterion added to the task returns `false`.

  * An input property has changed since the previous execution.

  * A non-incremental input file property has changed since the previous execution.

  * One or more output files have changed since the previous execution.

In all of these cases, Gradle will report all input files as `ADDED` and the
`getFileChanges()` method will return details for all the files that comprise
the given input property.

You can check if the task execution is incremental or not with the
[InputChanges.isIncremental()](../dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges.html##org.gradle.work.InputChanges:incremental)
method.

### An incremental task in action

Given the example incremental task implementation above, let's walk through
some scenarios based on it.

First, consider an instance of `IncrementalReverseTask` that is executed
against a set of inputs for the first time. In this case, all inputs will be
considered added, as shown here:

Example 7. Running the incremental task for the first time

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

Build layout

    
    
    .
    ├── build.gradle
    └── inputs
        ├── 1.txt
        ├── 2.txt
        └── 3.txt

Output of `gradle -q incrementalReverse`

    
    
    > gradle -q incrementalReverse
    Executing non-incrementally
    ADDED: 1.txt
    ADDED: 2.txt
    ADDED: 3.txt

Naturally when the task is executed again with no changes, then the entire
task is up to date and the task action is not executed:

Example 8. Running the incremental task with unchanged inputs

Output of `gradle incrementalReverse`

    
    
    > gradle incrementalReverse
    > Task :incrementalReverse UP-TO-DATE
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 up-to-date

When an input file is modified in some way or a new input file is added, then
re-executing the task results in those files being returned by
[InputChanges.getFileChanges()](../dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\)).
The following example modifies the content of one file and adds another before
running the incremental task:

Example 9. Running the incremental task with updated input files

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

Output of `gradle -q updateInputs incrementalReverse`

    
    
    > gradle -q updateInputs incrementalReverse
    Executing incrementally
    MODIFIED: 1.txt
    ADDED: 4.txt

__ |  The various mutation tasks (`updateInputs`, `removeInput`, etc) are only
present to demonstrate the behavior of incremental tasks. They should not be
viewed as the kinds of tasks or task implementations you should have in your
own build scripts.  
---|---  
  
When an existing input file is removed, then re-executing the task results in
that file being returned by
[InputChanges.getFileChanges()](../dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))
as `REMOVED`. The following example removes one of the existing files before
executing the incremental task:

Example 10. Running the incremental task with an input file removed

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

Output of `gradle -q removeInput incrementalReverse`

    
    
    > gradle -q removeInput incrementalReverse
    Executing incrementally
    REMOVED: 3.txt

When an _output_ file is deleted (or modified), then Gradle is unable to
determine which input files are out of date. In this case, details for _all_
the input files for the given property are returned by
[InputChanges.getFileChanges()](../dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\)).
The following example removes just one of the output files from the build
directory, but notice how all the input files are considered to be `ADDED`:

Example 11. Running the incremental task with an output file removed

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

Output of `gradle -q removeOutput incrementalReverse`

    
    
    > gradle -q removeOutput incrementalReverse
    Executing non-incrementally
    ADDED: 1.txt
    ADDED: 2.txt
    ADDED: 3.txt

The last scenario we want to cover concerns what happens when a non-file-based
input property is modified. In such cases, Gradle is unable to determine how
the property impacts the task outputs, so the task is executed non-
incrementally. This means that _all_ input files for the given property are
returned by
[InputChanges.getFileChanges()](../dsl/org.gradle.work.InputChanges.html#org.gradle.work.InputChanges:getFileChanges\(org.gradle.api.file.FileCollection\))
and they are all treated as `ADDED`. The following example sets the project
property `taskInputProperty` to a new value when running the
`incrementalReverse` task and that project property is used to initialize the
task's `inputProperty` property, as you can see in the first example of this
section. Here's the output you can expect in this case:

Example 12. Running the incremental task with an input property changed

Output of `gradle -q -PtaskInputProperty=changed incrementalReverse`

    
    
    > gradle -q -PtaskInputProperty=changed incrementalReverse
    Executing non-incrementally
    ADDED: 1.txt
    ADDED: 2.txt
    ADDED: 3.txt

### Storing incremental state for cached tasks

Using Gradle's `InputChanges` is not the only way to create tasks that only
work on changes since the last execution. Tools like the Kotlin compiler
provide incrementality as a built-in feature. The way this is typically
implemented is that the tool stores some analysis data about the state of the
previous execution in some file. If such state files are
[relocatable](build_cache.html#sec:task_output_caching_inputs), then they can
be declared as outputs of the task. This way when the task's results are
loaded from cache, the next execution can already use the analysis data loaded
from cache, too.

However, if the state files are non-relocatable, then they can't be shared via
the build cache. Indeed, when the task is loaded from cache, any such state
files must be cleaned up to prevent stale state from confusing the tool during
the next execution. Gradle can ensure such stale files are removed if they are
declared via
[task.localState.register()](../javadoc/org/gradle/api/tasks/TaskLocalState.html#register-
java.lang.Object...-) or if a property is marked with the
[@LocalState](../javadoc/org/gradle/api/tasks/LocalState.html) annotation.

## Declaring and Using Command Line Options

Sometimes a user wants to declare the value of an exposed task property on the
command line instead of the build script. Being able to pass in property
values on the command line is particularly helpful if they change more
frequently. The task API supports a mechanism for marking a property to
automatically generate a corresponding command line parameter with a specific
name at runtime.

### Declaring a command-line option

Exposing a new command line option for a task property is straightforward. You
just have to annotate the corresponding setter method of a property with
[Option](../javadoc/org/gradle/api/tasks/options/Option.html). An option
requires a mandatory identifier. Additionally, you can provide an optional
description. A task can expose as many command line options as properties
available in the class.

Let's have a look at an example to illustrate the functionality. The custom
task `UrlVerify` verifies whether a given URL can be resolved by making a HTTP
call and checking the response code. The URL to be verified is configurable
through the property `url`. The setter method for the property is annotated
with [@Option](../javadoc/org/gradle/api/tasks/options/Option.html).

### Example: Declaring a command line option

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

All options declared for a task can be rendered as console output by running
the `help` task and the `--task` option.

### Using an option on the command line

Using an option on the command line has to adhere to the following rules:

  * The option uses a double-dash as prefix e.g. `--url`. A single dash does not qualify as valid syntax for a task option.

  * The option argument follows directly after the task declaration e.g. `verifyUrl --url=http://www.google.com/`.

  * Multiple options of a task can be declared in any order on the command line following the task name.

Getting back to the previous example, the build script creates a task instance
of type `UrlVerify` and provides a value from the command line through the
exposed option.

Example 13. Using a command line option

build.gradle

    
    
    task verifyUrl(type: UrlVerify)

build.gradle.kts

    
    
    tasks.register<UrlVerify>("verifyUrl")

Output of **`gradle -q verifyUrl --url=http://www.google.com/`**

    
    
    > gradle -q verifyUrl --url=http://www.google.com/
    Verifying URL 'http://www.google.com/'

### Supported data types for options

Gradle limits the set of data types that can be used for declaring command
line options. The use on the command line differ per type.

`boolean`, `Boolean`, `Property<Boolean>`

    

Describes an option with the value `true` or `false`. Passing the option on
the command line treats the value as `true`. For example `--enabled` equates
to `true`. The absence of the option uses the default value of the property.

`String`, `Property<String>`

    

Describes an option with an arbitrary String value. Passing the option on the
command line also requires a value e.g. `--container-id=2x94held` or
`--container-id 2x94held`.

`enum`, `Property<enum>`

    

Describes an option as an enumerated type. Passing the option on the command
line also requires a value e.g. `--log-level=DEBUG` or `--log-level debug`.
The value is not case sensitive.

`List<String>`, `List<enum>`

    

Describes an option that can takes multiple values of a given type. The values
for the option have to be provided as multiple declarations e.g. `--image-
id=123 --image-id=456`. Other notations such as comma-separated lists or
multiple values separated by a space character are currently not supported.

### Documenting available values for an option

In theory, an option for a property type `String` or `List<String>` can accept
any arbitrary value. Expected values for such an option can be documented
programmatically with the help of the annotation
[OptionValues](../javadoc/org/gradle/api/tasks/options/OptionValues.html).
This annotation may be assigned to any method that returns a `List` of one of
the supported data types. In addition, you have to provide the option
identifier to indicate the relationship between option and available values.

__ |

Passing a value on the command line that is not supported by the option does
not fail the build or throw an exception. You'll have to implement custom
logic for such behavior in the task action.  
  
---|---  
  
This example demonstrates the use of multiple options for a single task. The
task implementation provides a list of available values for the option
`output-type`.

### Example: Declaring available values for an option

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

### Listing command line options

Command line options using the annotations
[Option](../javadoc/org/gradle/api/tasks/options/Option.html) and
[OptionValues](../javadoc/org/gradle/api/tasks/options/OptionValues.html) are
self-documenting. You will see declared options and their available values
reflected in the console output of the `help` task. The output renders options
in alphabetical order.

### Example: Listing available values for option

Output of **`gradle -q help --task processUrl`**

    
    
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

### Limitations

Support for declaring command line options currently comes with a few
limitations.

  * Command line options can only be declared for custom tasks via annotation. There's no programmatic equivalent for defining options.

  * Options cannot be declared globally e.g. on a project-level or as part of a plugin.

  * When assigning an option on the command line then the task exposing the option needs to be spelled out explicitly e.g. `gradle check --tests abc` does not work even though the `check` task depends on the `test` task.

## The Worker API

__ |

The Worker API is an [incubating](feature_lifecycle.html#feature_lifecycle)
feature.  
  
---|---  
  
As can be seen from the discussion of incremental tasks, the work that a task
performs can be viewed as discrete units (i.e. a subset of inputs that are
transformed to a certain subset of outputs). Many times, these units of work
are highly independent of each other, meaning they can be performed in any
order and simply aggregated together to form the overall action of the task.
In a single threaded execution, these units of work would execute in sequence,
however if we have multiple processors, it would be desirable to perform
independent units of work concurrently. By doing so, we can fully utilize the
available resources at build time and complete the activity of the task
faster.

The Worker API provides a mechanism for doing exactly this. It allows for
safe, concurrent execution of multiple items of work during a task action. But
the benefits of the Worker API are not confined to parallelizing the work of a
task. You can also configure a desired level of isolation such that work can
be executed in an isolated classloader or even in an isolated process.
Furthermore, the benefits extend beyond even the execution of a single task.
Using the Worker API, Gradle can begin to execute tasks in parallel by
default. In other words, once a task has submitted its work to be executed
asynchronously, and has exited the task action, Gradle can then begin the
execution of other independent tasks in parallel, even if those tasks are in
the same project.

### Using the Worker API

In order to submit work to the Worker API, two things must be provided: an
implementation of the unit of work, and the parameters for the unit of work.

The parameters for the unit of work are defined as an interface or abstract
class that implements
[WorkParameters](../javadoc/org/gradle/workers/WorkParameters.html). The
parameters type must be a [managed
type](custom_gradle_types.html#managed_types).

You can find out more about implementing work parameters in [Developing Custom
Gradle Types](custom_gradle_types.html#custom_gradle_types).

The implementation is a class that extends
[WorkAction](../javadoc/org/gradle/workers/WorkAction.html). This class should
be abstract and should not implement the `getParameters()` method. Gradle will
inject an implementation of this method at runtime with the parameters object
for each unit of work.

Example 14. Defining the unit of work parameters and implementation

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

A `WorkAction` implementation can inject services that provide capabilities
during work execution, such as the
[FileSystemOperations](../javadoc/org/gradle/api/file/FileSystemOperations.html)
service in the example above. See [Service
Injection](custom_gradle_types.html#service_injection) for further information
on injecting service types.

In order to submit the unit of work, it is necessary to first acquire the
[WorkerExecutor](../javadoc/org/gradle/workers/WorkerExecutor.html). To do
this, a task should have a constructor annotated with `javax.inject.Inject`
that accepts a
[WorkerExecutor](../javadoc/org/gradle/workers/WorkerExecutor.html) parameter.
Gradle will inject the instance of
[WorkerExecutor](../javadoc/org/gradle/workers/WorkerExecutor.html) at runtime
when the task is created. Then a
[WorkQueue](../javadoc/org/gradle/workers/WorkQueue.html) object can be
created and individual items of work can be submitted.

Example 15. Submitting a unit of work for execution

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

Once all of the work for a task action has been submitted, it is safe to exit
the task action. The work will be executed asynchronously and in parallel (up
to the setting of `max-workers`). Of course, any tasks that are dependent on
this task (and any subsequent task actions of this task) will not begin
executing until all of the asynchronous work completes. However, other
independent tasks that have no relationship to this task can begin executing
immediately.

If any failures occur while executing the asynchronous work, the task will
fail and a
[WorkerExecutionException](../javadoc/org/gradle/workers/WorkerExecutionException.html)
will be thrown detailing the failure for each failed work item. This will be
treated like any failure during task execution and will prevent any dependent
tasks from executing.

In some cases, however, it might be desirable to wait for work to complete
before exiting the task action. This is possible using the
[WorkQueue.await()](../javadoc/org/gradle/workers/WorkQueue.html#await--)
method. As in the case of allowing the work to complete asynchronously, any
failures that occur while executing an item of work will be surfaced as a
[WorkerExecutionException](../javadoc/org/gradle/workers/WorkerExecutionException.html)
thrown from the
[WorkQueue.await()](../javadoc/org/gradle/workers/WorkQueue.html#await--)
method.

__ |

Note that Gradle will only begin running other independent tasks in parallel
when a task has exited a task action and returned control of execution to
Gradle. When
[WorkQueue.await()](../javadoc/org/gradle/workers/WorkQueue.html#await--) is
used, execution does not leave the task action. This means that Gradle will
not allow other tasks to begin executing and will wait for the task action to
complete before doing so.  
  
---|---  
  
Example 16. Waiting for asynchronous work to complete

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

### Isolation Modes

Gradle provides three isolation modes that can be configured when creating a
[WorkQueue](../javadoc/org/gradle/workers/WorkQueue.html) and are specified
using the one of the following methods on
[WorkerExecutor](../javadoc/org/gradle/workers/WorkerExecutor.html):

[WorkerExecutor.noIsolation()](../javadoc/org/gradle/workers/WorkerExecutor.html#noIsolation--)

    

This states that the work should be run in a thread with a minimum of
isolation. For instance, it will share the same classloader that the task is
loaded from. This is the fastest level of isolation.

[WorkerExecutor.classLoaderIsolation()](../javadoc/org/gradle/workers/WorkerExecutor.html#classLoaderIsolation-
org.gradle.api.Action-)

    

This states that the work should be run in a thread with an isolated
classloader. The classloader will have the classpath from the classloader that
the unit of work implementation class was loaded from as well as any
additional classpath entries added through
[ClassLoaderWorkerSpec.getClasspath()](../javadoc/org/gradle/workers/ClassLoaderWorkerSpec.html#getClasspath--).

[WorkerExecutor.processIsolation()](../javadoc/org/gradle/workers/WorkerExecutor.html#processIsolation-
org.gradle.api.Action-)

    

This states that the work should be run with a maximum level of isolation by
executing the work in a separate process. The classloader of the process will
use the classpath from the classloader that the unit of work was loaded from
as well as any additional classpath entries added through
[ClassLoaderWorkerSpec.getClasspath()](../javadoc/org/gradle/workers/ClassLoaderWorkerSpec.html#getClasspath--).
Furthermore, the process will be a _Worker Daemon_ which will stay alive and
can be reused for future work items that may have the same requirements. This
process can be configured with different settings than the Gradle JVM using
[ProcessWorkerSpec.forkOptions(org.gradle.api.Action)](../javadoc/org/gradle/workers/ProcessWorkerSpec.html#forkOptions-
org.gradle.api.Action-).

### Worker Daemons

When using `processIsolation()`, gradle will start a long-lived _Worker
Daemon_ process that can be reused for future work items.

Example 17. Submitting an item of work to run in a worker daemon

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

When a unit of work for a Worker Daemon is submitted, Gradle will first look
to see if a compatible, idle daemon already exists. If so, it will send the
unit of work to the idle daemon, marking it as busy. If not, it will start a
new daemon. When evaluating compatibility, Gradle looks at a number of
criteria, all of which can be controlled through
[ProcessWorkerSpec.forkOptions(org.gradle.api.Action)](../javadoc/org/gradle/workers/ProcessWorkerSpec.html#forkOptions-
org.gradle.api.Action-).

By default, a worker daemon starts with a maximum heap of 512MB. This can be
changed by adjusting the workers fork options.

executable

    

A daemon is considered compatible only if it uses the same java executable.

classpath

    

A daemon is considered compatible if its classpath contains all of the
classpath entries requested. Note that a daemon is considered compatible only
if the classpath exactly matches the requested classpath.

heap settings

    

A daemon is considered compatible if it has at least the same heap size
settings as requested. In other words, a daemon that has higher heap settings
than requested would be considered compatible.

jvm arguments

    

A daemon is considered compatible if it has set all of the jvm arguments
requested. Note that a daemon is considered compatible if it has additional
jvm arguments beyond those requested (except for arguments treated specially
such as heap settings, assertions, debug, etc).

system properties

    

A daemon is considered compatible if it has set all of the system properties
requested with the same values. Note that a daemon is considered compatible if
it has additional system properties beyond those requested.

environment variables

    

A daemon is considered compatible if it has set all of the environment
variables requested with the same values. Note that a daemon is considered
compatible if it has more environment variables in addition to those
requested.

bootstrap classpath

    

A daemon is considered compatible if it contains all of the bootstrap
classpath entries requested. Note that a daemon is considered compatible if it
has more bootstrap classpath entries in addition to those requested.

debug

    

A daemon is considered compatible only if debug is set to the same value as
requested (true or false).

enable assertions

    

A daemon is considered compatible only if enable assertions is set to the same
value as requested (true or false).

default character encoding

    

A daemon is considered compatible only if the default character encoding is
set to the same value as requested.

Worker daemons will remain running until either the build daemon that started
them is stopped, or system memory becomes scarce. When available system memory
is low, Gradle will begin stopping worker daemons in an attempt to minimize
memory consumption.

## Cancellation and timeouts

In order to support cancellation (e.g. when the user stops the build with
CTRL+C) and task timeouts, custom tasks should react to their executing thread
being interrupted. The same is true for work items submitted via the worker
API. If a task does not respond to an interrupt within 10s, the daemon will
shut down in order to free up system resources.

## More details

It's often a good approach to package custom task types in a custom Gradle
plugin. The plugin can provide useful defaults and conventions for the task
type, and provides a convenient way to use the task type from a build script
or another plugin. Please see [Developing Custom Gradle
Plugins](custom_plugins.html#custom_plugins) for more details.

Gradle provides a number of features that are helpful when developing Gradle
types, including tasks. Please see [Developing Custom Gradle
Types](custom_gradle_types.html) for more details.

**Docs**

  * [User Manual](/userguide/userguide.html)
  * [DSL Reference](/dsl/)
  * [Release Notes](/release-notes.html)
  * [Javadoc](/javadoc/)

**News**

  * [Blog](https://blog.gradle.org/)
  * [Newsletter](https://newsletter.gradle.com/)
  * [Twitter](https://twitter.com/gradle)
  * [Status Page](https://status.gradle.com/)

**Products**

  * [Build Scans](https://gradle.com/build-scans/)
  * [Build Cache](https://gradle.com/build-cache/)
  * [Enterprise Docs](https://gradle.com/enterprise/resources/)

**Get Help**

  * [Forums](https://discuss.gradle.org/c/help-discuss)
  * [GitHub](https://github.com/gradle/)
  * [Training](https://gradle.com/training/)
  * [Services](https://gradle.org/services/)

##### Stay `UP-TO-DATE` on new features and news

By entering your email, you agree to our
[Terms](https://gradle.com/legal/terms-of-service/) and [Privacy
Policy](https://gradle.com/legal/privacy/), including receipt of emails. You
can unsubscribe at any time.

Subscribe

© [Gradle Inc.](https://gradle.com) 2020 All rights reserved.

[ gradle ](/)

[Careers](https://gradle.com/careers/) |
[Privacy](https://gradle.com/legal/privacy/) | [Terms of
Service](https://gradle.com/legal/terms-of-service/) |
[Contact](https://gradle.org/contact/)

