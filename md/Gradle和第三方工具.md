

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

# Gradle & Third-party Tools

version 6.7.1

Contents

  * IDEs
  * Continuous integration
  * How to integrate with Gradle
  * Embedding Gradle using the Tooling API

Gradle can be integrated with many different third-party tools such as IDEs
and continuous integration platforms. Here we look at some of the more common
ones as well as how to integrate your own tool with Gradle.

## IDEs

Android Studio

    

As a variant of IntelliJ IDEA, [Android
Studio](https://developer.android.com/studio/) has built-in support for
importing and building Gradle projects. You can also use the [IDEA Plugin for
Gradle](idea_plugin.html) to fine-tune the import process if that's necessary.

This IDE also has an [extensive user
guide](https://developer.android.com/studio/intro/) to help you get the most
out of the IDE and Gradle.

Eclipse

    

If you want to work on a project within Eclipse that has a Gradle build, you
should use the [Eclipse Buildship
plugin](https://projects.eclipse.org/projects/tools.buildship). This will
allow you to import and run Gradle builds. If you need to fine tune the import
process so that the project loads correctly, you can use the [Eclipse Plugins
for Gradle](eclipse_plugin.html). See [the associated release
announcement](https://discuss.gradle.org/t/buildship-1-0-18-is-now-
available/19012) for details on what fine tuning you can do.

IntelliJ IDEA

    

IDEA has built-in support for importing Gradle projects. If you need to fine
tune the import process so that the project loads correctly, you can use the
[IDEA Plugin for Gradle](idea_plugin.html).

NetBeans

    

Add the [Gradle Support](http://plugins.netbeans.org/plugin/44510/gradle-
support) plugin to NetBeans in order to import and run projects with Gradle
builds.

Visual Studio

    

For developing C++ projects, Gradle comes with a [Visual Studio
plugin](visual_studio_plugin.html).

Xcode

    

For developing C++ projects, Gradle comes with a [Xcode
plugin](xcode_plugin.html).

CLion

    

JetBrains supports building [C++ projects with
Gradle](https://blog.jetbrains.com/clion/2018/05/clion-starts-2018-2-eap-
sanitizers-gradle-db-performance/).

## Continuous integration

We have dedicated guides showing you how to integrate a Gradle project with
the following CI platforms:

  * [Jenkins](https://guides.gradle.org/executing-gradle-builds-on-jenkins)

  * [TeamCity](https://guides.gradle.org/executing-gradle-builds-on-teamcity)

  * [Travis CI](https://guides.gradle.org/executing-gradle-builds-on-travisci)

Even if you don't use one of the above, you can almost certainly configure
your CI platform to use the [Gradle Wrapper](gradle_wrapper.html) scripts.

## How to integrate with Gradle

There are two main ways to integrate a tool with Gradle:

  * The Gradle build uses the tool

  * The tool executes the Gradle build

The former case is typically [implemented as a Gradle
plugin](custom_plugins.html). The latter can be accomplished by embedding
Gradle through the Tooling API as described below.

## Embedding Gradle using the Tooling API

### Introduction to the Tooling API

Gradle provides a programmatic API called the Tooling API, which you can use
for embedding Gradle into your own software. This API allows you to execute
and monitor builds and to query Gradle about the details of a build. The main
audience for this API is IDE, CI server, other UI authors; however, the API is
open for anyone who needs to embed Gradle in their application.

  * [Gradle TestKit](test_kit.html#test_kit) uses the Tooling API for functional testing of your Gradle plugins.

  * [Eclipse Buildship](http://projects.eclipse.org/projects/tools.buildship) uses the Tooling API for importing your Gradle project and running tasks.

  * [IntelliJ IDEA](https://www.jetbrains.com/idea/) uses the Tooling API for importing your Gradle project and running tasks.

### Tooling API Features

A fundamental characteristic of the Tooling API is that it operates in a
version independent way. This means that you can use the same API to work with
builds that use different versions of Gradle, including versions that are
newer or older than the version of the Tooling API that you are using. The
Tooling API is Gradle wrapper aware and, by default, uses the same Gradle
version as that used by the wrapper-powered build.

Some features that the Tooling API provides:

  * Query the details of a build, including the project hierarchy and the project dependencies, external dependencies (including source and Javadoc jars), source directories and tasks of each project.

  * Execute a build and listen to stdout and stderr logging and progress messages (e.g. the messages shown in the 'status bar' when you run on the command line).

  * Execute a specific test class or test method.

  * Receive interesting events as a build executes, such as project configuration, task execution or test execution.

  * Cancel a build that is running.

  * Combine multiple separate Gradle builds into a single composite build.

  * The Tooling API can download and install the appropriate Gradle version, similar to the wrapper.

  * The implementation is lightweight, with only a small number of dependencies. It is also a well-behaved library, and makes no assumptions about your classloader structure or logging configuration. This makes the API easy to embed in your application.

### Tooling API and the Gradle Build Daemon

The Tooling API always uses the Gradle daemon. This means that subsequent
calls to the Tooling API, be it model building requests or task executing
requests will be executed in the same long-living process. [Gradle
Daemon](gradle_daemon.html#gradle_daemon) contains more details about the
daemon, specifically information on situations when new daemons are forked.

### Quickstart

As the Tooling API is an interface for developers, the Javadoc is the main
documentation for it.

To use the Tooling API, add the following repository and dependency
declarations to your build script:

Example 1. Using the tooling API

build.gradle

    
    
    repositories {
        maven { url 'https://repo.gradle.org/gradle/libs-releases' }
    }
    
    dependencies {
        implementation "org.gradle:gradle-tooling-api:$toolingApiVersion"
        // The tooling API need an SLF4J implementation available at runtime, replace this with any other implementation
        runtimeOnly 'org.slf4j:slf4j-simple:1.7.10'
    }

build.gradle.kts

    
    
    repositories {
        maven { url = uri("https://repo.gradle.org/gradle/libs-releases") }
    }
    
    dependencies {
        implementation("org.gradle:gradle-tooling-api:$toolingApiVersion")
        // The tooling API need an SLF4J implementation available at runtime, replace this with any other implementation
        runtimeOnly("org.slf4j:slf4j-simple:1.7.10")
    }

The main entry point to the Tooling API is the
[GradleConnector](../javadoc/org/gradle/tooling/GradleConnector.html). You can
navigate from there to find code samples and explore the available Tooling API
models. You can use
[GradleConnector.connect()](https://docs.gradle.org/nightly/javadoc/org/gradle/tooling/GradleConnector.html#connect--)
to create a
[ProjectConnection](../javadoc/org/gradle/tooling/ProjectConnection.html). A
`ProjectConnection` connects to a single Gradle project. Using the connection
you can execute tasks, tests and retrieve models relative to this project.

### Compatibility of Java and Gradle versions

The Tooling API requires Java 8 or later. The Gradle version used by builds
may impose [additional Java version requirements](compatibility.html).

The Tooling API supports running builds using Gradle 2.6 and later. Gradle 5.0
and up require clients to use Tooling API version 3.0 or later.

You should note that not all features of the Tooling API are available for all
versions of Gradle. Refer to the documentation for each class and method for
more details.

In general, the Tooling API client can run on a different version of Java than
the build, but classes that are sent to the build via custom build actions
need to be targeted to the lowest supported Java version.

  * Gradle 2.6-2.14.1 only supports Java 6 through Java 8.

  * Gradle 3.x and 4.x require a minimum version of Java 7.

  * Gradle 5 and above require a minimum version of Java 8.

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

