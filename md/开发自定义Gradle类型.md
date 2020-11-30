# 开发自定义Gradle类型


内容

  * [使用bean属性进行配置](#configuration_using_bean_properties)
  * [DSL支持和可扩展性](#dsl_support_and_extensibility)
  * [服务注入](#service_injection)
  * [创建嵌套对象](#nested_objects)
  * [集合类型](#collection_types)

您可以为Gradle开发几种不同类型的“附加组件”，例如[插件](/md/开发自定义Gradle插件.md#custom_plugins)，[任务](/md/开发自定义Gradle任务类型.md#custom_tasks)，
[项目扩展](/md/开发自定义Gradle插件.md#sec:getting_input_from_the_build)或[工件转换](/md/转换解决方案上的依赖工件.md#sec:implementing-
artifact-
transforms)，它们全部实现为类和可以在JVM上运行的其他类型。本章讨论了这些类型共有的一些功能和概念。您可以使用这些功能来帮助实现自定义的Gradle类型，并为用户提供一致的DSL。

本章适用于以下类型：

  * 插件类型。

  * 任务类型。

  * 伪影转换参数类型。

  * Worker API工作操作参数类型。

  * 使用创建的扩展对象`ExtensionContainer.create()`，例如由插件注册的项目扩展。

  * 使用创建的对象`ObjectFactory.newInstance()`。

  * 为托管嵌套属性创建的对象。

  * 的元素`NamedDomainObjectContainer`。

<h2 id = '#configuration_using_bean_properties'> <a href = '#configuration_using_bean_properties'>使用bean属性进行配置</a> </h2>

您实现的自定义Gradle类型通常具有一些配置，您希望这些配置可用于构建脚本和其他插件。例如，下载任务可以具有指定要从中下载的URL和将结果写入的文件系统位置的配置。此配置表示为Java
bean属性。

Kotlin和Groovy为声明Java bean属性提供了便利，这使它们成为实现Gradle类型的良好语言选择。在下面的示例中演示了这些便利。

Gradle还为使用bean属性实现类型提供了一些便利。

<h3 id = '#managed_properties'> <a href = '#managed_properties'>托管属性</a> </h3>

Gradle可以提供抽象属性的实现。这称为 _托管属性_ ，因为Gradle _负责管理属性_ 的状态。属性可以是 _可变的_
，这意味着它既具有getter方法又具有setter方法，或者是 _只读的_ ，意味着它仅具有getter方法。

╔═════════════════════════════  

托管属性当前是一个[孵化](/md/功能生命周期.md#feature_lifecycle)功能。  
  
╚═════════════════════════════    
  
<h4 id = '#mutable_managed_properties'> <a href = '#mutable_managed_properties'>可变的托管属性</a> </h4>

要声明可变的托管属性，请为该类型的属性添加一个抽象的getter方法和一个抽象的setter方法。

这是带有`uri`属性的任务类型的示例：

例子1.可变的托管属性

下载.java

    
    
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

请注意，要使某个属性被视为可变的托管属性，该属性的 _所有_
getter方法和setter方法都必须具有`abstract`并且具有`public`或`protected`可见性。

<h4 id = '#read_only_managed_properties'> <a href = '#read_only_managed_properties'>只读托管属性</a> </h4>

要声明只读托管属性，请为该类型添加一个抽象的getter方法。该属性不应具有任何setter方法。Gradle将提供getter的实现，并为该属性创建一个值。

这是与Gradle可配置的[惰性属性](/md/延迟配置.md#lazy_configuration)或容器类型之一一起使用的有用模式。

这是带有`uri`属性的任务类型的示例：

例子2.只读托管属性

下载.java

    
    
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

请注意，对于一个被视为只读托管属性的属性，该属性的 _所有_
getter方法都必须是`abstract`并且具有`public`或`protected`可见性。该属性不得具有任何setter方法。此外，属性类型必须具有以下之一：

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

Gradle以与[ObjectFactory](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/model/ObjectFactory.html)相同的方式为只读托管属性创建值。

<h4 id = '#read_only_managed_nested_properties'> <a href = '#read_only_managed_nested_properties'>只读托管嵌套属性</a> </h4>

要声明只读的托管嵌套属性，请将该属性的抽象getter方法添加到以注释的类型。该属性不应具有任何setter方法。Gradle提供了getter方法的实现，还为该属性创建了一个值。嵌套类型也被视为自定义类型，并且可以使用本章中讨论的功能。`@[Nested](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/Nested.html)`

当定制类型的嵌套复杂类型具有相同的生命周期时，此模式很有用。如果生命周期不同，请考虑`Property<NestedType>`改用。

这是带有`resource`属性的任务类型的示例。该`Resource`类型也是自定义Gradle类型，并定义了一些托管属性：

例子3.只读托管嵌套属性

下载.java

    
    
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

请注意，要使某个属性被视为只读的托管嵌套属性，则该属性的 _所有_
getter方法都必须是`abstract`且具有`public`或`protected`可见性。该属性不得具有任何setter方法。此外，必须使用注释属性获取器。`@[Nested](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/Nested.html)`

<h3 id = '#managed_types'> <a href = '#managed_types'>托管类型</a> </h3>

甲 _托管类型_ 是一个抽象类或接口没有字段和其属性的所有被管理。也就是说，这是一种其状态完全由Gradle管理的类型。

<h2 id = '#dsl_support_and_extensibility'> <a href = '#dsl_support_and_extensibility'>DSL支持和可扩展性</a> </h2>

当Gradle创建自定义类型的实例时，它会将该实例 _装饰_ 为混入DSL和可扩展性支持。

每个装饰实例都实现[ExtensionAware](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/plugins/ExtensionAware.html)，因此可以将扩展对象附加到它。

请注意，由于向后兼容性问题，当前未装饰使用[Project.container（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Project.html#container-
java.lang.Class-)创建的插件和容器的元素。

<h2 id = '#service_injection'> <a href = '#service_injection'>服务注入</a> </h2>

Gradle提供了许多可用于自定义Gradle类型的有用服务。例如，任务可以使用[WorkerExecutor](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutor.html)服务来并行运行工作，如[worker
API](/md/开发自定义Gradle任务类型.md#worker_api)部分所示。通过
_服务注入_ 提供 _服务_ 。

<h3 id = '#services_for_injection'> <a href = '#services_for_injection'>可用服务</a> </h3>

以下服务可用于注射：

  * [ObjectFactory-](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/model/ObjectFactory.html)允许创建模型对象。有关更多详细信息，请参见[创建嵌套对象](#nested_objects)。

  * [ProjectLayout-](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/ProjectLayout.html)提供对关键项目位置的访问。有关更多详细信息，请参见[延迟配置](/md/延迟配置.md#working_with_files_in_lazy_properties)。该服务在Worker API操作中不可用。

  * [ProviderFactory-](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/provider/ProviderFactory.html)创建`Provider`实例。有关更多详细信息，请参见[延迟配置](/md/延迟配置.md#lazy_configuration)。

  * [WorkerExecutor-](https://docs.gradle.org/6.7.1/javadoc/org/gradle/workers/WorkerExecutor.html)允许任务并行运行工作。有关更多详细信息，请参见[worker API](/md/开发自定义Gradle任务类型.md#worker_api)。

  * [FileSystemOperations-](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/FileSystemOperations.html)允许任务在文件系统上运行操作，例如删除文件，复制文件或同步目录。

  * [ArchiveOperations-](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/ArchiveOperations.html)允许任务对存档文件（例如ZIP或TAR文件）运行操作。

  * [ExecOperations-](https://docs.gradle.org/6.7.1/javadoc/org/gradle/process/ExecOperations.html)允许任务运行外部进程，并带有对运行外部`java`程序的专门支持。

  * [ToolingModelBuilderRegistry-](https://docs.gradle.org/6.7.1/javadoc/org/gradle/tooling/provider/model/ToolingModelBuilderRegistry.html)允许插件注册Gradle工具API模型。

出了上述情况，`ProjectLayout`并`WorkerExecutor`服务仅适用于注射项目插件。

<h3 id = '#constructor_injection'> <a href = '#constructor_injection'>构造器注入</a> </h3>

对象可以通过两种方式接收其所需的服务。第一种选择是将服务添加为类构造函数的参数。构造函数必须带有`javax.inject.Inject`注释。Gradle使用每个构造函数参数的声明类型来确定对象所需的服务。构造函数参数及其名称的顺序并不重要，可以随便更改。

这是一个示例，显示了一个`ObjectFactory`通过其构造函数接收“通过”的任务类型：

例子4.构造函数服务注入

下载.java

    
    
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

<h3 id = '#property_injection'> <a href = '#property_injection'>资产注入</a> </h3>

或者，可以通过`javax.inject.Inject`在类中添加带有注释的属性获取方法来注入服务。例如，当由于向后兼容性约束而无法更改类的构造函数时，此功能很有用。这种模式还允许Gradle将服务的创建推迟到调用getter方法之前，而不是在创建实例时进行。这可以帮助提高性能。Gradle使用getter方法的声明的返回类型来确定要提供的服务。该属性的名称并不重要，并且可以是您喜欢的任何名称。

属性获取器方法必须为`public`或`protected`。该方法可以是，`abstract`或者在不可能的情况下可以具有虚拟方法主体。方法主体将被丢弃。

这是一个示例，显示了一个通过属性getter方法接收两项服务的任务类型：

例子5.物业服务注入

下载.java

    
    
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

<h2 id = '#nested_objects'> <a href = '#nested_objects'>创建嵌套对象</a> </h2>

自定义Gradle类型可以使用[ObjectFactory](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/model/ObjectFactory.html)服务创建Gradle类型的实例以用于其属性值。这些实例可以利用本章讨论的功能，使您可以创建“嵌套”对象和嵌套DSL。

您还可以让Gradle使用[托管的嵌套属性](#read_only_managed_nested_properties)为您创建嵌套对象。

在以下示例中，项目扩展`ObjectFactory`通过其构造函数接收实例。构造函数使用它创建一个嵌套`Resource`对象（也是一个自定义Gradle类型），并使该对象可通过`resource`属性使用。

例子6.嵌套对象的创建

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

<h2 id = '#collection_types'> <a href = '#collection_types'>集合类型</a> </h2>

Gradle提供了用于维护对象集合的类型，旨在与Gradle DSL配合使用并提供有用的功能，例如延迟配置。

<h3 id = '#nameddomainobjectcontainer'> <a href = '#nameddomainobjectcontainer'>命名域对象容器</a> </h3>

甲[NamedDomainObjectContainer](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/NamedDomainObjectContainer.html)管理一组对象，其中每个元素具有与之相关联的名称。该容器负责创建和配置元素，并提供DSL，构建脚本可用于定义和配置元素。它旨在容纳本身可配置的对象，例如一组自定义Gradle对象。

Gradle`NamedDomainObjectContainer`在整个API中广泛使用类型。例如，`project.tasks`用于管理项目任务的对象是`NamedDomainObjectContainer<Task>`。

您可以使用[ObjectFactory](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/model/ObjectFactory.html)服务创建容器实例，
该服务提供[ObjectFactory.domainObjectContainer（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/model/ObjectFactory.html#domainObjectContainer-java.lang.Class-)方法。使用[Project.container（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/Project.html#container-java.lang.Class-)方法也可以使用此方法，但是在自定义Gradle类型中，通常最好使用注入的`ObjectFactory`服务而不是传递`Project`实例。

您还可以使用如上所述的[只读托管属性](#read_only_managed_properties)创建容器实例。

为了对任何`domainObjectContainer()`方法使用类型，它必须公开一个名为“
name”的属性作为对象的唯一且恒定的名称。该`domainObjectContainer(Class)`方法的变体通过调用带有字符串参数的类的构造函数来创建新实例，该参数是对象的所需名称。以这种方式创建的对象被视为自定义Gradle类型，因此可以利用本章讨论的功能，例如服务注入或托管属性。

有关`domainObjectContainer()`允许自定义实例化策略的方法变体，请参见上面的链接。

例子7.管理对象集合

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

对于每个容器属性，Gradle会自动向Groovy和Kotlin DSL添加一个块，您可以使用该块来配置容器的内容：

例子8.配置块

`Kotlin``Groovy`

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

<h3 id = '#extensiblepolymorphicdomainobjectcontainer'> <a href = '#extensiblepolymorphicdomainobjectcontainer'>ExtensiblePolymorphicDomainObjectContainer</a> </h3>

一个[ExtensiblePolymorphicDomainObjectContainer](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/ExtensiblePolymorphicDomainObjectContainer.html)是一个`NamedDomainObjectContainer`允许您为不同类型的对象定义实例化策略的工具。

您可以使用[ObjectFactory.polymorphicDomainObjectContainer（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/model/ObjectFactory.html#polymorphicDomainObjectContainer-java.lang.Class-)方法创建实例。

<h3 id = '#nameddomainobjectset'> <a href = '#nameddomainobjectset'>命名域对象集</a> </h3>

甲[NamedDomainObjectSet](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/NamedDomainObjectSet.html)持有一组可配置的对象，其中每个元素具有与之相关联的名称。这类似于`NamedDomainObjectContainer`，但是`NamedDomainObjectSet`不管理集合中的对象。它们需要手动创建和添加。

您可以使用[ObjectFactory.namedDomainObjectSet（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/model/ObjectFactory.html#namedDomainObjectSet-java.lang.Class-)方法创建实例。

<h3 id = '#nameddomainobjectlist'> <a href = '#nameddomainobjectlist'>NamedDomainObjectList</a> </h3>

一个[NamedDomainObjectList](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/NamedDomainObjectList.html)持有配置的对象，其中每个元素具有与它关联的名称的列表。这类似于`NamedDomainObjectContainer`，但是`NamedDomainObjectList`不管理集合中的对象。它们需要手动创建和添加。

您可以使用[ObjectFactory.namedDomainObjectList（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/model/ObjectFactory.html#namedDomainObjectList-java.lang.Class-)方法创建实例。

<h3 id = '#domainobjectset'> <a href = '#domainobjectset'>DomainObjectSet</a> </h3>

一个[DomainObjectSet](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/DomainObjectSet.html)只是拥有一组可配置的对象。与相比`NamedDomainObjectContainer`，a`DomainObjectSet`不管理集合中的对象。它们需要手动创建和添加。

您可以使用[ObjectFactory.domainObjectSet（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/model/ObjectFactory.html#domainObjectSet-java.lang.Class-)方法创建实例。

