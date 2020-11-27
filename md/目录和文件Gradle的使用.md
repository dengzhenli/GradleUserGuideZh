

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

# The Directories and Files Gradle Uses

version 6.7.1

Contents

  * Gradle user home directory
  * Project root directory

Gradle uses two main directories to perform and manage its work: the Gradle
user home directory and the Project root directory. The following two sections
describe what is stored in each of them and how transient files and
directories are cleaned up.

## Gradle user home directory

The Gradle user home directory (`$USER_HOME/.gradle` by default) is used to
store global configuration properties and initialization scripts as well as
caches and log files. It is roughly structured as follows:

    
    
    ├── caches __**(1)**
    │   ├── 4.8 __**(2)**
    │   ├── 4.9 __**(2)**
    │   ├── ⋮
    │   ├── jars-3 __**(3)**
    │   └── modules-2 __**(3)**
    ├── daemon __**(4)**
    │   ├── ⋮
    │   ├── 4.8
    │   └── 4.9
    ├── init.d __**(5)**
    │   └── my-setup.gradle
    ├── jdks __**(6)**
    │   ├── ⋮
    │   └── jdk-14.0.2+12
    ├── wrapper
    │   └── dists __**(7)**
    │       ├── ⋮
    │       ├── gradle-4.8-bin
    │       ├── gradle-4.9-all
    │       └── gradle-4.9-bin
    └── gradle.properties __**(8)**

__**1** | Global cache directory (for everything that's not project-specific)  
---|---  
__**2** | Version-specific caches (e.g. to support incremental builds)  
__**3** | Shared caches (e.g. for artifacts of dependencies)  
__**4** | Registry and logs of the [Gradle
Daemon](gradle_daemon.html#gradle_daemon)  
__**5** | Global [initialization scripts](init_scripts.html#init_scripts)  
__**6** | JDKs downloaded by the [toolchain
support](toolchains.html#sec:provisioning)  
__**7** | Distributions downloaded by the [Gradle
Wrapper](gradle_wrapper.html#gradle_wrapper)  
__**8** | Global [Gradle configuration
properties](build_environment.html#sec:gradle_configuration_properties)  
  
### Cleanup of caches and distributions

From version 4.10 onwards, Gradle automatically cleans its user home
directory. The cleanup runs in the background when the Gradle daemon is
stopped or shuts down. If using `--no-daemon`, it runs in the foreground after
the build session with a visual progress indicator.

The following cleanup strategies are applied periodically (at most every 24
hours):

  * Version-specific caches in `caches/<gradle-version>/` are checked for whether they are still in use.If not, directories for release versions are deleted after 30 days of inactivity, snapshot versions after 7 days of inactivity.

  * Shared caches in `caches/` (e.g. `jars-*`) are checked for whether they are still in use.If there's no Gradle version that still uses them, they are deleted.

  * Files in shared caches used by the current Gradle version in `caches/` (e.g. `jars-3` or `modules-2`) are checked for when they were last accessed.Depending on whether the file can be recreated locally or would have to be downloaded from a remote repository again, it will be deleted after 7 or 30 days of not being accessed, respectively.

  * Gradle distributions in `wrapper/dists/` are checked for whether they are still in use, i.e. whether there's a corresponding version-specific cache directory.Unused distributions are deleted.

## Project root directory

The project root directory contains all source files that are part of your
project. In addition, it contains files and directories that are generated by
Gradle such as `.gradle` and `build`. While the former are usually checked in
to source control, the latter are transient files used by Gradle to support
features like incremental builds. Overall, the anatomy of a typical project
root directory looks roughly as follows:

    
    
    ├── .gradle __**(1)**
    │   ├── 4.8 __**(2)**
    │   ├── 4.9 __**(2)**
    │   └── ⋮
    ├── build __**(3)**
    ├── gradle
    │   └── wrapper __**(4)**
    ├── gradle.properties __**(5)**
    ├── gradlew __**(6)**
    ├── gradlew.bat __**(6)**
    ├── settings.gradle _or_ settings.gradle.kts __**(7)**
    ├── subproject-one __**(8)**
    |   └── build.gradle _or_ build.gradle.kts __**(9)**
    ├── subproject-two __**(8)**
    |   └── build.gradle _or_ build.gradle.kts __**(9)**
    └── ⋮

__**1** | Project-specific cache directory generated by Gradle  
---|---  
__**2** | Version-specific caches (e.g. to support incremental builds)  
__**3** | The build directory of this project into which Gradle generates all
build artifacts.  
__**4** | Contains the JAR file and configuration of the [Gradle
Wrapper](gradle_wrapper.html#gradle_wrapper)  
__**5** | Project-specific [Gradle configuration
properties](build_environment.html#sec:gradle_configuration_properties)  
__**6** | Scripts for executing builds using the [Gradle
Wrapper](gradle_wrapper.html#gradle_wrapper)  
__**7** | The project's [settings
file](build_lifecycle.html#sec:settings_file) where the list of subprojects is
defined  
__**8** | Usually a project is organized into one or multiple subprojects  
__**9** | Each subproject has its own Gradle build script  
  
### Project cache cleanup

From version 4.10 onwards, Gradle automatically cleans the project-specific
cache directory. After building the project, version-specific cache
directories in `.gradle/<gradle-version>/` are checked periodically (at most
every 24 hours) for whether they are still in use. They are deleted if they
haven't been used for 7 days.

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

