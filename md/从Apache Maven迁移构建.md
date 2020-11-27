

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

# Migrating Builds From Apache Maven

version 6.7.1

Contents

  * Making a case for migration
  * General guidelines
  * Understanding the build lifecycle
  * Performing an automatic conversion
  * Migrating dependencies
  * Using bills of materials (BOMs)
  * Migrating multi-module builds (project aggregation)
  * Migrating Maven profiles and properties
  * Filtering resources
  * Configuring integration tests
  * Migrating common plugins
  * Understanding which plugins you don't need
  * Dealing with uncommon and custom plugins
  * Further reading

__ |  Suffering from slow Maven builds? [Register
here](https://gradle.com/training/build-cache-deep-dive/?bid=docs-migrating-
maven) for our Build Cache training session to learn how Gradle Enterprise can
speed up Maven builds by up to 90%.  
---|---  
  
[Apache Maven](https://maven.apache.org) is a build tool for Java and other
JVM-based projects that's in widespread use, and so people that want to use
Gradle often have to migrate an existing Maven build. This guide will help
with such a migration by explaining the differences and similarities between
the two tools' models and providing steps that you can follow to ease the
process.

Converting a build can be scary, but you don't have to do it alone. You can
search docs, forums, and StackOverflow from
[help.gradle.org](https://gradle.org/help) or reach out to the [Gradle
community on the forums](https://discuss.gradle.org/c/help-discuss) if you get
stuck.

## Making a case for migration

The primary differences between Gradle and Maven are flexibility, performance,
user experience, and dependency management. A visual overview of these aspects
is available in the [Maven vs Gradle feature
comparison](https://gradle.org/maven-vs-gradle).

Since Gradle 3.0, Gradle has invested heavily in making Gradle builds much
faster, with features such as [build
caching](https://blog.gradle.org/introducing-gradle-build-cache), [compile
avoidance](https://blog.gradle.org/incremental-compiler-avoidance), and an
improved incremental Java compiler. Gradle is now 2-10x faster than Maven for
the vast majority of projects, even without using a build cache. In-depth
performance comparison and business cases for switching from Maven to Gradle
can be found [here](https://gradle.org/gradle-vs-maven-performance/).

## General guidelines

Gradle and Maven have fundamentally different views on how to build a project.
Gradle provides a flexible and extensible build model that delegates the
actual work to a [_graph of task
dependencies_](what_is_gradle.html#the_core_model_is_based_on_tasks). Maven
uses a model of fixed, linear phases to which you can attach goals (the things
that do the work). This may make migrating between the two seem intimidating,
but migrations can be surprisingly easy because Gradle follows many of the
same conventions as Maven -- such as the [standard project
structure](java_plugin.html#sec:java_project_layout) -- and its dependency
management works in a similar way.

Here we lay out a series of steps for you to follow that will help facilitate
the migration of any Maven build to Gradle:

__ |  Keep the old Maven build and new Gradle build side by side. You know the
Maven build works, so you should keep it until you are confident that the
Gradle build produces all the same artifacts and otherwise does what you need.
This also means that users can try the Gradle build without getting a new copy
of the source tree.  
---|---  
  
  1. [Create a build scan for the Maven build](https://scans.gradle.com#maven).

A build scan will make it easier to visualize what's happening in your
existing Maven build. For Maven builds, you'll be able to see the project
structure, what plugins are being used, a timeline of the build steps, and
more. Keep this handy so you can compare it to the Gradle build scans you get
while converting the project.

  2. Develop a mechanism to verify that the two builds produce the same artifacts

This is a vitally important step to ensure that your deployments and tests
don't break. Even small changes, such as the contents of a manifest file in a
JAR, can cause problems. If your Gradle build produces the same output as the
Maven build, this will give you and others confidence in switching over and
make it easier to implement the big changes that will provide the greatest
benefits.

This doesn't mean that you need to verify every artifact at every stage,
although doing so can help you quickly identify the source of a problem. You
can just focus on the critical output such as final reports and the artifacts
that are published or deployed.

You will need to factor in some inherent differences in the build output that
Gradle produces compared to Maven. Generated POMs will contain only the
information needed for consumption and they will use `<compile>` and
`<runtime>` scopes correctly for that scenario. You might also see differences
in the order of files in archives and of files on classpaths. Most differences
will be benign, but it's worth identifying them and verifying that they are
OK.

  3. Run an automatic conversion

This will create all the Gradle build files you need, even for multi-module
builds. For simpler Maven projects, the Gradle build will be ready to run!

  4. [Create a build scan for the Gradle build](https://scans.gradle.com).

A build scan will make it easier to visualize what's happening in the build.
For Gradle builds, you'll be able to see the project structure, the
dependencies (regular and inter-project ones), what plugins are being used and
the console output of the build.

Your build may fail at this point, but that's ok; the scan will still run.
Compare the build scan for the Gradle build to the one for the Maven build and
continue down this list to troubleshoot the failures.

We recommend that you regularly generate build scans during the migration to
help you identify and troubleshoot problems. If you want, you can also use a
Gradle build scan to identify opportunities to [improve the performance of the
build](https://guides.gradle.org/performance/), after all performance is a big
reason for switching to Gradle in the first place.

  5. Verify your dependencies and fix any problems

  6. Configure integration and functional tests

Many tests can simply be migrated by configuring an extra source set. If you
are using a third-party library, such as
[FitNesse](http://docs.fitnesse.org/FrontPage), look to see whether there is a
suitable community plugin available on the [Gradle Plugin
Portal](https://plugins.gradle.org/).

  7. Replace Maven plugins with Gradle equivalents

In the case of popular plugins, Gradle often has an equivalent plugin that you
can use. You might also find that you can replace a plugin with built-in
Gradle functionality. As a last resort, you may need to reimplement a Maven
plugin via your own custom plugins and task types.

The rest of this chapter looks in more detail at specific aspects of migrating
a build from Maven to Gradle.

## Understanding the build lifecycle

Maven builds are based around the concept of [_build
lifecycles_](https://maven.apache.org/guides/introduction/introduction-to-the-
lifecycle.html) that consist of a set of fixed phases. This can prove an
impediment for users migrating to Gradle because its build lifecycle is
[something different](build_lifecycle.html#build_lifecycle), although it's
important to understand how Gradle builds fit into the structure of
initialization, configuration, and execution phases. Fortunately, Gradle has a
feature that can mimic Maven's phases: [_lifecycle
tasks_](more_about_tasks.html#sec:lifecycle_tasks).

These allow you to define your own "lifecycles" by creating no-action tasks
that simply depend on the tasks you're interested in. And to make the
transition to Gradle easier for Maven users, the [Base
Plugin](base_plugin.html#sec:base_tasks) -- applied by all the JVM language
plugins like the [Java Library
Plugin](java_library_plugin.html#java_library_plugin) -- provides a set of
lifecycle tasks that correspond to the main Maven phases.

Here is a list of some of the main Maven phases and the Gradle tasks that they
map to:

`clean`

    

Use the `clean` task provided by the Base Plugin.

`compile`

    

Use the `classes` task provided by the [Java
Plugin](java_plugin.html#sec:java_tasks) and other JVM language plugins. This
compiles all classes for all source files of all languages and also performs
resource filtering via the `processResources` task.

`test`

    

Use the `test` task provided by the Java Plugin. It runs just the unit tests,
or more specifically, the tests that make up the [`test` source
set](java_plugin.html#source_sets).

`package`

    

Use the `assemble` task provided by the Base Plugin. This builds whatever is
the appropriate package for the project, for example a JAR for Java libraries
or a WAR for traditional Java webapps.

`verify`

    

Use the `check` task provided by the Base Plugin. This runs all verification
tasks that are attached to it, which typically includes the unit tests, any
static analysis tasks -- such as
[Checkstyle](checkstyle_plugin.html#checkstyle_plugin) -- and others. If you
want to include integration tests, you will have to configure these manually,
which is a simple process.

`install`

    

Use the `publishToMavenLocal` task provided by the [Maven Publish
Plugin](publishing_maven.html#publishing_maven:tasks).

Note that Gradle builds don't require you to "install" artifacts as you have
access to more appropriate features like [inter-project
dependencies](declaring_dependencies.html#sub:project_dependencies) and
[composite builds](composite_builds.html). You should only use
`publishToMavenLocal` for interoperating with Maven builds.

Gradle also allows you to resolve dependencies against the local Maven cache,
as described in the Declaring repositories section.

`deploy`

    

Use the `publish` task provided by the [Maven Publish
Plugin](publishing_maven.html#publishing_maven:tasks) -- making sure you
switch from the older Maven Plugin (ID: `maven`) if your build is using that
one. This will publish your package to all configured publication
repositories. There are also other tasks that allow you to publish to a single
repository even when multiple ones are defined.

Note that the Maven Publish Plugin does not publish **source and Javadoc
JARs** _by default_ , but this can easily be activated as explained in [the
guide for building java
projects](building_java_projects.html#sec:java_packaging).

## Performing an automatic conversion

Gradle's [`init` task](build_init_plugin.html#build_init_plugin) is typically
used to create a new skeleton project, but you can also use it to convert an
existing Maven build to Gradle automatically. Once Gradle is [installed on
your system](installation.html#installation), all you have to do is run the
command

    
    
    > gradle init

from the root project directory and let Gradle do its thing. That basically
consists of parsing the existing POMs and generating the corresponding Gradle
build scripts. Gradle will also create a settings script if you're migrating a
[multi-project build](multi_project_builds.html#multi_project_builds).

You'll find that the new Gradle build includes the following:

  * All the custom repositories that are specified in the POM

  * Your external and inter-project dependencies

  * The appropriate plugins to build the project (limited to one or more of the [Maven Publish](publishing_maven.html), [Java](java_plugin.html) and [War](war_plugin.html) Plugins)

See the [Build Init Plugin
chapter](build_init_plugin.html#sec:pom_maven_conversion) for a complete list
of the automatic conversion features.

One thing to bear in mind is that assemblies are not automatically converted.
They aren't necessarily problematic to convert, but you will need to do some
manual work. Options include:

  * Using the [Distribution Plugin](distribution_plugin.html#distribution_plugin)

  * Using the [Java Library Distribution Plugin](java_library_distribution_plugin.html#java_library_distribution_plugin)

  * Using the [Application Plugin](application_plugin.html#application_plugin)

  * [Creating custom archive tasks](working_with_files.html#sec:creating_archives_example)

  * Using a suitable community plugin from the [Gradle Plugin Portal](https://plugins.gradle.org/)

If your Maven build does not have many plugins or much in the way of
customisation, you can simply run

    
    
    > gradle build

once the migration has completed. This will run the tests and produce the
required artifacts without any extra intervention on your part.

## Migrating dependencies

Gradle's dependency management system is more flexible than Maven's, but it
still supports the same concepts of repositories, declared dependencies,
scopes ([dependency configurations](declaring_dependencies.html#sec:what-are-
dependency-configurations) in Gradle), and transitive dependencies. In fact,
Gradle works perfectly with Maven-compatible repositories, which makes it easy
to migrate your dependencies.

__ |  One notable difference between the two tools is in how they manage
version conflicts. Maven uses a "closest" match algorithm, whereas Gradle
picks the newest. Don't worry though, you have a lot of control over which
versions are selected, as documented in [Managing Transitive
Dependencies](dependency_constraints.html).  
---|---  
  
Over the following sections, we will show you how to migrate the most common
elements of a Maven build's dependency management information.

### Declaring dependencies

Gradle uses the same dependency identifier components as Maven: group ID,
artifact ID and version. It also supports classifiers. So all you need to do
is substitute the identifier information for a dependency into Gradle's
syntax, which is described in the [Declaring
Dependencies](declaring_dependencies.html) chapter.

For example, consider this Maven-style dependency on Log4J:

    
    
    <dependencies>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
    </dependencies>

This dependency would look like the following in a Gradle build script:

Example 1. Declaring a simple compile-time dependency

build.gradle

    
    
    dependencies {
        implementation 'log4j:log4j:1.2.12'  __**(1)**
    }

build.gradle.kts

    
    
    dependencies {
        implementation("log4j:log4j:1.2.12")  __**(1)**
    }

__**1** | Attaches version 1.2.12 of Log4J to the `implementation`
configuration (scope)  
---|---  
  
The string identifier takes the Maven values of `groupId`, `artifactId` and
`version`, although Gradle refers to them as `group`, `module` and `version`.

The above example raises an obvious question: what is that `implementation`
configuration? It's one of the standard dependency configurations provided by
the [Java Plugin](java_plugin.html#tab:configurations) and is often used as a
substitute for Maven's default `compile` scope.

Several of the differences between Maven's scopes and Gradle's standard
configurations come down to Gradle distinguishing between the dependencies
required to build a module and the dependencies required to build a module
that depends on it. Maven makes no such distinction, so published POMs
typically include dependencies that consumers of a library don't actually
need.

Here are the main Maven dependency scopes and how you should deal with their
migration:

`compile`

    

Gradle has two configurations that can be used in place of the `compile`
scope: `implementation` and `api`. The former is available to any project that
applies the Java Plugin, while `api` is only available to projects that
specifically apply the [Java Library
Plugin](java_library_plugin.html#java_library_plugin).

In most cases you should simply use the `implementation` configuration,
particularly if you're building an application or webapp. But if you're
building a library, you can learn about which dependencies should be declared
using `api` in the section on [Building Java
libraries](building_java_projects.html#sec:building_java_libraries). Even more
information on the differences between `api` and `implementation` is provided
in the Java Library Plugin chapter linked above.

`runtime`

    

Use the `runtimeOnly` configuration.

`test`

    

Gradle distinguishes between those dependencies that are required to _compile_
a project's tests and those that are only needed to _run_ them.

Dependencies required for test compilation should be declared against the
`testImplementation` configuration. Those that are only required for running
the tests should use `testRuntimeOnly`.

`provided`

    

Use the `compileOnly` configuration.

Note that the [War Plugin](war_plugin.html#sec:war_dependency_management) adds
`providedCompile` and `providedRuntime` dependency configurations. These
behave slightly differently from `compileOnly` and simply ensure that those
dependencies aren't packaged in the WAR file. However, the dependencies are
included on runtime and test runtime classpaths, so use these configurations
if that's the behavior you need.

`import`

    

The `import` scope is mostly used within `<dependencyManagement>` blocks and
applies solely to POM-only publications. Read the section on Using bills of
materials to learn more about how to replicate this behavior.

You can also specify a regular dependency on a POM-only publication. In this
case, the dependencies declared in that POM are treated as normal transitive
dependencies of the build.

For example, imagine you want to use the `groovy-all` POM for your tests. It's
a POM-only publication that has its own dependencies listed inside a
`<dependencies>` block. The appropriate configuration in the Gradle build
looks like this:

Example 2. Consuming a POM-only dependency

build.gradle

    
    
    dependencies {
        testImplementation 'org.codehaus.groovy:groovy-all:2.5.4'
    }

build.gradle.kts

    
    
    dependencies {
        testImplementation("org.codehaus.groovy:groovy-all:2.5.4")
    }

The result of this will be that all `compile` and `runtime` scope dependencies
in the `groovy-all` POM get added to the test runtime classpath, while only
the `compile` scope dependencies get added to the test compilation classpath.
Dependencies with other scopes will be ignored.

### Declaring repositories

Gradle allows you to retrieve declared dependencies from any Maven-compatible
or Ivy-compatible repository. Unlike Maven, it has no default repository and
so you have to declare at least one. In order to have the same behavior as
your Maven build, just configure [Maven
Central](declaring_repositories.html#sub:maven_central) in your Gradle build,
like this:

Example 3. Configuring the build to use Maven Central

build.gradle

    
    
    repositories {
        mavenCentral()
    }

build.gradle.kts

    
    
    repositories {
        mavenCentral()
    }

You can also use the `repositories {}` block to configure custom repositories,
as described in the [Repository
Types](declaring_repositories.html#sec:maven_repo) chapter.

Lastly, Gradle allows you to resolve dependencies against the [local Maven
cache/repository](declaring_repositories.html#sub:maven_local). This helps
Gradle builds interoperate with Maven builds, but it shouldn't be a technique
that you use if you don't need that interoperability. If you want to share
published artifacts via the filesystem, consider configuring a [custom Maven
repository](declaring_repositories.html#sec:maven_repo) with a `file://` URL.

You might also be interested in learning about Gradle's own [dependency
cache](dependency_resolution.html#sec:dependency_cache), which behaves more
reliably than Maven's and can be used safely by multiple concurrent Gradle
processes.

### Controlling dependency versions

The existence of transitive dependencies means that you can very easily end up
with multiple versions of the same dependency in your dependency graph. By
default, Gradle will pick the newest version of a dependency in the graph, but
that's not always the right solution. That's why it provides several
mechanisms for controlling which version of a given dependency is resolved.

On a per-project basis, you can use:

  * [Dependency constraints](dependency_constraints.html#sec:adding-constraints-transitive-deps)

  * Bills of materials (Maven BOMs)

  * [Overriding transitive versions](dependency_downgrade_and_exclude.html#sec:enforcing_dependency_version)

There are even more, specialized options listed in the [controlling transitive
dependencies](dependency_constraints.html) chapter.

If you want to ensure consistency of versions across all projects in a multi-
project build, similar to how the `<dependencyManagement>` block in Maven
works, you can use the [Java Platform
Plugin](java_platform_plugin.html#java_platform_plugin). This allows you
declare a set of dependency constraints that can be applied to multiple
projects. You can even publish the platform as a Maven BOM or using Gradle's
metadata format. See the plugin page for more information on how to do that,
and in particular the section on [Consuming
platforms](java_platform_plugin.html#sec:java_platform_consumption) to see how
you can apply a platform to other projects in the same build.

### Excluding transitive dependencies

Maven builds use exclusions to keep unwanted dependencies -- or unwanted
_versions_ of dependencies -- out of the dependency graph. You can do the same
thing with Gradle, but that's not necessarily the _right_ thing to do. Gradle
provides other options that may be more appropriate for a given situation, so
you really need to understand _why_ an exclusion is in place to migrate it
properly.

If you want to exclude a dependency for reasons unrelated to versions, then
check out the section on
[dependency_downgrade_and_exclude.html](dependency_downgrade_and_exclude.html#sec:excluding-
transitive-deps). It shows you how to attach an exclusion either to an entire
configuration (often the most appropriate solution) or to a dependency. You
can even easily apply an exclusion to all configurations.

If you're more interested in controlling which version of a dependency is
actually resolved, see the previous section.

### Handling optional dependencies

You are likely to encounter two situations regarding optional dependencies:

  * Some of your transitive dependencies are declared as optional

  * You want to declare some of your direct dependencies as optional in your project's published POM

For the first scenario, Gradle behaves the same way as Maven and simply
ignores any transitive dependencies that are declared as optional. They are
not resolved and have no impact on the versions selected if the same
dependencies appear elsewhere in the dependency graph as non-optional.

As for publishing dependencies as optional, Gradle provides a richer model
called [feature variants](feature_variants.html#feature_variants), which will
let you declare the "optional features" your library provides.

## Using bills of materials (BOMs)

Maven allows you to share dependency constraints by defining dependencies
inside a `<dependencyManagement>` section of a POM file that has a packaging
type of `pom`. This special type of POM (a BOM) can then be imported into
other POMs so that you have consistent library versions across your projects.

Gradle can use such BOMs for the same purpose, using a special dependency
syntax based on
[platform()](../dsl/org.gradle.api.artifacts.dsl.DependencyHandler.html#org.gradle.api.artifacts.dsl.DependencyHandler:platform\(java.lang.Object\))
and
[enforcedPlatform()](../dsl/org.gradle.api.artifacts.dsl.DependencyHandler.html#org.gradle.api.artifacts.dsl.DependencyHandler:enforcedPlatform\(java.lang.Object\))
methods. You simply declare the dependency in the normal way, but wrap the
dependency identifier in the appropriate method, as shown in this example that
"imports" the Spring Boot Dependencies BOM:

Example 4. Importing a BOM in a Gradle build

build.gradle

    
    
    dependencies {
        implementation platform('org.springframework.boot:spring-boot-dependencies:1.5.8.RELEASE') __**(1)**
    
        implementation 'com.google.code.gson:gson' __**(2)**
        implementation 'dom4j:dom4j'
    }

build.gradle.kts

    
    
    dependencies {
        implementation(platform("org.springframework.boot:spring-boot-dependencies:1.5.8.RELEASE"))  __**(1)**
    
        implementation("com.google.code.gson:gson")  __**(2)**
        implementation("dom4j:dom4j")
    }

__**1** | Applies the Spring Boot Dependencies BOM  
---|---  
__**2** | Adds a dependency whose version is defined by that BOM  
  
You can learn more about this feature and the difference between `platform()`
and `enforcedPlatform()` in the section on [importing version recommendations
from a Maven BOM](platforms.html#sub:bom_import).

__ |  You can use this feature to apply the `<dependencyManagement>`
information from any dependency's POM to the Gradle build, even those that
don't have a packaging type of `pom`. Both `platform()` and
`enforcedPlatform()` will ignore any dependencies declared in the
`<dependencies>` block.  
---|---  
  
## Migrating multi-module builds (project aggregation)

Maven's multi-module builds map nicely to Gradle's [multi-project
builds](multi_project_builds.html#multi_project_builds). Try the corresponding
[sample](../samples/sample_jvm_multi_project_build.html) to see how a basic
multi-project Gradle build is set up.

To migrate a multi-module Maven build, simply follow these steps:

  1. Create a settings script that matches the `<modules>` block of the root POM.

For example, this `<modules>` block:

    
        <modules>
        <module>simple-weather</module>
        <module>simple-webapp</module>
    </modules>

can be migrated by adding the following line to the settings script:

Example 5. Declaring which projects are part of the build

settings.gradle

    
        rootProject.name = 'simple-multi-module'  __**(1)**
    
    include 'simple-weather', 'simple-webapp'  __**(2)**

settings.gradle.kts

    
        rootProject.name = "simple-multi-module"  __**(1)**
    
    include("simple-weather", "simple-webapp")  __**(2)**

__**1** | Sets the name of the overall project  
---|---  
__**2** | Configures two subprojects as part of this build  
  
Output of **`gradle projects`**

    
        > gradle projects
    
    ------------------------------------------------------------
    Root project
    ------------------------------------------------------------
    
    Root project 'simple-multi-module'
    +--- Project ':simple-weather'
    \--- Project ':simple-webapp'
    
    To see a list of the tasks of a project, run gradle <project-path>:tasks
    For example, try running gradle :simple-weather:tasks

  2. Replace cross-module dependencies with [project dependencies](declaring_dependencies.html#sub:project_dependencies).

  3. Replicate project inheritance with [convention plugins](sharing_build_logic_between_subprojects.html#sec:convention_plugins).

This basically involves creating a root project build script that injects
shared configuration into the appropriate subprojects.

### Sharing versions across projects

If you want to replicate the Maven pattern of having dependency versions
declared in the `dependencyManagement` section of the root POM file, the best
approach is to leverage the `java-platform` plugin. You will need to add a
dedicated project for this and consume it in the regular projects of your
build. See [the documentation](java_platform_plugin.html) for more details on
this pattern.

## Migrating Maven profiles and properties

Maven allows you parameterize builds using properties of various sorts. Some
are read-only properties of the project model, others are user-defined in the
POM. It even allows you to treat system properties as project properties.

Gradle has a similar system of project properties, although it differentiates
between those and system properties. You can, for example, define properties
in:

  * the build script

  * a `gradle.properties` file in the root project directory

  * a `gradle.properties` file in the `$HOME/.gradle` directory

Those aren't the only options, so if you are interested in finding out more
about how and where you can define properties, check out the [Build
Environment](build_environment.html#build_environment) chapter.

One important piece of behavior you need to be aware of is what happens when
the same property is defined in both the build script and one of the external
properties files: the build script value takes precedence. Always.
Fortunately, you can mimic the concept of profiles to provide overridable
default values.

Which brings us on to Maven profiles. These are a way to enable and disable
different configurations based on environment, target platform, or any other
similar factor. Logically, they are nothing more than limited ‘if' statements.
And since Gradle has much more powerful ways to declare conditions, it does
not need to have formal support for profiles (except in the POMs of
dependencies). You can easily get the same behavior by combining conditions
with secondary build scripts, as you'll see.

Let's say you have different deployment settings depending on the environment:
local development (the default), a test environment, and production. To add
profile-like behavior, you first create build scripts for each environment in
the project root: `profile-default.gradle`, `profile-test.gradle`, and
`profile-prod.gradle`. You can then conditionally apply one of those profile
scripts based on a [project
property](build_environment.html#sec:project_properties) of your own choice.

The following example demonstrates the basic technique using a project
property called `buildProfile` and profile scripts that simply initialize an
[extra project property](writing_build_scripts.html#sec:extra_properties)
called `message`:

Example 6. Mimicking the behavior of Maven profiles in Gradle

build.gradle

    
    
    if (!hasProperty('buildProfile')) ext.buildProfile = 'default'  __**(1)**
    
    apply from: "profile-${buildProfile}.gradle"  __**(2)**
    
    task greeting {
        doLast {
            println message  __**(3)**
        }
    }

profile-default.gradle

    
    
    ext.message = 'foobar'  __**(4)**

profile-test.gradle

    
    
    ext.message = 'testing 1 2 3'  __**(4)**

profile-prod.gradle

    
    
    ext.message = 'Hello, world!'  __**(4)**

build.gradle.kts

    
    
    val buildProfile: String? by project  __**(1)**
    
    apply(from = "profile-${buildProfile ?: "default"}.gradle.kts")  __**(2)**
    
    tasks.register("greeting") {
        val message: String by project.extra
        doLast {
            println(message)  __**(3)**
        }
    }

profile-default.gradle.kts

    
    
    val message by extra("foobar")  __**(4)**

profile-test.gradle.kts

    
    
    val message by extra("testing 1 2 3")  __**(4)**

profile-prod.gradle.kts

    
    
    val message by extra("Hello, world!")  __**(4)**

__**1** | Checks for the existence of (Groovy) or binds (Kotlin) the
`buildProfile` project property  
---|---  
__**2** | Applies the appropriate profile script, using the value of
`buildProfile` in the script filename  
__**3** | Prints out the value of the `message` extra project property  
__**4** | Initializes the `message` extra project property, whose value can
then be used in the main build script  
  
With this setup in place, you can activate one of the profiles by passing a
value for the project property you're using -- `buildProfile` in this case:

Output of **`gradle greeting`**

    
    
    > gradle greeting
    foobar

Output of **`gradle -PbuildProfile=test greeting`**

    
    
    > gradle -PbuildProfile=test greeting
    testing 1 2 3

You're not limited to checking project properties. You could also check
environment variables, the JDK version, the OS the build is running on, or
anything else you can imagine.

One thing to bear in mind is that high level condition statements make builds
harder to understand and maintain, similar to the way they complicate object-
oriented code. The same applies to profiles. Gradle offers you many better
ways to avoid the extensive use of profiles that Maven often requires, for
example by configuring multiple tasks that are variants of one another. See
the `publish _PubName_ PublicationTo _RepoName_ Repository` tasks created by
the [Maven Publish Plugin](publishing_maven.html#publishing_maven:tasks).

For a lengthier discussion on working with Maven profiles in Gradle, look no
further than [this blog post](https://blog.gradle.org/maven-pom-profiles).

## Filtering resources

Maven has a phase called `process-resources` that has the goal
`resources:resources` bound to it by default. This gives the build author an
opportunity to perform variable substitution on various files, such as web
resources, packaged properties files, etc.

The Java plugin for Gradle provides a `processResources` task to do the same
thing. This is a [Copy](../dsl/org.gradle.api.tasks.Copy.html) task that
copies files from the configured resources directory -- `src/main/resources`
by default -- to an output directory. And as with any `Copy` task, you can
configure it to perform [file
filtering](working_with_files.html#filtering_files),
[renaming](working_with_files.html#sec:renaming_files), and [content
filtering](working_with_files.html#sec:filtering_files).

As an example, here's a configuration that treats the source files as [Groovy
`SimpleTemplateEngine`](https://docs.groovy-
lang.org/docs/next/html/documentation/template-
engines.html#_simpletemplateengine) templates, providing `version` and
`buildNumber` properties to those templates:

Example 7. Filtering the content of resources via the `processResources` task

build.gradle

    
    
    processResources {
        expand(version: version, buildNumber: currentBuildNumber)
    }

build.gradle.kts

    
    
    tasks {
        processResources {
            expand("version" to version, "buildNumber" to currentBuildNumber)
        }
    }

See the API docs for [CopySpec](../javadoc/org/gradle/api/file/CopySpec.html)
to see all the options available to you.

## Configuring integration tests

Many Maven builds incorporate integration tests of some sort, which Maven
supports through an extra set of phases: `pre-integration-test`, `integration-
test`, `post-integration-test`, and `verify`. It also uses the Failsafe plugin
in place of Surefire so that failed integration tests don't automatically fail
the build (because you may need to clean up resources, such as a running
application server).

This behavior is easy to replicate in Gradle with source sets, as explained in
our chapter on [Testing in Java & JVM
projects](java_testing.html#sec:configuring_java_integration_tests). You can
then configure a clean-up task, such as one that shuts down a test server for
example, to always run after the integration tests regardless of whether they
succeed or fail using
[Task.finalizedBy()](../dsl/org.gradle.api.Task.html#org.gradle.api.Task:finalizedBy\(java.lang.Object\[\]\)).

If you really don't want your integration tests to fail the build, then you
can use the
[Test.ignoreFailures](../dsl/org.gradle.api.tasks.testing.Test.html#org.gradle.api.tasks.testing.Test:ignoreFailures)
setting described in the [Test
execution](java_testing.html#sec:test_execution) section of the Java testing
chapter.

Source sets also give you a lot of flexibility on where you place the source
files for your integration tests. You can easily keep them in the same
directory as the unit tests or, more preferably, in a separate source
directory like `src/integTest/java`. To support other types of tests, you just
add more source sets and [Test](../dsl/org.gradle.api.tasks.testing.Test.html)
tasks!

## Migrating common plugins

Maven and Gradle share a common approach of extending the build through
plugins. Although the plugin systems are very different beneath the surface,
they share many feature-based plugins, such as:

  * Shade/Shadow

  * Jetty

  * Checkstyle

  * JaCoCo

  * AntRun (see further down)

Why does this matter? Because many plugins rely on standard Java conventions,
so migration is just a matter of replicating the configuration of the Maven
plugin in Gradle. As an example, here's a simple Maven Checkstyle plugin
configuration:

    
    
    ...
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-checkstyle-plugin</artifactId>
      <version>2.17</version>
      <executions>
        <execution>
          <id>validate</id>
          <phase>validate</phase>
          <configuration>
            <configLocation>checkstyle.xml</configLocation>
            <encoding>UTF-8</encoding>
            <consoleOutput>true</consoleOutput>
            <failsOnError>true</failsOnError>
            <linkXRef>false</linkXRef>
          </configuration>
          <goals>
            <goal>check</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
    ...

Everything outside of the configuration block can safely be ignored when
migrating to Gradle. In this case, the corresponding Gradle configuration
looks like the following:

Example 8. Configuring the Gradle Checkstyle Plugin

build.gradle

    
    
    checkstyle {
        config = resources.text.fromFile('checkstyle.xml', 'UTF-8')
        showViolations = true
        ignoreFailures = false
    }

build.gradle.kts

    
    
    checkstyle {
        config = resources.text.fromFile("checkstyle.xml", "UTF-8")
        isShowViolations = true
        isIgnoreFailures = false
    }

The Checkstyle tasks are automatically added as dependencies of the `check`
task, which also includes `test`. If you want to ensure that Checkstyle runs
before the tests, then just specify an ordering with the mustRunAfter()
method:

Example 9. Controlling when the `checkstyle` task runs

build.gradle

    
    
    test.mustRunAfter checkstyleMain, checkstyleTest

build.gradle.kts

    
    
    tasks {
        test {
            mustRunAfter(checkstyleMain, checkstyleTest)
        }
    }

As you can see, the Gradle configuration is often much shorter than the Maven
equivalent. You also have a much more flexible execution model since you are
no longer constrained by Maven's fixed phases.

While migrating a project from Maven, don't forget about source sets. These
often provide a more elegant solution for handling integration tests or
generated sources than Maven can provide, so you should factor them into your
migration plans.

### Ant goals

Many Maven builds rely on the AntRun plugin to customize the build without the
overhead of implementing a custom Maven plugin. Gradle has no equivalent
plugin because Ant is a first-class citizen in Gradle builds, via the `ant`
object. For example, you can use Ant's Echo task like this:

Example 10. Invoking Ant tasks

build.gradle

    
    
    task sayHello {
        doLast {
            ant.echo message: 'Hello!'
        }
    }

build.gradle.kts

    
    
    tasks.register("sayHello") {
        doLast {
            ant.withGroovyBuilder {
                "echo"("message" to "Hello!")
            }
        }
    }

Even Ant properties and filesets are supported natively. To learn more, see
[Using Ant from Gradle](ant.html#ant).

__ |

It may be simpler and cleaner to just [create custom task
types](custom_tasks.html#custom_tasks) to replace the work that Ant is doing
for you. You can then more readily benefit from [incremental
build](more_about_tasks.html#sec:up_to_date_checks) and other useful Gradle
features.  
  
---|---  
  
## Understanding which plugins you don't need

It's worth remembering that Gradle builds are typically easier to extend and
customize than Maven ones. In this context, that means you may not need a
Gradle plugin to replace a Maven one. For example, the Maven Enforcer plugin
allows you to control dependency versions and environmental factors, but these
things can easily be configured in a normal Gradle build script.

## Dealing with uncommon and custom plugins

You may come across Maven plugins that have no counterpart in Gradle,
particularly if you or someone in your organisation has written a custom
plugin. Such cases rely on you understanding how Gradle (and potentially
Maven) works, because you will usually have to write your own plugin.

For the purposes of migration, there are two key types of Maven plugins:

  * Those that use the Maven project object.

  * Those that don't.

Why is this important? Because if you use one of the latter, you can trivially
reimplement it as a [custom Gradle task type](custom_tasks.html#custom_tasks).
Simply define task inputs and outputs that correspond to the mojo parameters
and convert the execution logic into a task action.

If a plugin depends on the Maven project, then you will have to rewrite it.
Don't start by considering how the Maven plugin works, but look at what
problem it is trying to solve. Then try to work out how to solve that problem
in Gradle. You'll probably find that the two build models are different enough
that "transcribing" Maven plugin code into a Gradle plugin just won't be
effective. On the plus side, the plugin is likely to be much easier to write
than the original Maven one because Gradle has a much richer build model and
API.

If you do need to implement custom logic, either via build scripts or plugins,
check out the [Guides related to plugin
development](https://gradle.org/guides/?q=Plugin%20Development). Also be sure
to familiarize yourself with Gradle's [Groovy DSL Reference](../dsl/), which
provides comprehensive documentation on the API that you'll be working with.
It details the standard configuration blocks (and the objects that back them),
the core types in the system (`Project`, `Task`, etc.), and the standard set
of task types. The main entry point is the
[Project](../dsl/org.gradle.api.Project.html) interface as that's the top-
level object that backs the build scripts.

## Further reading

This chapter has covered the major topics that are specific to migrating Maven
builds to Gradle. All that remain are a few other areas that may be useful
during or after a migration:

  * Learn how to configure Gradle's [build environment](build_environment.html#build_environment), including the JVM settings used to run it

  * Learn how to [structure your builds effectively](organizing_gradle_projects.html#organizing_gradle_projects)

  * [Configure Gradle's logging](logging.html#logging) and use it from your builds

As a final note, this guide has only touched on a few of Gradle's features and
we encourage you to learn about the rest from the other chapters of the user
manual and from our [step-by-step samples](../samples/index.html).

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

