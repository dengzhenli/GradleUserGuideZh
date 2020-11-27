

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

# Installing Gradle

version 6.7.1

Contents

  * Prerequisites
  * Installing with a package manager
  * Installing manually
  * Verifying installation
  * Next steps

You can install the Gradle build tool on Linux, macOS, or Windows. This
document covers installing using a package manager like SDKMAN! or Homebrew,
as well as manual installation.

Use of the [Gradle Wrapper](gradle_wrapper.html#sec:upgrading_wrapper) is the
recommended way to upgrade Gradle.

You can find all releases and their checksums on the [releases
page](https://gradle.org/releases).

## Prerequisites

Gradle runs on all major operating systems and requires only a [Java
Development Kit](https://jdk.java.net/) version 8 or higher to run. To check,
run `java -version`. You should see something like this:

    
    
    ❯ java -version
    java version "1.8.0_151"
    Java(TM) SE Runtime Environment (build 1.8.0_151-b12)
    Java HotSpot(TM) 64-Bit Server VM (build 25.151-b12, mixed mode)

Gradle ships with its own Groovy library, therefore Groovy does not need to be
installed. Any existing Groovy installation is ignored by Gradle.

Gradle uses whatever JDK it finds in your path. Alternatively, you can set the
`JAVA_HOME` environment variable to point to the installation directory of the
desired JDK.

[See the full compatibility notes for Java, Groovy, Kotlin and
Android.](compatibility.html#compatibility)

## Installing with a package manager

[SDKMAN!](http://sdkman.io) is a tool for managing parallel versions of
multiple Software Development Kits on most Unix-like systems (macOS, Linux,
Cygwin, Solaris and FreeBSD). We deploy and maintain the versions available
from SDKMAN!.

    
    
    ❯ sdk install gradle

[Homebrew](http://brew.sh) is "the missing package manager for macOS".

    
    
    ❯ brew install gradle

Other package managers are available, but the version of Gradle distributed by
them is not controlled by Gradle, Inc. Linux package managers may distribute a
modified version of Gradle that is incompatible or incomplete when compared to
the official version (available from SDKMAN! or below).

↓ Proceed to next steps

## Installing manually

### Step 1. [Download](https://gradle.org/releases) the latest Gradle
distribution

The distribution ZIP file comes in two flavors:

  * Binary-only (bin)

  * Complete (all) with docs and sources

Need to work with an older version? See the [releases
page](https://gradle.org/releases).

### Step 2. Unpack the distribution

#### Linux & MacOS users

Unzip the distribution zip file in the directory of your choosing, e.g.:

    
    
    ❯ mkdir /opt/gradle
    ❯ unzip -d /opt/gradle gradle-6.7.1-bin.zip
    ❯ ls /opt/gradle/gradle-6.7.1
    LICENSE  NOTICE  bin  README  init.d  lib  media

#### Microsoft Windows users

Create a new directory `C:\Gradle` with **File Explorer**.

Open a second **File Explorer** window and go to the directory where the
Gradle distribution was downloaded. Double-click the ZIP archive to expose the
content. Drag the content folder `gradle-6.7.1` to your newly created
`C:\Gradle` folder.

Alternatively, you can unpack the Gradle distribution ZIP into `C:\Gradle`
using an archiver tool of your choice.

### Step 3. Configure your system environment

To run Gradle, the path to the unpacked files from the Gradle website need to
be on your terminal's path. The steps to do this are different for each
operating system.

#### Linux & MacOS users

Configure your `PATH` environment variable to include the `bin` directory of
the unzipped distribution, e.g.:

    
    
    ❯ export PATH=$PATH:/opt/gradle/gradle-6.7.1/bin

Alternatively, you could also add the environment variable `GRADLE_HOME` and
point this to the unzipped distribution. Instead of adding a specific version
of Gradle to your `PATH`, you can add `_$GRADLE_HOME_ /bin` to your `PATH`.
When upgrading to a different version of Gradle, just change the `GRADLE_HOME`
environment variable.

#### Microsoft Windows users

In **File Explorer** right-click on the `This PC` (or `Computer`) icon, then
click `Properties` -> `Advanced System Settings` -> `Environmental Variables`.

Under `System Variables` select `Path`, then click `Edit`. Add an entry for
`C:\Gradle\gradle-6.7.1\bin`. Click OK to save.

Alternatively, you could also add the environment variable `GRADLE_HOME` and
point this to the unzipped distribution. Instead of adding a specific version
of Gradle to your `Path`, you can add `_%GRADLE_HOME%_ /bin` to your `Path`.
When upgrading to a different version of Gradle, just change the `GRADLE_HOME`
environment variable.

↓ Proceed to next steps

## Verifying installation

Open a console (or a Windows command prompt) and run `gradle -v` to run gradle
and display the version, e.g.:

    
    
    ❯ gradle -v
    
    ------------------------------------------------------------
    Gradle 6.7.1
    ------------------------------------------------------------
    
    (environment specific information)

If you run into any trouble, see the [section on troubleshooting
installation](troubleshooting.html#sec:troubleshooting_installation).

You can verify the integrity of the Gradle distribution by downloading the
SHA-256 file (available from the [releases page](https://gradle.org/releases))
and following these [verification
instructions](gradle_wrapper.html#sec:verification).

## Next steps

Now that you have Gradle installed, use these resources for getting started:

  * Create your first Gradle project by following one of our [step-by-step samples](../samples/index.html).

  * Sign up for a [live introductory Gradle training](https://gradle.org/training/intro-to-gradle/) with a core engineer.

  * Learn how to achieve common tasks through the [command-line interface](command_line_interface.html#command_line_interface).

  * [Configure Gradle execution](build_environment.html#build_environment), such as use of an HTTP proxy for downloading dependencies.

  * Subscribe to the [Gradle Newsletter](https://newsletter.gradle.com/) for monthly release and community updates.

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

