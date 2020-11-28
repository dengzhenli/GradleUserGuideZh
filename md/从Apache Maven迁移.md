# 从Apache Maven迁移构建


内容

  * [进行迁移](#migmvn:making_a_case)
  * [一般准则](#migmvn:general_guidelines)
  * [了解构建生命周期](#migmvn:build_lifecycle)
  * [执行自动转换](#migmvn:automatic_conversion)
  * [迁移依赖项](#migmvn:migrating_deps)
  * [使用物料清单（BOM）](#migmvn:using_boms)
  * [迁移多模块构建（项目聚合）](#migmvn:multimodule_builds)
  * [迁移Maven配置文件和属性](#migmvn:profiles_and_properties)
  * [筛选资源](#migmvn:filtering_resources)
  * [配置集成测试](#migmvn:integration_tests)
  * [迁移常用插件](#migmvn:common_plugins)
  * [了解您不需要哪些插件](#migmvn:unnecessary_plugins)
  * [处理不常见和自定义的插件](#migmvn:custom_plugins)
  * [进一步阅读](#further_reading)

  
[Apache Maven](https://maven.apache.org/)是用于Java和其他基于JVM的项目的构建工具，这些项目已得到广泛使用，因此想要使用Gradle的人经常必须迁移现有的Maven构建。本指南将通过解释这两种工具的模型之间的差异和相似之处，并提供可简化操作的步骤来帮助进行这种迁移。

转换构建可能会很吓人，但您不必一个人做。您可以从[help.gradle.org](https://gradle.org/help)搜索文档，论坛和StackOverflow，或者在[遇到麻烦时](https://gradle.org/help)访问[Gradle社区](https://discuss.gradle.org/c/help-discuss)。

## [](#migmvn:making_a_case)[进行迁移](#migmvn:making_a_case)

Gradle和Maven之间的主要区别是灵活性，性能，用户体验和依赖性管理。[Maven与Gradle功能比较中](https://gradle.org/maven-vs-gradle)提供了这些方面的直观概述。

自Gradle
3.0以来，Gradle投入了大量资金，以使Gradle的构建速度更快，并具有[构建缓存](https://blog.gradle.org/introducing-gradle-build-cache) ，
[避免编译](https://blog.gradle.org/incremental-compiler-avoidance) 和改进的增量Java编译器等功能。对于大多数项目，即使不使用构建缓存，
Gradle的速度也比Maven快2-10倍。可以在[此处](https://gradle.org/gradle-vs-maven-performance/) 找到有关从Maven切换到Gradle的深入性能比较和业务案例。

## [](#migmvn:general_guidelines)[一般准则](#migmvn:general_guidelines)

Gradle和Maven对如何构建项目有根本不同的看法。Gradle提供了一种灵活且可扩展的构建模型，该模型将实际工作委托给[_任务依赖关系图_](/md/什么是Gradle.md#the_core_model_is_based_on_tasks)
。Maven使用固定的线性阶段模型，可以在其中附加目标（完成工作的事物）。这可能会使两者之间的迁移看起来令人生畏，但迁移可能出奇的容易，因为Gradle遵循许多与Maven相同的约定（例如[标准项目结构）](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#sec:java_project_layout)
，并且其依赖项管理以类似的方式工作。

在这里，我们列出了一系列步骤供您遵循，这将有助于促进将任何Maven构建移植到Gradle：

╔═════════════════════════════  
并排保留旧的Maven构建和新的Gradle构建。您知道Maven构建可以工作，因此您应该保留它，直到您确信Gradle构建可以生成所有相同的工件，然后再做您需要的事情。这也意味着用户无需获取源树的新副本即可尝试Gradle构建。  
╚═════════════════════════════    
  
  1. [创建Maven构建的构建扫描](https://scans.gradle.com/#maven)。

构建扫描将使可视化现有Maven构建中发生的事情变得更加容易。对于Maven构建，您将能够查看项目结构，正在使用的插件，构建步骤的时间表等等。保持方便，以便您可以将其与转换项目时获得的Gradle构建扫描进行比较。

  2. 开发一种机制来验证两个构建产生相同的工件

这是确保您的部署和测试不中断的至关重要的一步。即使是很小的更改，例如JAR中清单文件的内容，也会引起问题。如果您的Gradle构建生成的输出与Maven构建生成的输出相同，则这将使您和其他人对切换充满信心，并使更容易实施将带来最大收益的重大更改。

这并不意味着您需要在每个阶段都验证每个工件，尽管这样做可以帮助您快速确定问题的根源。您可以只关注关键输出，例如最终报告以及已发布或部署的工件。

与Maven相比，您需要考虑Gradle产生的构建输出中的某些固有差异。生成的POM将仅包含消耗所需的信息，并且它们将针对该场景正确使用`<compile>`和确定`<runtime>`范围。您可能还会看到存档中文件和类路径中文件顺序的差异。大多数差异将是良性的，但值得识别它们并验证它们是否正确。

  3. [运行自动转换](#migmvn:automatic_conversion)

这将创建您需要的所有Gradle构建文件，即使对于[多模块构建也是如此](#migmvn:multimodule_builds)。对于更简单的Maven项目，Gradle构建将可以运行！

  4. [为Gradle build创建一个build扫描](https://scans.gradle.com/)。

构建扫描将使可视化构建中的事情变得更加容易。对于Gradle构建，您将能够查看项目结构，依赖关系（常规和项目间的依赖关系），正在使用的插件以及构建的控制台输出。

此时您的构建可能会失败，但是没关系；扫描仍将运行。将Gradle构建的构建扫描与Maven构建的构建扫描进行比较，然后继续执行此列表以排除故障。

我们建议您在迁移过程中定期生成构建扫描，以帮助您确定问题并排除故障。如果需要，您还可以使用Gradle构建扫描来确定[提高构建性能的](https://guides.gradle.org/performance/)机会，毕竟性能是首先切换到Gradle的主要原因。

  5. [验证您的依赖关系并解决任何问题](#migmvn:migrating_deps)

  6. [配置集成和功能测试](#migmvn:integration_tests)

通过配置额外的源集，可以简单地迁移许多测试。如果您使用的是第三方库（例如[FitNesse）](http://docs.fitnesse.org/FrontPage)，请查看[Gradle
Plugin Portal](https://plugins.gradle.org/)上是否有合适的社区插件。

  7. 用Gradle等效项替换Maven插件

对于[流行的插件](#migmvn:common_plugins)，Gradle经常有一个等效的插件供您使用。您可能还会发现可以[用内置的Gradle功能替换插件](#migmvn:unnecessary_plugins)。最后，您可能需要[通过自己的自定义插件和任务类型](#migmvn:custom_plugins)重新实现Maven插件。

本章的其余部分更详细地介绍了将构建从Maven迁移到Gradle的特定方面。

## [](#migmvn:build_lifecycle)[了解构建生命周期](#migmvn:build_lifecycle)

Maven构建基于[_构建生命周期_](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
的概念，该概念由一组固定阶段组成。这可能会成为用户迁移到Gradle的障碍，因为它的构建生命周期[有所不同](/md/构建生命周期.md#build_lifecycle)，
尽管了解Gradle的构建方式如何适合初始化，配置和执行阶段的结构很重要。幸运的是，Gradle具有可以模仿Maven各个阶段的功能：[_生命周期任务_](/md/处理任务.md#sec:lifecycle_tasks)
。

这些使您可以通过创建仅依赖于您感兴趣的任务的无操作任务来定义自己的“生命周期”。为了使Maven用户更轻松地过渡到Gradle，[Base插件](https://docs.gradle.org/6.7.1/userguide/base_plugin.html#sec:base_tasks)
-由所有JVM语言应用[Java库](/md/Java库插件.md#java_library_plugin) 插件之类的[插件](/md/Java库插件.md#java_library_plugin)
—提供了一组与主要Maven阶段相对应的生命周期任务。

这是一些主要的Maven阶段以及它们映射到的Gradle任务的列表：

`clean`

    

使用`clean`基本插件提供的任务。

`compile`

    

使用[Java插件](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#sec:java_tasks) 和其他JVM语言插件`classes`提供的任务。这将编译所有语言的所有源文件的所有类，并通过任务执行[资源过滤](#migmvn:filtering_resources)。[](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#sec:java_tasks)[](#migmvn:filtering_resources)`processResources`

`test`

    

使用`test`Java插件提供的任务。它仅运行单元测试，或更具体地说，运行组成[`test`源集](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#source_sets) 的测试。

`package`

    

使用`assemble`基本插件提供的任务。这将构建适合项目的任何软件包，例如Java库的JAR或传统Java Webapp的WAR。

`verify`

    

使用`check`基本插件提供的任务。这将运行附加到它的所有验证任务，通常包括单元测试，任何静态分析任务（例如[Checkstyle](https://docs.gradle.org/6.7.1/userguide/checkstyle_plugin.html#checkstyle_plugin)
）以及其他任务。如果要包括集成测试，则必须[手动配置这些](#migmvn:integration_tests)，这是一个简单的过程。

`install`

    

使用[Maven发布插件](/md/Maven发布插件.md#publishing_maven:tasks) `publishToMavenLocal`提供的任务。[](/md/Maven发布插件.md#publishing_maven:tasks)

请注意，Gradle构建不需要您“安装”工件，因为您可以访问更合适的功能，例如[项目间依赖](/md/声明依赖.md#sub:project_dependencies) 和[复合构建](/md/复合构建.md) 。您应该只`publishToMavenLocal`用于与Maven构建进行互操作。

Gradle还允许您解决对本地Maven缓存的依赖关系，如“[声明存储库”](#migmvn:declaring_repos) 部分中所述。

`deploy`

    

使用[Maven Publish插件](/md/Maven发布插件.md#publishing_maven:tasks) `publish`提供的任务
-如果您的构建正在使用该Maven插件，请确保从旧的Maven插件（ID：）切换。这会将您的程序包发布到所有已配置的发布存储库。即使定义了多个存储库，也有其他任务可以使您发布到单个存储库。[](/md/Maven发布插件.md#publishing_maven:tasks)`maven`

请注意， _默认情况下_ ，Maven Publish Plugin不会发布 **源代码和Javadoc JAR**
，但是可以按照[构建Java项目的指南中的](/md/构建Java和JVM项目.md#sec:java_packaging)说明轻松激活它。
__[](/md/构建Java和JVM项目.md#sec:java_packaging)

## [](#migmvn:automatic_conversion)[执行自动转换](#migmvn:automatic_conversion)

Gradle的[`init`任务](https://docs.gradle.org/6.7.1/userguide/build_init_plugin.html#build_init_plugin)通常用于创建新的骨架项目，但是您也可以使用它来将现有的Maven构建自动转换为Gradle。将Gradle[安装到系统上后](/md/安装Gradle.md#installation)，只需执行以下命令

    
    
    > gradle init

从根项目目录，并让Gradle做它的事情。这基本上包括解析现有的POM并生成相应的Gradle构建脚本。如果您要迁移[多项目构建，](/md/Gradle中的多项目构建.md#multi_project_builds)
Gradle还将创建一个设置脚本。

您会发现新的Gradle构建包括以下内容：

  * POM中指定的所有自定义存储库

  * 您的外部和项目间依赖性

  * 用于构建项目的适当插件（仅限于[Maven Publish](/md/Maven发布插件.md)，[Java](https://docs.gradle.org/6.7.1/userguide/java_plugin.html)和[War](https://docs.gradle.org/6.7.1/userguide/war_plugin.html)插件中的一个或多个）

有关自动转换功能的完整列表，请参见[Build Init插件一章](https://docs.gradle.org/6.7.1/userguide/build_init_plugin.html#sec:pom_maven_conversion)。

要记住的一件事是程序集不会自动转换。它们转换时并不一定有问题，但是您将需要做一些手工工作。选项包括：

  * 使用[分发插件](https://docs.gradle.org/6.7.1/userguide/distribution_plugin.html#distribution_plugin)

  * 使用[Java库分发插件](https://docs.gradle.org/6.7.1/userguide/java_library_distribution_plugin.html#java_library_distribution_plugin)

  * 使用[应用程序插件](/md/Java应用插件.md#application_plugin)

  * [创建自定义存档任务](/md/编写构建脚本.md#sec:creating_archives_example)

  * 从[Gradle插件门户](https://plugins.gradle.org/)使用合适的社区插件[](https://plugins.gradle.org/)

如果您的Maven版本没有很多插件或自定义方式很多，则只需运行

    
    
    > gradle build

迁移完成后。这将运行测试并产生所需的工件，而您无需任何额外的干预。

## [](#migmvn:migrating_deps)[迁移依赖项](#migmvn:migrating_deps)

Gradle的依赖项管理系统比Maven的依赖项管理系统更灵活，但它仍支持相同的存储库，声明的依赖项，
范围（Gradle中的[依赖项配置](/md/声明依赖.md#sec:what-are-dependency-configurations) ）和可传递依赖项的概念。
实际上，Gradle与兼容Maven的存储库完美配合，这使得迁移依赖关系变得容易。

╔═════════════════════════════  
两种工具之间的一个显着区别是它们如何管理版本冲突。Maven使用“最接近”的匹配算法，而Gradle选择最新的匹配算法。不过不要担心，如[管理传递依赖项中](/md/升级传递依赖的版本.md)所述，您对选择哪个版本有很多控制权。  
╚═════════════════════════════    
  
在以下各节中，我们将向您展示如何迁移Maven构建的依赖管理信息中最常见的元素。

### [](#migmvn:declaring_deps)[声明依赖](#migmvn:declaring_deps)

Gradle使用与Maven相同的依赖项标识符组件：组ID，工件ID和版本。它还支持分类器。因此，您需要做的就是将标识符的依赖项信息替换为Gradle的语法，这在“[声明依赖项”](/md/声明依赖.md)一章中进行了介绍。

例如，考虑对Log4J的这种Maven风格的依赖关系：

    
    
    <dependencies>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
    </dependencies>

在Gradle构建脚本中，这种依赖关系如下所示：

例子1.声明一个简单的编译时依赖

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        implementation 'log4j:log4j:1.2.12'  __**( 1)**
    }

build.gradle.kts

    
    
    dependencies {
        implementation("log4j:log4j:1.2.12")  __**( 1)**
    }
═════════════════════════════   
①  将Log4J的1.2.12版本附加到`implementation`配置（作用域）  
╚═════════════════════════════    
  
字符串标识符采用的Maven的值`groupId`，`artifactId`并且`version`，虽然Gradle指它们作为`group`，`module`和`version`。

上面的示例提出了一个明显的问题：该`implementation`配置是什么？它是[Java插件](https://docs.gradle.org/6.7.1/userguide/java_plugin.html#tab:configurations)提供的标准依赖项配置之一，通常被用来替代Maven的默认`compile`范围。

Maven的作用域和Gradle的标准配置之间的一些区别归结为Gradle区分了构建模块所需的依赖项和构建依赖于模块的模块所需的依赖项。Maven没有这种区别，因此已发布的POM通常包括库的使用者实际上不需要的依赖项。

以下是主要的Maven依赖范围以及如何处理它们的迁移：

`compile`

    

Gradle具有两种可以代替`compile`示波器的配置：`implementation`和`api`。前者适用于所有应用Java插件的项目，而`api`仅适用于专门应用[Java库插件的项目](/md/Java库插件.md#java_library_plugin)。

在大多数情况下，您应该只使用`implementation`配置，尤其是在构建应用程序或Webapp时。但是，如果您要构建库，则可以`api`在[构建Java库](/md/构建Java和JVM项目.md#sec:building_java_libraries)的部分中了解应使用哪些依赖项进行声明。对之间的差异甚至更多的信息`api`，并`implementation`在上面链接的Java库插件章提供。

`runtime`

    

使用`runtimeOnly`配置。

`test`

    

Gradle区分了 _编译_ 项目测试所需的那些依赖项和仅 _运行_ 它们所需的那些依赖项。

应针对`testImplementation`配置声明测试编译所需的依赖关系。仅运行测试所需的那些应该使用`testRuntimeOnly`。

`provided`

    

使用`compileOnly`配置。

请注意，[War插件](https://docs.gradle.org/6.7.1/userguide/war_plugin.html#sec:war_dependency_management)添加`providedCompile`和`providedRuntime`依赖项配置。它们的行为`compileOnly`与WAR文件中的行为略有不同，并且仅确保这些依赖项未打包。但是，依赖项包含在运行时和测试运行时类路径中，因此如果您需要这种行为，请使用这些配置。

`import`

    

该`import`范围主要在`<dependencyManagement>`块内使用，并且仅适用于仅POM的出版物。阅读有关[使用物料清单](#migmvn:using_boms)的部分，以了解有关如何复制此行为的更多信息。

您还可以指定对仅POM的发布的常规依赖性。在这种情况下，在该POM中声明的依赖关系将被视为构建的常规传递依赖关系。

例如，假设您要使用`groovy-
all`POM进行测试。这是仅POM的出版物，在`<dependencies>`块内列出了自己的依赖性。Gradle构建中的适当配置如下所示：

示例2.消费仅POM依赖项

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        testImplementation 'org.codehaus.groovy:groovy-all:2.5.4'
    }

build.gradle.kts

    
    
    dependencies {
        testImplementation("org.codehaus.groovy:groovy-all:2.5.4")
    }

这样的结果将是将POM中的所有`compile`和`runtime`范围依赖项`groovy-
all`添加到测试运行时类路径，而仅将`compile`范围依赖项添加到测试编译类路径。与其他作用域的依赖关系将被忽略。

### [](#migmvn:declaring_repos)[声明存储库](#migmvn:declaring_repos)

Gradle允许您从任何与Maven兼容或与Ivy兼容的存储库中检索已声明的依赖项。与Maven不同，它没有默认存储库，因此您必须声明至少一个。为了具有与Maven构建相同的行为，只需在Gradle构建中配置[Maven
Central](/md/声明存储库.md#sub:maven_central)，如下所示：

例子3.配置构建以使用Maven Central

`Groovy``Kotlin`

build.gradle

    
    
    repositories {
        mavenCentral()
    }

build.gradle.kts

    
    
    repositories {
        mavenCentral()
    }

您还可以使用该`repositories
{}`块来配置自定义存储库，如“[存储库类型”](/md/声明存储库.md#sec:maven_repo)一章中所述。

最后，Gradle允许您解决对[本地Maven缓存/存储库的](/md/声明存储库.md#sub:maven_local)依赖关系。这有助于Gradle构建与Maven构建进行互操作，但是如果您不需要这种互操作性，则不应使用该技术。如果要通过文件系统共享已发布的工件，请考虑使用URL配置[自定义Maven存储库](/md/声明存储库.md#sec:maven_repo)`file://`。

您可能还对学习Gradle自己的[依赖项缓存](/md/了解依赖性解析.md#sec:dependency_cache)感兴趣，该[缓存的](/md/了解依赖性解析.md#sec:dependency_cache)行为比Maven的可靠，并且可以被多个并发的Gradle进程安全地使用。

### [](#migmvn:controlling_dep_versions)[控制依赖项版本](#migmvn:controlling_dep_versions)

传递依赖项的存在意味着您可以轻松地在依赖关系图中最终获得同一依赖项的多个版本。默认情况下，Gradle将在图中选择依赖关系的最新版本，但这并不总是正确的解决方案。这就是为什么它提供了几种机制来控制解决给定依赖项的哪个版本的原因。

在每个项目的基础上，您可以使用：

  * [依赖约束](/md/升级传递依赖的版本.md#sec:adding-constraints-transitive-deps)

  * [的材料清单](#migmvn:using_boms)（BOM表的Maven）

  * [覆盖传递版本](/md/降级版本并排除依赖项.md#sec:enforcing_dependency_version)

在[控制传递依赖项](/md/升级传递依赖的版本.md)一章中列出了更多专门的选项。

如果要确保多项目构建中所有项目之间版本的一致性（类似于`<dependencyManagement>`Maven中的块的工作方式），可以使用[Java Platform Plugin](/md/Java平台插件.md#java_platform_plugin) 。这允许您声明一组可以应用于多个项目的依赖项约束。您甚至可以将平台发布为Maven
BOM或使用Gradle的元数据格式发布。有关如何执行此操作的更多信息，请参见插件页面，尤其是在使用[平台](/md/Java平台插件.md#sec:java_platform_consumption)部分，以了解如何将平台应用于同一构建中的其他项目。

### [](#migmvn:excluding_deps)[排除传递依赖](#migmvn:excluding_deps)

Maven构建使用排除项将不需要的依赖关系或不需要的依赖关系 _版本_ 排除在依赖关系图中。您可以使用Gradle做同样的事情，但这不一定是 _正确的_
事情。Gradle提供了其他一些选项，这些选项可能更适合给定的情况，因此您确实需要了解 _为什么_ 要适当地设置排除项。

如果您出于与版本无关的原因而要排除依赖项，请查看[dependency_downgrade_and_exclude.html](/md/降级版本并排除依赖项.md#sec:excluding-transitive-deps)的部分。它显示了如何将排除项附加到整个配置（通常是最合适的解决方案）或依赖项。您甚至可以轻松地将排除应用于所有配置。

如果您对控制实际解决依赖关系的版本更感兴趣，请参阅上一节。

### [](#migmvn:optional_deps)[处理可选的依赖](#migmvn:optional_deps)

关于可选依赖项，您可能会遇到两种情况：

  * 您的某些传递依赖项被声明为可选

  * 您想在项目的已发布POM中将一些直接依赖项声明为可选

对于第一种情况，Gradle的行为与Maven相同，只是忽略了声明为可选的任何传递依赖。如果相同的依存关系在依存关系图中的其他位置显示为非可选，则无法解析，并且对所选版本没有影响。

至于将依赖项发布为可选的，Gradle提供了一个更丰富的模型，称为[Feature Variants](/md/建模功能变体和可选依赖项.md#feature_variants)，它可以让您声明库提供的“可选功能”。

## [](#migmvn:using_boms)[使用物料清单（BOM）](#migmvn:using_boms)

Maven允许您通过在`<dependencyManagement>`打包类型为的POM文件的一部分内定义依赖项来共享依赖项约束`pom`。然后可以将这种特殊类型的POM（物料清单）导入其他POM中，以便您在项目中拥有一致的库版本。

Gradle可以通过基于[platform（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.dsl.DependencyHandler.html#org.gradle.api.artifacts.dsl.DependencyHandler:platform\(java.lang.Object\))和[forcedPlatform（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.artifacts.dsl.DependencyHandler.html#org.gradle.api.artifacts.dsl.DependencyHandler:enforcedPlatform\(java.lang.Object\))方法的特殊依赖项语法，将此类BOM用于相同的目的。您只需以常规方式声明依赖项，然后将依赖项标识符包装在适当的方法中，如“导入”
Spring Boot Dependencies BOM的示例所示：

例子4.在Gradle版本中导入BOM

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        implementation platform('org.springframework.boot:spring-boot-dependencies:1.5.8.RELEASE') __**( 1)**
    
        implementation 'com.google.code.gson:gson' __**( 2)**
        implementation 'dom4j:dom4j'
    }

build.gradle.kts

    
    
    dependencies {
        implementation(platform("org.springframework.boot:spring-boot-dependencies:1.5.8.RELEASE"))  __**( 1)**
    
        implementation("com.google.code.gson:gson")  __**( 2)**
        implementation("dom4j:dom4j")
    }
═════════════════════════════  
①  应用Spring Boot Dependencies BOM  
②  添加依赖项，该依赖项的版本由该BOM表定义  
╚═════════════════════════════  
您可以了解更多关于此功能之间的差异`platform()`，并`enforcedPlatform()`在上一节[从一个Maven
BOM导入版本的建议](/md/在项目之间共享依赖版本.md#sub:bom_import)。

╔═════════════════════════════  
您可以使用此功能将`<dependencyManagement>`来自任何依赖项的POM的信息应用于Gradle构建，即使那些没有打包类型为的信息也是如此`pom`。双方`platform()`并`enforcedPlatform()`会忽略声明的依赖`<dependencies>`块。  
╚═════════════════════════════    
  
## [](#migmvn:multimodule_builds)[迁移多模块构建（项目聚合）](#migmvn:multimodule_builds)

Maven的多模块构建与Gradle的[多项目构建](/md/Gradle中的多项目构建.md#multi_project_builds)很好地映射。尝试相应的[示例，](https://docs.gradle.org/6.7.1/samples/sample_jvm_multi_project_build.html)以了解如何设置基本的多项目Gradle构建。

要迁移多模块Maven构建，只需执行以下步骤：

  1. 创建一个与`<modules>`根POM块匹配的设置脚本。

例如，以下代码`<modules>`块：

    
    <modules>
        <module>simple-weather</module>
        <module>simple-webapp</module>
    </modules>

可以通过在设置脚本中添加以下行来迁移：

例子5.声明哪些项目是构建的一部分

`Groovy``Kotlin`

settings.gradle

    
    rootProject.name = 'simple-multi-module'  __**( 1)**
    
    include 'simple-weather', 'simple-webapp'  __**( 2)**

settings.gradle.kts

    
    rootProject.name = "simple-multi-module"  __**( 1)**
    
    include("simple-weather", "simple-webapp")  __**( 2)**
    
═════════════════════════════    
①  设置整个项目的名称   
②  配置两个子项目作为此构建的一部分  
╚═════════════════════════════    
输出 **`gradle projects`**

    
    > gradle projects
    
    ------------------------------------------------------------
    Root project
    ------------------------------------------------------------
    
    Root project 'simple-multi-module'
    +--- Project ':simple-weather'
    \--- Project ':simple-webapp'
    
    To see a list of the tasks of a project, run gradle <project-path>:tasks
    For example, try running gradle :simple-weather:tasks

  2. 将跨模块依赖项替换为[项目依赖项](/md/声明依赖.md#sub:project_dependencies)。

  3. 使用[约定插件](/md/在子项目之间共享构建逻辑.md#sec:convention_plugins)复制项目继承。

这基本上涉及创建一个根项目构建脚本，该脚本将共享配置注入到适当的子项目中。

### [](#migmvn:sub:sharing-versions)[跨项目共享版本](#migmvn:sub:sharing-versions)

如果要复制在`dependencyManagement`根POM文件的部分中声明的具有依赖项版本的Maven模式，最好的方法是利用`java-
platform`插件。您将需要为此添加一个专用项目，并在构建的常规项目中使用它。有关此模式的更多详细信息，请参见[文档](/md/Java平台插件.md)。

## [](#migmvn:profiles_and_properties)[迁移Maven配置文件和属性](#migmvn:profiles_and_properties)

Maven允许您使用各种属性对构建进行参数化。一些是项目模型的只读属性，其他是用户在POM中定义的属性。它甚至允许您将系统属性视为项目属性。

Gradle具有类似的项目属性系统，尽管它可以区分项目属性和系统属性。例如，您可以在以下位置定义属性：

  * 构建脚本

  * 一个`gradle.properties`在项目根目录文件

  * 一个`gradle.properties`在文件`$HOME/.gradle`目录

这些不是唯一的选择，因此，如果您有兴趣了解有关如何以及在何处定义属性的更多信息，请查阅“[构建环境”](/md/Gradle环境搭建.md#build_environment)一章。

您需要了解的一项重要行为是，在构建脚本和一个外部属性文件中定义了相同的属性时会发生什么：构建脚本值优先。总是。幸运的是，您可以模仿配置文件的概念以提供可覆盖的默认值。

这使我们进入了Maven配置文件。这些是根据环境，目标平台或任何其他类似因素启用和禁用不同配置的方法。从逻辑上讲，它们仅是有限的“
if”语句。并且由于Gradle具有更强大的声明条件的方法，因此它不需要对配置文件的正式支持（依赖项的POM中除外）。您将看到，通过将条件与辅助构建脚本结合使用，可以轻松获得相同的行为。

假设您根据环境有不同的部署设置：本地开发（默认），测试环境和生产。要添加型材式的行为，首先在项目的根每个环境中创建构建脚本：`profile-
default.gradle`，`profile-test.gradle`，和`profile-
prod.gradle`。然后，您可以根据自己选择的[项目属性](/md/Gradle环境搭建.md#sec:project_properties)有条件地应用这些概要文件脚本之一。

下面的例子演示了如何使用称为项目属性的基本技术`buildProfile`和配置文件脚本，简单地初始化一个[额外的项目属性](/md/编写构建脚本.md#sec:extra_properties)称为`message`：

例子6.模仿Gradle中的Maven配置文件的行为

`Groovy``Kotlin`

build.gradle

    
    
    if (!hasProperty('buildProfile')) ext.buildProfile = 'default'  __**( 1)**
    
    apply from: "profile-${buildProfile}.gradle"  __**( 2)**
    
    task greeting {
        doLast {
            println message  __**( 3)**
        }
    }

profile-default.gradle

    
    
    ext.message = 'foobar'  __**( 4)**

profile-test.gradle

    
    
    ext.message = 'testing 1 2 3'  __**( 4)**

profile-prod.gradle

    
    
    ext.message = 'Hello, world!'  __**( 4)**

build.gradle.kts

    
    
    val buildProfile: String? by project  __**( 1)**
    
    apply(from = "profile-${buildProfile ?: "default"}.gradle.kts")  __**( 2)**
    
    tasks.register("greeting") {
        val message: String by project.extra
        doLast {
            println(message)  __**( 3)**
        }
    }

profile-default.gradle.kts

    
    
    val message by extra("foobar")  __**( 4)**

profile-test.gradle.kts

    
    
    val message by extra("testing 1 2 3")  __**( 4)**

profile-prod.gradle.kts

    
    
    val message by extra("Hello, world!")  __**( 4)**
═════════════════════════════   
①  检查（Groovy）是否存在或绑定（Kotlin）`buildProfile`项目属性  
②  使用`buildProfile`脚本文件名中的值应用适当的配置文件脚本  
③  打印出`message`额外项目属性的值  
④  初始化`message`额外的项目属性，然后可以在主构建脚本中使用其值  
╚═════════════════════════════   
使用此设置后，您可以通过传递所用项目属性的值来激活其中一个配置文件- `buildProfile`在这种情况下：

输出 **`gradle greeting`**

    > gradle greeting
    foobar

输出 **`gradle -PbuildProfile=test greeting`**

    > gradle -PbuildProfile=test greeting
    testing 1 2 3

您不仅限于检查项目属性。您还可以检查环境变量，JDK版本，运行内部版本的OS或您可以想象的任何其他内容。

要记住的一件事是，高级条件语句使构建更难以理解和维护，类似于它们使面向对象的代码复杂化的方式。配置文件也是如此。Gradle提供了许多更好的方法来避免广泛使用Maven经常需要的配置文件，例如，通过配置彼此不同的多个任务。请参见[Maven发布插件](/md/Maven发布插件.md#publishing_maven:tasks)创建的任务。`publish_PubName_ PublicationTo _RepoName_Repository`[](/md/Maven发布插件.md#publishing_maven:tasks)

有关在Gradle中使用Maven概要文件的冗长讨论，请参阅[此博客文章](https://blog.gradle.org/maven-pom-
profiles)。

## [](#migmvn:filtering_resources)[筛选资源](#migmvn:filtering_resources)

Maven有一个称为的阶段，默认情况下`process-
resources`目标已`resources:resources`绑定到该阶段。这为构建作者提供了对各种文件（例如Web资源，打包的属性文件等）执行变量替换的机会。

Gradle的Java插件提供了`processResources`执行相同操作的任务。这是一个[复制](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.Copy.html)任务，`src/main/resources`默认情况下将文件从配置的资源目录复制
到输出目录。与任何`Copy`任务一样，您可以对其进行配置以执行[文件过滤](/md/编写构建脚本.md#filtering_files)，[重命名](/md/编写构建脚本.md#sec:renaming_files)和[内容过滤](/md/编写构建脚本.md#sec:filtering_files)。

例如，以下配置将源文件视为[Groovy`SimpleTemplateEngine`](https://docs.groovy-lang.org/docs/next/html/documentation/template-engines.html#_simpletemplateengine)模板，`version`并`buildNumber`为这些模板提供属性：

例子7.通过`processResources`任务过滤资源的内容

`Groovy``Kotlin`

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

请参阅[CopySpec](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/file/CopySpec.html)的API文档以查看所有可用选项。

## [](#migmvn:integration_tests)[配置集成测试](#migmvn:integration_tests)

许多Maven的建立某种形式的一体化集成测试，它的Maven通过一组额外的阶段的支持：`pre-integration-
test`，`integration-test`，`post-integration-
test`，和`verify`。它还使用Failsafe插件代替Surefire，以便失败的集成测试不会自动使构建失败（因为您可能需要清理资源，例如正在运行的应用程序服务器）。

如我们[在Java和JVM项目中测试](/md/在Java和JVM项目中进行测试.md#sec:configuring_java_integration_tests)章节中所述，此行为很容易在带有源集的Gradle中复制。然后，您可以使用[Task.finalizedBy（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Task.html#org.gradle.api.Task:finalizedBy\(java.lang.Object\[\]\))将清理任务（例如关闭测试服务器的清理任务）配置为始终在集成测试之后运行，而不管它们是否成功。

如果您确实不希望集成测试使构建失败，那么可以使用Java测试一章的“[测试执行”](/md/在Java和JVM项目中进行测试.md#sec:test_execution)部分中描述的[Test.ignoreFailures](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.testing.Test.html#org.gradle.api.tasks.testing.Test:ignoreFailures)设置。[](/md/在Java和JVM项目中进行测试.md#sec:test_execution)

源集还为您在集成测试中放置源文件的位置提供了很大的灵活性。您可以轻松地将它们保存在与单元测试相同的目录中，或者更可取的是将它们保存在单独的源目录中，例如`src/integTest/java`。要支持其他类型的测试，您只需添加更多源集和[测试](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.testing.Test.html)任务！

## [](#migmvn:common_plugins)[迁移常用插件](#migmvn:common_plugins)

Maven和Gradle共享一种通过插件扩展构建的通用方法。尽管表面上的插件系统有很大不同，但是它们共享许多基于功能的插件，例如：

  * 阴影/阴影

  * 码头

  * Checkstyle

  * JaCoCo

  * AntRun（进一步了解）

为什么这么重要？因为许多插件都依赖于标准Java约定，所以迁移仅是在Gradle中复制Maven插件的配置即可。例如，这是一个简单的Maven
Checkstyle插件配置：

    
    
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

迁移到Gradle时，可以安全地忽略配置块之外的所有内容。在这种情况下，相应的Gradle配置如下所示：

例子8.配置Gradle Checkstyle插件

`Groovy``Kotlin`

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

Checkstyle任务会自动添加为`check`任务的依赖项，其中还包括`test`。如果要确保Checkstyle在测试之前运行，则只需使用mustRunAfter（）方法指定一个顺序即可：

例子9.控制`checkstyle`任务何时运行

`Groovy``Kotlin`

build.gradle

    
    
    test.mustRunAfter checkstyleMain, checkstyleTest

build.gradle.kts

    
    
    tasks {
        test {
            mustRunAfter(checkstyleMain, checkstyleTest)
        }
    }

如您所见，Gradle配置通常比Maven等效配置短得多。您还拥有一个更加灵活的执行模型，因为您不再受Maven固定阶段的约束。

从Maven迁移项目时，请不要忘记源集。与Maven相比，它们通常为处理集成测试或生成的源提供了更优雅的解决方案，因此您应将它们纳入迁移计划中。

### [](#ant_goals)[Ant目标](#ant_goals)

许多Maven构建依赖于AntRun插件来自定义构建，而无需实现自定义Maven插件的开销。Gradle没有等效的插件，因为Ant通过该`ant`对象是Gradle构建中的一等公民。例如，您可以使用Ant的Echo任务，如下所示：

例子10.调用Ant任务

`Groovy``Kotlin`

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

本机还支持Ant属性和文件集。要了解更多信息，请参阅《[从Gradle中使用Ant》](/md/从Gradle使用Ant.md#ant)。

╔═════════════════════════════  

仅[创建自定义任务类型](/md/开发自定义Gradle任务类型.md#custom_tasks)来替换Ant为您所做的工作可能更简单，更简洁。然后，您可以更轻松地从[增量构建](/md/处理任务.md#sec:up_to_date_checks)和其他有用的Gradle功能中受益。  
  
╚═════════════════════════════    
  
## [](#migmvn:unnecessary_plugins)[了解您不需要哪些插件](#migmvn:unnecessary_plugins)

值得记住的是，Gradle版本通常比Maven版本更易于扩展和自定义。在这种情况下，这意味着您可能不需要Gradle插件来替换Maven。例如，Maven
Enforcer插件允许您控制依赖项版本和环境因素，但是可以在常规Gradle构建脚本中轻松配置这些内容。

## [](#migmvn:custom_plugins)[处理不常见和自定义的插件](#migmvn:custom_plugins)

您可能会遇到在Gradle中没有对应版本的Maven插件，特别是如果您或组织中的某人已编写了自定义插件。这种情况取决于您了解Gradle（以及可能的Maven）的工作方式，因为您通常必须编写自己的插件。

为了进行迁移，Maven插件有两种主要类型：

  * 那些使用Maven项目对象的对象。

  * 那些没有。

为什么这很重要？因为如果使用后者之一，则可以轻松地将其重新实现为[自定义Gradle任务类型](/md/开发自定义Gradle任务类型.md#custom_tasks)。只需定义与mojo参数相对应的任务输入和输出，然后将执行逻辑转换为任务动作即可。

如果插件依赖于Maven项目，那么您将不得不重写它。不要首先考虑Maven插件的工作原理，而要看看它试图解决的问题。然后尝试解决如何在Gradle中解决该问题。您可能会发现这两个构建模型之间的差异足以使Maven插件代码“转录”到Gradle插件中才有效。从好的方面来说，该插件可能比原始的Maven插件更容易编写，因为Gradle具有更丰富的构建模型和API。

如果确实需要通过构建脚本或插件来实现自定义逻辑，请查看[与插件开发相关](https://gradle.org/guides/?q=Plugin%20Development)的[指南](https://gradle.org/guides/?q=Plugin%20Development)。另外，请务必熟悉Gradle的[Groovy
DSL参考](https://docs.gradle.org/6.7.1/dsl/)，该[参考](https://docs.gradle.org/6.7.1/dsl/)提供了有关您将使用的API的全面文档。它详细介绍了标准配置块（以及支持他们的对象），系统（核心类型`Project`，`Task`等等），和一组标准的任务类型。主要的入口点是[Project](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.Project.html)接口，因为它是支持构建脚本的顶级对象。

## [](#further_reading)[进一步阅读](#further_reading)

本章涵盖了将Maven构建迁移到Gradle的主要主题。剩下的就是迁移期间或迁移之后可能有用的其他一些方面：

  * 了解如何配置Gradle的[构建环境](/md/Gradle环境搭建.md#build_environment)，包括用于运行Gradle的JVM设置

  * 了解如何[有效构建构建](/md/组织Gradle项目.md#organizing_gradle_projects)

  * [配置Gradle的日志记录](/md/使用记录.md#logging)并在构建中使用它

最后，本指南仅涉及Gradle的一些功能，我们鼓励您从用户手册的其他章节以及[分步示例中](https://docs.gradle.org/6.7.1/samples/index.html)学习其余内容。

