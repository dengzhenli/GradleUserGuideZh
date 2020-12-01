# 在Swift项目中进行测试


内容

  * [基础知识](#基础知识)
  * [测试执行](#测试执行)
  * [测试过滤](#测试过滤)
  * [测试报告](#测试报告)

在本地生态系统中进行测试是一个丰富的主题。有许多不同的测试库和框架，以及许多不同类型的测试。无论它们是频繁执行还是不频繁执行，所有这些都需要成为构建的一部分。本章专门说明Gradle如何处理内部版本之间以及内部内部的不同要求，并广泛介绍了Gradle如何与macOS和Linux上的XCTest集成。

它解释了：-控制测试运行方式的方法（测试执行）-如何选择要运行的特定测试（测试过滤）-生成了哪些测试报告以及如何影响过程（测试报告）-Gradle如何找到要运行的测试运行（测试检测）

但是首先，我们看一下Gradle中本机测试的基础。

## [基础知识](#基础知识)

Gradle支持与Swift语言的XCTest测试框架进行深度集成，并围绕[XCTest](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html)任务类型展开。这将在macOS上使用[XcodeXCTest](https://developer.apple.com/documentation/xctest)或在Linux上使用[开源Swift核心库替代方案](https://github.com/apple/swift-corelibs-xctest)运行一系列测试用例，并整理结果。然后，您可以通过[TestReport](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.testing.TestReport.html)任务类型的实例将这些结果转换为报告。

为了进行操作，[XCTest](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html)任务类型需要三条信息：-在哪里找到构建的可测试包（在macOS上）或可执行文件（在Linux上）（属性：[XCTest.getTestInstalledDirectory（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html#org.gradle.nativeplatform.test.xctest.tasks.XCTest:testInstallDirectory)）-用于执行包的运行脚本或可执行文件（属性：[XCTest.getRunScriptFile（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html#org.gradle.nativeplatform.test.xctest.tasks.XCTest:runScriptFile)）-执行捆绑或可执行文件的工作目录（属性：[XCTest.getWorkingDirectory（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html#org.gradle.nativeplatform.test.xctest.tasks.XCTest:workingDirectory)）

使用[XCTest插件时](https://docs.gradle.org/6.7.1/userguide/xctest_plugin.html)，将自动获得以下内容：-用于配置测试组件及其变体的[SwiftXCTestSuite](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.SwiftXCTestSuite.html)`xctest`类型的专用扩展-
运行那些单元测试的[XCTest](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html)类型的任务-
与主程序链接的可测试包或可执行文件组件的目标文件[](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.SwiftXCTestSuite.html)`xcTest`[](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html)

测试插件会适当配置所需的信息。此外，它们还将`xcTest`或`run`任务附加到`check`生命周期任务。它还创建`testImplementation`依赖项配置。只能将测试编译，链接和运行时所需的依赖项添加到此配置中。该`xctest`脚本块的行为类似于一个`application`或`library`脚本块。

该[XCTest](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html)任务有许多配置选项。在本章的其余部分中，我们将讨论很多。

## [测试执行](#测试执行)

Gradle在单独的（“分叉”）过程中执行测试。

您可以通过[XCTest](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html)任务上的几个属性来控制如何启动测试过程，包括以下内容：

`ignoreFailures` -默认值：false

    

如果此属性为`true`，则在测试完成后，即使其中一些失败，Gradle也会继续进行项目的构建。请注意，默认情况下，无论使用哪种设置，两种任务类型始终执行它检测到的每个测试。

`testLogging` -默认：未设置

    

此属性表示一组选项，用于控制记录哪些测试事件以及记录在哪个级别。您还可以通过此属性配置其他日志记录行为。设置[TestLoggingContainer](https://docs.gradle.org/6.7.1/javadoc/org/gradle/api/tasks/testing/logging/TestLoggingContainer.html)以获得更多详细信息。

有关所有可用配置选项的详细信息，请参见[XCTest](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html)。

## [测试过滤](#测试过滤)

运行测试套件的子集是常见的要求，例如，当您修复错误或开发新的测试用例时。Gradle为此提供了过滤。您可以根据以下条件选择要运行的测试：

  * 一个简单的类名或方法名，例如`SomeTest`，`SomeTest.someMethod`

  * '*'通配符匹配

您可以在构建脚本中或通过`--tests`命令行选项启用筛选。这是每次构建运行时都会应用的一些过滤器的示例：

例子1.在每个版本上过滤测试

`Groovy``Kotlin`

build.gradle

    
    
    xctest {
        binaries.configureEach {
            runTask.get().configure {
                // include all tests from test class
                filter.includeTestsMatching "SomeIntegTest.*" // or `"Testing.SomeIntegTest.*"` on macOS
            }
        }
    }

build.gradle.kts

    
    
    xctest {
        binaries.configureEach {
            runTask.get().filter.includeTestsMatching("SomeIntegTest.*") // or `"Testing.SomeIntegTest.*"` on macOS
        }
    }

有关在构建脚本中声明过滤器的更多详细信息和示例，请参见TestFilter参考。

命令行选项对于执行单个测试方法特别有用。也可以提供多个`--tests`选项，所有选项都会生效。以下各节提供了几个使用命令行选项的示例。

╔═════════════════════════════  
目前，测试过滤仅支持XCTest兼容过滤器。这意味着同一过滤器在macOS和Linux之间会有所不同。在macOS上，捆绑软件的基本名称需要添加到过滤器的前面，例如`TestBundle.SomeTest`，有关有效过滤模式的更多信息，`TestBundle.SomeTest.someMethod`
请参见下面的“[简单名称模式”](#测试过滤_simple_name_pattern)部分。  
╚═════════════════════════════    
  
以下部分介绍简单类/方法名称的特殊情况。

### [简单名称模式](#测试过滤_simple_name_pattern)

Gradle支持简单的类名，或类名+方法名的测试过滤。例如，以下命令行运行测试用例中的所有测试或仅运行其中一个`SomeTestClass`测试：

    
    
    # Executes all tests in SomeTestClass
    gradle xcTest --tests SomeTestClass
    # or `gradle xcTest --tests TestBundle.SomeTestClass` on macOS
    
    # Executes a single specified test in SomeTestClass
    gradle xcTest --tests TestBundle.SomeTestClass.someSpecificMethod
    # or `gradle xcTest --tests TestBundle.SomeTestClass.someSpecificMethod` on macOS

您还可以将在命令行中定义的过滤器与[连续构建结合使用，](/md/命令行界面_md#持续构建)以在每次对生产或测试源文件进行更改后立即重新执行测试的子集。每当更改触发测试运行时，以下命令将执行“
SomeTestClass”测试类中的所有测试：

    
    
    gradle test --continuous --tests SomeTestClass

## [测试报告](#测试报告)

该[XCTest](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.xctest.tasks.XCTest.html)任务默认生成的结果如下：

  * HTML测试报告

  * XML测试结果的格式与Ant JUnit报告任务兼容-许多其他工具（例如CI服务器）都支持该格式

  * `XCTest`任务用于生成其他格式的结果的有效二进制格式

在大多数情况下，您将使用标准HTML报告，该报告会自动包含`XCTest`任务的结果。

还有一种独立的[TestReport](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.testing.TestReport.html)任务类型，可用于生成自定义HTML测试报告。它所需要的只是一个值`destinationDir`以及要包含在报告中的测试结果。这是一个示例，可为所有子项目的单元测试生成组合报告：

例子2.合并所有子项目的测试报告

`Groovy``Kotlin`

buildSrc / src / main / groovy / myproject.xctest-conventions.gradle

    
    
    plugins {
        id 'xctest'
    }
    
    xctest {
        binaries.configureEach {
            runTask.get().configure {
                // Disable the test report for the individual test task
                reports.html.enabled = false
            }
        }
    }
    
    // Share the test report data to be aggregated for the whole project
    configurations {
        binaryTestResultsElements {
            canBeResolved = false
            canBeConsumed = true
            attributes {
                attribute(Category.CATEGORY_ATTRIBUTE, objects.named(Category, Category.DOCUMENTATION))
                attribute(DocsType.DOCS_TYPE_ATTRIBUTE, objects.named(DocsType, 'test-report-data'))
            }
            tasks.withType(XCTest) {
                outgoing.artifact(it.binaryResultsDirectory)
            }
        }
    }

build.gradle

    
    
    // A resolvable configuration to collect test reports data
    configurations {
        testReportData {
            canBeResolved = true
            canBeConsumed = false
            attributes {
                attribute(Category.CATEGORY_ATTRIBUTE, objects.named(Category, Category.DOCUMENTATION))
                attribute(DocsType.DOCS_TYPE_ATTRIBUTE, objects.named(DocsType, 'test-report-data'))
            }
        }
    }
    
    dependencies {
        testReportData project(':core')
        testReportData project(':util')
    }
    
    tasks.register('testReport', TestReport) {
        destinationDir = file("$buildDir/reports/allTests")
        // Use test results from testReportData configuration
        testResultDirs.from(configurations.testReportData)
    }

buildSrc/src/main/kotlin/myproject.xctest-conventions.gradle.kts

    
    
    plugins {
        id("xctest")
    }
    
    extensions.configure<SwiftXCTestSuite>() {
        binaries.configureEach {
            // Disable the test report for the individual test task
            runTask.get().reports.html.isEnabled = false
        }
    }
    
    configurations.create("binaryTestResultsElements") {
        isCanBeResolved = false
        isCanBeConsumed = true
        attributes {
            attribute(Category.CATEGORY_ATTRIBUTE, objects.named(Category.DOCUMENTATION))
            attribute(DocsType.DOCS_TYPE_ATTRIBUTE, objects.named("test-report-data"))
        }
        tasks.withType<XCTest>() {
            outgoing.artifact(binaryResultsDirectory)
        }
    }

build.gradle.kts

    
    
    val testReportData by configurations.creating {
        isCanBeResolved = true
        isCanBeConsumed = false
        attributes {
            attribute(Category.CATEGORY_ATTRIBUTE, objects.named(Category.DOCUMENTATION))
            attribute(DocsType.DOCS_TYPE_ATTRIBUTE, objects.named("test-report-data"))
        }
    }
    
    dependencies {
        testReportData(project(":core"))
        testReportData(project(":util"))
    }
    
    tasks.register<TestReport>("testReport") {
        destinationDir = file("$buildDir/reports/allTests")
    
        // Use test results from testReportData configuration
        (getTestResultDirs() as ConfigurableFileCollection).from(testReportData)
    }

在此示例中，我们使用约定插件`myproject.xctest-
conventions`将项目的测试结果暴露给Gradle的[变体感知依赖管理引擎](/md/使用变体属性.md)。

该插件声明了一个耗材`binaryTestResultsElements`配置，该配置代表了该`test`任务的二进制测试结果。在聚合项目的构建文件中，我们声明`testReportData`配置并依赖于要从中聚合结果的所有项目。Gradle将从每个子项目中自动选择二进制测试结果变量，而不是从项目的jar文件中选择。最后，我们添加了一个`testReport`任务，该任务汇总了来自`testResultsDirs`属性的测试结果，其中包含从`testReportData`配置中解析的所有二进制测试结果。

您应该注意，[TestReport](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.testing.TestReport.html)类型组合了多个测试任务的结果，并且需要汇总单个测试类的结果。这意味着给定的测试类由多个测试任务执行，然后测试报告将包括该类的执行，但是很难区分该类的各个执行及其输出。

