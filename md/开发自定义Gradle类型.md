

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

# Developing Custom Gradle Types

version 6.7.1

Contents

  * Configuration using bean properties
  * DSL support and extensibility
  * Service injection
  * Creating nested objects
  * Collection types

There are several different kinds of "add-ons" to Gradle that you can develop,
such as [plugins](custom_plugins.html#custom_plugins),
[tasks](custom_tasks.html#custom_tasks), [project
extensions](custom_plugins.html#sec:getting_input_from_the_build) or [artifact
transforms](artifact_transforms.html#sec:implementing-artifact-transforms),
that are all implemented as classes and other types that can run on the JVM.
This chapter discusses some of the features and concepts that are common to
these types. You can use these features to help implement custom Gradle types
and provide a consistent DSL for your users.

This chapter applies to the following types:

  * Plugin types.

  * Task types.

  * Artifact transform parameters types.

  * Worker API work action parameters types.

  * Extension objects created using `ExtensionContainer.create()`, for example a project extension registered by a plugin.

  * Objects created using `ObjectFactory.newInstance()`.

  * Objects created for a managed nested property.

  * Elements of a `NamedDomainObjectContainer`.

## Configuration using bean properties

The custom Gradle types that you implement often hold some configuration that
you want to make available to build scripts and other plugins. For example, a
download task may have configuration that specifies the URL to download from
and the file system location to write the result to. This configuration is
represented as Java bean properties.

Kotlin and Groovy provide conveniences for declaring Java bean properties,
which make them good language choices to use to implement Gradle types. These
conveniences are demonstrated in the samples below.

Gradle also provides some conveniences for implementing types with bean
properties.

### Managed properties

Gradle can provide an implementation of an abstract property. This is called a
_managed property_ , as Gradle takes care of managing the state of the
property. A property may be _mutable_ , meaning that it has both a getter
method and setter method, or _read-only_ , meaning that it has only a getter
method.

__ |

Managed properties are currently an
[incubating](feature_lifecycle.html#feature_lifecycle) feature.  
  
---|---  
  
#### Mutable managed properties

To declare a mutable managed property, add an abstract getter method and an
abstract setter method for the property to the type.

Here is an example of a task type with a `uri` property:

Example 1. Mutable managed property

Download.java

    
    
    import org.gradle.api.DefaultTask;
    import org.gradle.api.tasks.Input;
    import org.gradle.api.tasks.TaskAction;
    
    import java.net.URI;
    
    public abstract class Download extends DefaultTask {
        // Use an abstract getter and setter method
        @Input
        abstract URI getUri();
        abstract void setUri(URI uri);
    
        @TaskAction
        void run() {
            // Use the `uri` property
            System.out.println("Downloading " + getUri());
        }
    }

Note that for a property to be considered a mutable managed property, _all_ of
the property's getter methods and setter methods must be `abstract` and have
`public` or `protected` visibility.

#### Read-only managed properties

To declare a read-only managed property, add an abstract getter method for the
property to the type. The property should not have any setter methods. Gradle
will provide an implementation of the getter and also create a value for the
property.

This is a useful pattern to use with one of Gradle's configurable [lazy
property](lazy_configuration.html#lazy_configuration) or container types.

Here is an example of a task type with a `uri` property:

Example 2. Read-only managed property

Download.java

    
    
    import org.gradle.api.DefaultTask;
    import org.gradle.api.provider.Property;
    import org.gradle.api.tasks.Input;
    import org.gradle.api.tasks.TaskAction;
    
    import java.net.URI;
    
    public abstract class Download extends DefaultTask {
        // Use an abstract getter method
        @Input
        abstract Property<URI> getUri();
    
        @TaskAction
        void run() {
            // Use the `uri` property
            System.out.println("Downloading " + getUri().get());
        }
    }

Note that for a property to be considered a read only managed property, _all_
of the property's getter methods must be `abstract` and have `public` or
`protected` visibility. The property must not have any setter methods. In
addition, the property type must have one of the following:

  * `Property<T>`

  * `RegularFileProperty`

  * `DirectoryProperty`

  * `ListProperty<T>`

  * `SetProperty<T>`

  * `MapProperty<K, V>`

  * `ConfigurableFileCollection`

  * `ConfigurableFileTree`

  * `DomainObjectSet<T>`

  * `NamedDomainObjectContainer<T>`

Gradle creates values for read-only managed properties in the same way as
[ObjectFactory](../javadoc/org/gradle/api/model/ObjectFactory.html).

#### Read-only managed nested properties

To declare a read-only managed nested property, add an abstract getter method
for the property to the type annotated with
`@[Nested](../javadoc/org/gradle/api/tasks/Nested.html)`. The property should
not have any setter methods. Gradle provides an implementation for the getter
method, and also creates a value for the property. The nested type is also
treated as a custom type, and can use the features discussed in this chapter.

This pattern is useful when a custom type has a nested complex type which has
the same lifecycle. If the lifecycle is different, consider using
`Property<NestedType>` instead.

Here is an example of a task type with a `resource` property. The `Resource`
type is also a custom Gradle type and defines some managed properties:

Example 3. Read-only managed nested property

Download.java

    
    
    public abstract class Download extends DefaultTask {
        // Use an abstract getter method annotated with @Nested
        @Nested
        abstract Resource getResource();
    
        @TaskAction
        void run() {
            // Use the `resource` property
            System.out.println("Downloading https://" + getResource().getHostName().get() + "/" + getResource().getPath().get());
        }
    }
    
    public interface Resource {
        @Input
        Property<String> getHostName();
        @Input
        Property<String> getPath();
    }

Note that for a property to be considered a read only managed nested property,
_all_ of the property's getter methods must be `abstract` and have `public` or
`protected` visibility. The property must not have any setter methods. In
addition, the property getter must be annotated with
`@[Nested](../javadoc/org/gradle/api/tasks/Nested.html)`.

### Managed types

A _managed type_ is an abstract class or interface with no fields and whose
properties are all managed. That is, it is a type whose state is entirely
managed by Gradle.

## DSL support and extensibility

When Gradle creates an instance of a custom type, it _decorates_ the instance
to mix-in DSL and extensibility support.

Each decorated instance implements
[ExtensionAware](../javadoc/org/gradle/api/plugins/ExtensionAware.html), and
so can have extension objects attached to it.

Note that plugins and the elements of containers created using
[Project.container()](../javadoc/org/gradle/api/Project.html#container-
java.lang.Class-) are currently not decorated, due to backwards compatibility
issues.

## Service injection

Gradle provides a number of useful services that can be used by custom Gradle
types. For example, the
[WorkerExecutor](../javadoc/org/gradle/workers/WorkerExecutor.html) service
can be used by a task to run work in parallel, as seen in the [worker
API](custom_tasks.html#worker_api) section. The services are made available
through _service injection_.

### Available services

The following services are available for injection:

  * [ObjectFactory](../javadoc/org/gradle/api/model/ObjectFactory.html) \- Allows model objects to be created. See Creating nested objects for more details.

  * [ProjectLayout](../javadoc/org/gradle/api/file/ProjectLayout.html) \- Provides access to key project locations. See [lazy configuration](lazy_configuration.html#working_with_files_in_lazy_properties) for more details. This service is unavailable in Worker API actions.

  * [ProviderFactory](../javadoc/org/gradle/api/provider/ProviderFactory.html) \- Creates `Provider` instances. See [lazy configuration](lazy_configuration.html#lazy_configuration) for more details.

  * [WorkerExecutor](../javadoc/org/gradle/workers/WorkerExecutor.html) \- Allows a task to run work in parallel. See [the worker API](custom_tasks.html#worker_api) for more details.

  * [FileSystemOperations](../javadoc/org/gradle/api/file/FileSystemOperations.html) \- Allows a task to run operations on the filesystem such as deleting files, copying files or syncing directories.

  * [ArchiveOperations](../javadoc/org/gradle/api/file/ArchiveOperations.html) \- Allows a task to run operations on archive files such as ZIP or TAR files.

  * [ExecOperations](../javadoc/org/gradle/process/ExecOperations.html) \- Allows a task to run external processes with dedicated support for running external `java` programs.

  * [ToolingModelBuilderRegistry](../javadoc/org/gradle/tooling/provider/model/ToolingModelBuilderRegistry.html) \- Allows a plugin to registers a Gradle tooling API model.

Out of the above, `ProjectLayout` and `WorkerExecutor` services are only
available for injection in project plugins.

### Constructor injection

There are 2 ways that an object can receive the services that it needs. The
first option is to add the service as a parameter of the class constructor.
The constructor must be annotated with the `javax.inject.Inject` annotation.
Gradle uses the declared type of each constructor parameter to determine the
services that the object requires. The order of the constructor parameters and
their names are not significant and can be whatever you like.

Here is an example that shows a task type that receives an `ObjectFactory` via
its constructor:

Example 4. Constructor service injection

Download.java

    
    
    import org.gradle.api.DefaultTask;
    import org.gradle.api.file.DirectoryProperty;
    import org.gradle.api.model.ObjectFactory;
    import org.gradle.api.tasks.OutputDirectory;
    import org.gradle.api.tasks.TaskAction;
    
    import javax.inject.Inject;
    
    public class Download extends DefaultTask {
        private final DirectoryProperty outputDirectory;
    
        // Inject an ObjectFactory into the constructor
        @Inject
        public Download(ObjectFactory objectFactory) {
            // Use the factory
            outputDirectory = objectFactory.directoryProperty();
        }
    
        @OutputDirectory
        public DirectoryProperty getOutputDirectory() {
            return outputDirectory;
        }
    
        @TaskAction
        void run() {
            // ...
        }
    }

### Property injection

Alternatively, a service can be injected by adding a property getter method
annotated with the `javax.inject.Inject` annotation to the class. This can be
useful, for example, when you cannot change the constructor of the class due
to backwards compatibility constraints. This pattern also allows Gradle to
defer creation of the service until the getter method is called, rather than
when the instance is created. This can help with performance. Gradle uses the
declared return type of the getter method to determine the service to make
available. The name of the property is not significant and can be whatever you
like.

The property getter method must be `public` or `protected`. The method can be
`abstract` or, in cases where this isn't possible, can have a dummy method
body. The method body is discarded.

Here is an example that shows a task type that receives a two services via
property getter methods:

Example 5. Property service injection

Download.java

    
    
    import javax.inject.Inject;
    import org.gradle.api.model.ObjectFactory;
    import org.gradle.api.DefaultTask;
    import org.gradle.api.tasks.TaskAction;
    import org.gradle.workers.WorkerExecutor;
    
    public abstract class Download extends DefaultTask {
        // Use an abstract getter method
        @Inject
        protected abstract ObjectFactory getObjectFactory();
    
        // Alternatively, use a getter method with a dummy implementation
        @Inject
        protected WorkerExecutor getWorkerExecutor() {
            // Method body is ignored
            throw new UnsupportedOperationException();
        }
    
        @TaskAction
        void run() {
            WorkerExecutor workerExecutor = getWorkerExecutor();
            ObjectFactory objectFactory = getObjectFactory();
            // Use the executor and factory ...
        }
    }

## Creating nested objects

A custom Gradle type can use the
[ObjectFactory](../javadoc/org/gradle/api/model/ObjectFactory.html) service to
create instances of Gradle types to use for its property values. These
instances can make use of the features discussed in this chapter, allowing you
to create 'nested' object and a nested DSL.

You can also have Gradle create nested objects for you by using a managed
nested property.

In the following example, a project extension receives an `ObjectFactory`
instance through its constructor. The constructor uses this to create a nested
`Resource` object (also a custom Gradle type) and makes this object available
through the `resource` property.

Example 6. Nested object creation

DownloadExtension.java

    
    
    import org.gradle.api.model.ObjectFactory;
    
    import javax.inject.Inject;
    
    public class DownloadExtension {
        // A nested instance
        private final Resource resource;
    
        @Inject
        public DownloadExtension(ObjectFactory objectFactory) {
            // Use an injected ObjectFactory to create a Resource object
            resource = objectFactory.newInstance(Resource.class);
        }
    
        public Resource getResource() {
            return resource;
        }
    }
    
    public class Resource {
        private URI uri;
    
        public URI getUri() {
            return uri;
        }
        public void setUri(URI uri) {
            this.uri = uri;
        }
    }

## Collection types

Gradle provides types for maintaining collections of objects, intended to work
well with the Gradle DSL and provide useful features such as lazy
configuration.

### NamedDomainObjectContainer

A
[NamedDomainObjectContainer](../javadoc/org/gradle/api/NamedDomainObjectContainer.html)
manages a set of objects, where each element has a name associated with it.
The container takes care of creating and configuring the elements, and
provides a DSL that build scripts can use to define and configure elements. It
is intended to hold objects which are themselves configurable, for example a
set of custom Gradle objects.

Gradle uses `NamedDomainObjectContainer` type extensively throughout the API.
For example, the `project.tasks` object used to manage the tasks of a project
is a `NamedDomainObjectContainer<Task>`.

You can create a container instance using the
[ObjectFactory](../javadoc/org/gradle/api/model/ObjectFactory.html) service,
which provides the
[ObjectFactory.domainObjectContainer()](../javadoc/org/gradle/api/model/ObjectFactory.html#domainObjectContainer-
java.lang.Class-) method. This is also available using the
[Project.container()](../javadoc/org/gradle/api/Project.html#container-
java.lang.Class-) method, however in a custom Gradle type it's generally
better to use the injected `ObjectFactory` service instead of passing around a
`Project` instance.

You can also create a container instance using a read-only managed property,
described above.

In order to use a type with any of the `domainObjectContainer()` methods, it
must expose a property named "name" as the unique, and constant, name for the
object. The `domainObjectContainer(Class)` variant of the method creates new
instances by calling the constructor of the class that takes a string
argument, which is the desired name of the object. Objects created this way
are treated as custom Gradle types, and so can make use of the features
discussed in this chapter, for example service injection or managed
properties.

See the above link for `domainObjectContainer()` method variants that allow
custom instantiation strategies.

Example 7. Managing a collection of objects

DownloadExtension.java

    
    
    import org.gradle.api.NamedDomainObjectContainer;
    import org.gradle.api.model.ObjectFactory;
    
    import javax.inject.Inject;
    
    public class DownloadExtension {
        // A container of `Resource` objects
        private final NamedDomainObjectContainer<Resource> resources;
    
        @Inject
        public DownloadExtension(ObjectFactory objectFactory) {
            // Use an injected ObjectFactory to create a container
            resources = objectFactory.domainObjectContainer(Resource.class);
        }
    
        public NamedDomainObjectContainer<Resource> getResources() {
            return resources;
        }
    }
    
    public class Resource {
        private final String name;
        private URI uri;
        private String userName;
    
        // Type must have a public constructor that takes the element name as a parameter
        public Resource(String name) {
            this.name = name;
        }
    
        // Type must have a 'name' property, which should be read-only
        public String getName() {
            return name;
        }
    
        public URI getUri() {
            return uri;
        }
        public void setUri(URI uri) {
            this.uri = uri;
        }
    
        public String getUserName() {
            return userName;
        }
        public void setUserName(String userName) {
            this.userName = userName;
        }
    }

For each container property, Gradle automatically adds a block to the Groovy
and Kotlin DSL that you can use to configure the contents of the container:

Example 8. Configure block

build.gradle.kts

    
    
    plugins {
        id("org.gradle.sample.download")
    }
    
    download {
        // Can use a block to configure the container contents
        resources {
            register("gradle") {
                uri = uri("https://gradle.org")
            }
        }
    }

build.gradle

    
    
    plugins {
        id("org.gradle.sample.download")
    }
    
    download {
        // Can use a block to configure the container contents
        resources {
            gradle {
                uri = uri('https://gradle.org')
            }
        }
    }

### ExtensiblePolymorphicDomainObjectContainer

An
[ExtensiblePolymorphicDomainObjectContainer](../javadoc/org/gradle/api/ExtensiblePolymorphicDomainObjectContainer.html)
is a `NamedDomainObjectContainer` that allows you to define instantiation
strategies for different types of objects.

You can create an instance using the
[ObjectFactory.polymorphicDomainObjectContainer()](../javadoc/org/gradle/api/model/ObjectFactory.html#polymorphicDomainObjectContainer-
java.lang.Class-) method.

### NamedDomainObjectSet

A [NamedDomainObjectSet](../javadoc/org/gradle/api/NamedDomainObjectSet.html)
holds a set of configurable objects, where each element has a name associated
with it. This is similar to `NamedDomainObjectContainer`, however a
`NamedDomainObjectSet` doesn't manage the objects in the collection. They need
to be created and added manually.

You can create an instance using the
[ObjectFactory.namedDomainObjectSet()](../javadoc/org/gradle/api/model/ObjectFactory.html#namedDomainObjectSet-
java.lang.Class-) method.

### NamedDomainObjectList

A
[NamedDomainObjectList](../javadoc/org/gradle/api/NamedDomainObjectList.html)
holds a list of configurable objects, where each element has a name associated
with it. This is similar to `NamedDomainObjectContainer`, however a
`NamedDomainObjectList` doesn't manage the objects in the collection. They
need to be created and added manually.

You can create an instance using the
[ObjectFactory.namedDomainObjectList()](../javadoc/org/gradle/api/model/ObjectFactory.html#namedDomainObjectList-
java.lang.Class-) method.

### DomainObjectSet

A [DomainObjectSet](../javadoc/org/gradle/api/DomainObjectSet.html) simply
holds a set of configurable objects. Compared to `NamedDomainObjectContainer`,
a `DomainObjectSet` doesn't manage the objects in the collection. They need to
be created and added manually.

You can create an instance using the
[ObjectFactory.domainObjectSet()](../javadoc/org/gradle/api/model/ObjectFactory.html#domainObjectSet-
java.lang.Class-) method.

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

