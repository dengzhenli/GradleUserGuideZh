

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

# Gradle Plugin Reference

version 6.7.1

Contents

  * JVM languages and frameworks
  * Native languages
  * Packaging and distribution
  * Code analysis
  * IDE integration
  * Utility

This page contains links and short descriptions for all the core plugins
provided by Gradle itself.

## JVM languages and frameworks

[Java](java_plugin.html)

    

Provides support for building any type of Java project.

[Java Library](java_library_plugin.html)

    

Provides support for building a Java library.

[Java Platform](java_platform_plugin.html)

    

Provides support for building a Java platform.

[Groovy](groovy_plugin.html)

    

Provides support for building any type of [Groovy](https://groovy-lang.org/)
project.

[Scala](scala_plugin.html)

    

Provides support for building any type of [Scala](https://www.scala-lang.org/)
project.

[ANTLR](antlr_plugin.html)

    

Provides support for generating parsers using [ANTLR](http://www.antlr.org/).

## Native languages

[C++ Application](cpp_application_plugin.html)

    

Provides support for building C++ applications on Windows, Linux, and macOS.

[C++ Library](cpp_library_plugin.html)

    

Provides support for building C++ libraries on Windows, Linux, and macOS.

[C++ Unit Test](cpp_unit_test_plugin.html)

    

Provides support for building and running C++ executable-based tests on
Windows, Linux, and macOS.

[Swift Application](swift_application_plugin.html)

    

Provides support for building Swift applications on Linux and macOS.

[Swift Library](swift_library_plugin.html)

    

Provides support for building Swift libraries on Linux and macOS.

[XCTest](xctest_plugin.html)

    

Provides support for building and running XCTest-based tests on Linux and
macOS.

## Packaging and distribution

[Application](application_plugin.html)

    

Provides support for building JVM-based, runnable applications.

[WAR](war_plugin.html)

    

Provides support for building and packaging WAR-based Java web applications.

[EAR](ear_plugin.html)

    

Provides support for building and packaging Java EE applications.

[Maven Publish](publishing_maven.html)

    

Provides support for [publishing artifacts](publishing_setup.html) to Maven-
compatible repositories.

[Ivy Publish](publishing_ivy.html)

    

Provides support for [publishing artifacts](publishing_setup.html) to Ivy-
compatible repositories.

[Legacy Maven Plugin](maven_plugin.html)

    

Provides support for publishing artifacts using the [legacy
mechanism](artifact_management.html) to Maven-compatible repositories.

[Distribution](distribution_plugin.html)

    

Makes it easy to create ZIP and tarball distributions of your project.

[Java Library Distribution](java_library_distribution_plugin.html)

    

Provides support for creating a ZIP distribution of a Java library project
that includes its runtime dependencies.

## Code analysis

[Checkstyle](checkstyle_plugin.html)

    

Performs quality checks on your project’s Java source files using
[Checkstyle](https://checkstyle.org/index.html) and generates associated
reports.

[PMD](pmd_plugin.html)

    

Performs quality checks on your project’s Java source files using
[PMD](http://pmd.github.io/) and generates associated reports.

[JaCoCo](jacoco_plugin.html)

    

Provides code coverage metrics for your Java project using
[JaCoCo](http://www.eclemma.org/jacoco/).

[CodeNarc](codenarc_plugin.html)

    

Performs quality checks on your Groovy source files using
[CodeNarc](http://codenarc.sourceforge.net/index.html) and generates
associated reports.

## IDE integration

[Eclipse](eclipse_plugin.html)

    

Generates Eclipse project files for the build that can be opened by the IDE.
This set of plugins can also be used to fine tune
[Buildship's](http://projects.eclipse.org/projects/tools.buildship) import
process for Gradle builds.

[IntelliJ IDEA](idea_plugin.html)

    

Generates IDEA project files for the build that can be opened by the IDE. It
can also be used to fine tune IDEA's import process for Gradle builds.

[Visual Studio](visual_studio_plugin.html)

    

Generates Visual Studio solution and project files for build that can be
opened by the IDE.

[Xcode](xcode_plugin.html)

    

Generates Xcode workspace and project files for the build that can be opened
by the IDE.

## Utility

[Base](base_plugin.html)

    

Provides common lifecycle tasks, such as `clean`, and other features common to
most builds.

[Build Init](build_init_plugin.html)

    

Generates a new Gradle build of a specified type, such as a Java library. It
can also generate a build script from a Maven POM — see [Migrating from Maven
to Gradle](migrating_from_maven.html) for more details.

[Signing](signing_plugin.html)

    

Provides support for digitally signing generated files and artifacts.

[Plugin Development](java_gradle_plugin.html)

    

Makes it easier to develop and publish a Gradle plugin.

[Project Report Plugin](project_report_plugin.html)

    

Helps to generate reports containing useful information about your build.

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

