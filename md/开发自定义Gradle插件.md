

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

# Developing Custom Gradle Plugins

version 6.7.1

Contents

  * Packaging a plugin
  * Writing a simple plugin
  * Making the plugin configurable
  * Working with files in custom tasks and plugins
  * Mapping extension properties to task properties
  * A standalone project
  * Precompiled script plugins
  * Writing tests for your plugin
  * More details
  * Behind the scenes

A Gradle plugin packages up reusable pieces of build logic, which can be used
across many different projects and builds. Gradle allows you to implement your
own plugins, so you can reuse your build logic, and share it with others.

You can implement a Gradle plugin in any language you like, provided the
implementation ends up compiled as JVM bytecode. In our examples, we are going
to use Java as the implementation language for standalone plugin project and
Groovy or Kotlin in the buildscript plugin examples. In general, a plugin
implemented using Java or Kotlin, which are statically typed, will perform
better than the same plugin implemented using Groovy.

## Packaging a plugin

There are several places where you can put the source for the plugin.

Build script

    

You can include the source for the plugin directly in the build script. This
has the benefit that the plugin is automatically compiled and included in the
classpath of the build script without you having to do anything. However, the
plugin is not visible outside the build script, and so you cannot reuse the
plugin outside the build script it is defined in.

`buildSrc` project

    

You can put the source for the plugin in the `_rootProjectDir_
/buildSrc/src/main/java` directory (or `_rootProjectDir_
/buildSrc/src/main/groovy` or `_rootProjectDir_ /buildSrc/src/main/kotlin`
depending on which language you prefer). Gradle will take care of compiling
and testing the plugin and making it available on the classpath of the build
script. The plugin is visible to every build script used by the build.
However, it is not visible outside the build, and so you cannot reuse the
plugin outside the build it is defined in.

See [Organizing Gradle
Projects](organizing_gradle_projects.html#organizing_gradle_projects) for more
details about the `buildSrc` project.

Standalone project

    

You can create a separate project for your plugin. This project produces and
publishes a JAR which you can then use in multiple builds and share with
others. Generally, this JAR might include some plugins, or bundle several
related task classes into a single library. Or some combination of the two.

In our examples, we will start with the plugin in the build script, to keep
things simple. Then we will look at creating a standalone project.

## Writing a simple plugin

To create a Gradle plugin, you need to write a class that implements the
[Plugin](../javadoc/org/gradle/api/Plugin.html) interface. When the plugin is
applied to a project, Gradle creates an instance of the plugin class and calls
the instance's
[Plugin.apply()](../javadoc/org/gradle/api/Plugin.html#apply-T-) method. The
project object is passed as a parameter, which the plugin can use to configure
the project however it needs to. The following sample contains a greeting
plugin, which adds a `hello` task to the project.

Example 1. A custom plugin

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

Output of **`gradle -q hello`**

    
    
    > gradle -q hello
    Hello from the GreetingPlugin

One thing to note is that a new instance of a plugin is created for each
project it is applied to. Also note that the
[Plugin](../javadoc/org/gradle/api/Plugin.html) class is a generic type. This
example has it receiving the [Project](../dsl/org.gradle.api.Project.html)
type as a type parameter. A plugin can instead receive a parameter of type
[Settings](../dsl/org.gradle.api.initialization.Settings.html), in which case
the plugin can be applied in a settings script, or a parameter of type
[Gradle](../dsl/org.gradle.api.invocation.Gradle.html), in which case the
plugin can be applied in an initialization script.

## Making the plugin configurable

Most plugins offer some configuration options for build scripts and other
plugins to use to customize how the plugin works. Plugins do this using
_extension objects_. The Gradle [Project](../dsl/org.gradle.api.Project.html)
has an associated
[ExtensionContainer](../javadoc/org/gradle/api/plugins/ExtensionContainer.html)
object that contains all the settings and properties for the plugins that have
been applied to the project. You can provide configuration for your plugin by
adding an extension object to this container. An extension object is simply an
object with Java Bean properties that represent the configuration.

Let's add a simple extension object to the project. Here we add a `greeting`
extension object to the project, which allows you to configure the greeting.

Example 2. A custom plugin extension

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

Output of **`gradle -q hello`**

    
    
    > gradle -q hello
    Hi from Gradle

In this example, `GreetingPluginExtension` is an object with a property called
`message`. The extension object is added to the project with the name
`greeting`. This object then becomes available as a project property with the
same name as the extension object.

Oftentimes, you have several related properties you need to specify on a
single plugin. Gradle adds a configuration block for each extension object, so
you can group settings together. The following example shows you how this
works.

Example 3. A custom plugin with configuration block

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

Output of **`gradle -q hello`**

    
    
    > gradle -q hello
    Hi from Gradle

In this example, several settings can be grouped together within the
`greeting` closure. The name of the closure block in the build script
(`greeting`) needs to match the extension object name. Then, when the closure
is executed, the fields on the extension object will be mapped to the
variables within the closure based on the standard Groovy closure delegate
feature.

In this example, several settings can be grouped together within the
`configure<GreetingPluginExtension>` block. The type used on the `configure`
function in the build script (`GreetingPluginExtension`) needs to match the
extension type. Then, when the block is executed, the receiver of the block is
the extension.

In this way, using an extension object _extends_ the Gradle DSL to add a
project property and DSL block for the plugin. And because an extension object
is simply a regular object, you can provide your own DSL nested inside the
plugin block by adding properties and methods to the extension object.

### Developing project extensions

You can find out more about implementing project extensions in [Developing
Custom Gradle Types](custom_gradle_types.html#custom_gradle_types).

## Working with files in custom tasks and plugins

When developing custom tasks and plugins, it's a good idea to be very flexible
when accepting input configuration for file locations. To do this, you can
leverage the
[Project.file(java.lang.Object)](../dsl/org.gradle.api.Project.html#org.gradle.api.Project:file\(java.lang.Object\))
method to resolve values to files as late as possible.

Example 4. Evaluating file properties lazily

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

Output of **`gradle -q sayGreeting`**

    
    
    > gradle -q sayGreeting
    Hello!

In this example, we configure the `greet` task `destination` property as a
closure/provider, which is evaluated with the
[Project.file(java.lang.Object)](../dsl/org.gradle.api.Project.html#org.gradle.api.Project:file\(java.lang.Object\))
method to turn the return value of the closure/provider into a `File` object
at the last minute. You will notice that in the example above we specify the
`greetingFile` property value after we have configured to use it for the task.
This kind of lazy evaluation is a key benefit of accepting any value when
setting a file property, then resolving that value when reading the property.

## Mapping extension properties to task properties

Capturing user input from the build script through an extension and mapping it
to input/output properties of a custom task is a useful pattern. The build
script author interacts only with the DSL defined by the extension. The
imperative logic is hidden in the plugin implementation.

Gradle provides some types that you can use in task implementations and
extensions to help you with this. Refer to [Lazy
Configuration](lazy_configuration.html#lazy_configuration) for more
information.

## A standalone project

Now we will move our plugin to a standalone project so that we can publish it
and share it with others. This project is simply a Java project that produces
a JAR containing the plugin classes. The easiest and the recommended way to
package and publish a plugin is to use the [Java Gradle Plugin Development
Plugin](java_gradle_plugin.html#java_gradle_plugin). This plugin will
automatically apply the [Java Plugin](java_plugin.html#java_plugin), add the
`gradleApi()` dependency to the api configuration, generate the required
plugin descriptors in the resulting JAR file and configure the [Plugin Marker
Artifact](plugins.html#sec:plugin_markers) to be used when publishing. Here is
a simple build script for the project.

Example 5. A build for a custom plugin

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

### Creating a plugin id

Plugin ids are fully qualified in a manner similar to Java packages (i.e. a
reverse domain name). This helps to avoid collisions and provides a way to
group plugins with similar ownership.

Your plugin id should be a combination of components that reflect namespace (a
reasonable pointer to you or your organization) and the name of the plugin it
provides. For example if you had a Github account named "foo" and your plugin
was named "bar", a suitable plugin id might be `com.github.foo.bar`.
Similarly, if the plugin was developed at the baz organization, the plugin id
might be `org.baz.bar`.

Plugin ids should conform to the following:

  * May contain any alphanumeric character, '.', and '-'.

  * Must contain at least one '.' character separating the namespace from the name of the plugin.

  * Conventionally use a lowercase reverse domain name convention for the namespace.

  * Conventionally use only lowercase characters in the name.

  * `org.gradle` and `com.gradleware` namespaces may not be used.

  * Cannot start or end with a '.' character.

  * Cannot contain consecutive '.' characters (i.e. '..').

Although there are conventional similarities between plugin ids and package
names, package names are generally more detailed than is necessary for a
plugin id. For instance, it might seem reasonable to add "gradle" as a
component of your plugin id, but since plugin ids are only used for Gradle
plugins, this would be superfluous. Generally, a namespace that identifies
ownership and a name are all that are needed for a good plugin id.

### Publishing your plugin

If you are publishing your plugin internally for use within your organization,
you can publish it like any other code artifact. See the
[Ivy](publishing_ivy.html#publishing_ivy) and
[Maven](publishing_maven.html#publishing_maven) chapters on publishing
artifacts.

If you are interested in publishing your plugin to be used by the wider Gradle
community, you can publish it to the [Gradle Plugin
Portal](http://plugins.gradle.org). This site provides the ability to search
for and gather information about plugins contributed by the Gradle community.
Please refer to the corresponding
[guide](https://guides.gradle.org/publishing-plugins-to-gradle-plugin-portal/)
on how to make your plugin available on this site.

### Using your plugin in another project

To use a plugin in a build script, you need to configure the repository in
`pluginManagement {}` block of the project's settings file. The following
example shows how you might do this when the plugin has been published to a
local repository:

Example 6. Using a custom plugin in another project

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

#### Note for plugins published without `java-gradle-plugin`

If your plugin was published without using the [Java Gradle Plugin Development
Plugin](java_gradle_plugin.html#java_gradle_plugin), the publication will be
lacking [Plugin Marker Artifact](plugins.html#sec:plugin_markers), which is
needed for [plugins DSL](plugins.html#sec:plugins_block) to locate the plugin.
In this case, the recommended way to resolve the plugin in another project is
to add a `resolutionStrategy` section to the `pluginManagement {}` block of
the project's settings file as shown below.

Example 7. Resolution strategy for plugins without Plugin Marker Artifact

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

## Precompiled script plugins

In addition to plugins written as standalone projects, Gradle also allows you
to provide build logic written in either Groovy or Kotlin DSLs as precompiled
script plugins. You write these as `*.gradle` files in `src/main/groovy`
directory or `*.gradle.kts` files in `src/main/kotlin` directory.

Precompiled script plugins are compiled into class files and packaged into a
jar. For all intents and purposes, they are binary plugins and can be applied
by plugin ID, tested and published as binary plugins. In fact, the plugin
metadata for them is generated using the [Gradle Plugin Development
Plugin](java_gradle_plugin.html#java_gradle_plugin).

__ |

Kotlin DSL precompiled script plugins built with Gradle 6.0 cannot be used
with earlier versions of Gradle. This limitation will be lifted in a future
version of Gradle.

Groovy DSL precompiled script plugins are available starting with Gradle 6.4.  
  
---|---  
  
To apply a precompiled script plugin, you need to know its ID which is derived
from the plugin script's filename (minus the `.gradle` extension).

To apply a precompiled script plugin, you need to know its ID which is derived
from the plugin script's filename (minus the `.gradle.kts` extension) and its
(optional) package declaration.

For example, the script `src/main/groovy/java-library-convention.gradle` would
have a plugin ID of `java-library-convention`. Likewise,
`src/main/groovy/my.java-library-convention.gradle` would result in a plugin
ID of `my.java-library-convention`.

For example, the script `src/main/kotlin/java-library-convention.gradle.kts`
would have a plugin ID of `java-library-convention` (assuming it has no
package declaration). Likewise, `src/main/kotlin/my/java-library-
convention.gradle.kts` would result in a plugin ID of `my.java-library-
convention` as long as it has a package declaration of `my`.

To demonstrate how you can implement and use a precompiled script plugin,
let's walk through an example based on a `buildSrc` project.

First, you need a `buildSrc/build.gradle` file that applies the `groovy-
gradle-plugin` plugin:

First, you need a `buildSrc/build.gradle.kts` file that applies the `kotlin-
dsl` plugin:

Example 8. Enabling precompiled script plugins

buildSrc/build.gradle

    
    
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

We recommend that you also create a `buildSrc/settings.gradle` file, which you
may leave empty.

We recommend that you also create a `buildSrc/settings.gradle.kts` file, which
you may leave empty.

Next, create a new `java-library-convention.gradle` file in the
`buildSrc/src/main/groovy` directory and set its contents to the following:

Next, create a new `java-library-convention.gradle.kts` file in the
`buildSrc/src/main/kotlin` directory and set its contents to the following:

Example 9. Creating a simple script plugin

buildSrc/src/main/groovy/java-library-convention.gradle

    
    
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

This script plugin simply applies the Java Library and Checkstyle Plugins and
configures them. Note that this will actually apply the plugins to the main
project, i.e. the one that applies the precompiled script plugin.

Finally, apply the script plugin to the root project as follows:

Example 10. Applying the precompiled script plugin to the main project

build.gradle

    
    
    plugins {
        id 'java-library-convention'
    }

build.gradle.kts

    
    
    plugins {
        `java-library-convention`
    }

### Applying external plugins in precompiled script plugins

In order to apply an external plugin in a precompiled script plugin, it has to
be added to the plugin project's implementation classpath in the plugin's
build file.

buildSrc/build.gradle

    
    
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

It can then be applied in the precompiled script plugin.

buildSrc/src/main/groovy/my-plugin.gradle

    
    
    plugins {
        id 'com.bmuschko.docker-remote-api'
    }

buildSrc/src/main/kotlin/my-plugin.gradle.kts

    
    
    plugins {
        id("com.bmuschko.docker-remote-api")
    }

The plugin version in this case is defined in the dependency declaration.

This limitation will be removed in future versions of Gradle.

## Writing tests for your plugin

You can use the
[ProjectBuilder](../javadoc/org/gradle/testfixtures/ProjectBuilder.html) class
to create [Project](../dsl/org.gradle.api.Project.html) instances to use when
you test your plugin implementation.

### Example: Testing a custom plugin

src/test/java/org/gradle/GreetingPluginTest.java

    
    
    public class GreetingPluginTest {
        @Test
        public void greeterPluginAddsGreetingTaskToProject() {
            Project project = ProjectBuilder.builder().build();
            project.getPluginManager().apply("org.samples.greeting");
    
            assertTrue(project.getTasks().getByName("hello") instanceof GreetingTask);
        }
    }

## More details

Plugins often also provide custom task types. Please see [Developing Custom
Gradle Task Types](custom_tasks.html#custom_tasks) for more details.

Gradle provides a number of features that are helpful when developing Gradle
types, including plugins. Please see [Developing Custom Gradle
Types](custom_gradle_types.html#custom_gradle_types) for more details.

__ |  When developing Gradle Plugins, it is important to be cautious when
logging information to the build log. Logging sensitive information (e.g.
credentials, tokens, certain environment variables) is [considered a security
vulnerability](logging.html#sec:debug_security). Build logs for public
Continuous Integration services are world-viewable and can expose this
sensitive information.  
---|---  
  
## Behind the scenes

So how does Gradle find the [Plugin](../javadoc/org/gradle/api/Plugin.html)
implementation? The answer is - you need to provide a properties file in the
JAR's `META-INF/gradle-plugins` directory that matches the id of your plugin,
which is handled by [Java Gradle Plugin Development
Plugin](java_gradle_plugin.html#java_gradle_plugin).

### Example: Wiring for a custom plugin

src/main/resources/META-INF/gradle-plugins/org.samples.greeting.properties

    
    
    implementation-class=org.gradle.GreetingPlugin

Notice that the properties filename matches the plugin id and is placed in the
resources folder, and that the `implementation-class` property identifies the
[Plugin](../javadoc/org/gradle/api/Plugin.html) implementation class.

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

