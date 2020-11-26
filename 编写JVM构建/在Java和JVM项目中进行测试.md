# 在Java和JVM项目中进行测试

内容

* [基础知识](#sec:java_testing_basics)
* [测试执行](#sec:test_execution)
* [测试过滤](#test_filtering)
* [测试报告](#test_reporting)
* [测试检测](#sec:test_detection)
* [测试分组](#test_grouping)
* [使用JUnit 5](#using_junit5)
* [TestNG中的测试执行顺序](#test_execution_order)
* [配置集成测试](#sec:configuring_java_integration_tests)
* [测试Java模块](#sec:java_testing_modular)
* [跳过测试](#sec:skipping_java_tests)
* [强制测试运行](#sec:forcing_java_tests_to_run)
* [运行测试时进行调试](#sec:debugging_java_tests)
* [使用测试治具](#sec:java_test_fixtures)

在JVM上进行测试是一个很丰富的主题。有许多不同的测试库和框架，以及许多不同类型的测试。无论它们是频繁执行还是不频繁执行，所有这些都需要成为构建的一部分。本章致力于解释Gradle如何处理内部版本之间以及内部内部的不同需求，并广泛介绍了Gradle如何与两个最常见的测试框架[JUnit](https://junit.org/)和[TestNG](https://testng.org/)集成。

它说明：

* 控制测试运行方式的方法（[测试执行](#sec:test_execution)）
* 如何选择要运行的特定测试（[测试过滤](#test_filtering)）
* 生成什么测试报告以及如何影响过程（[测试报告](#test_reporting)）
* Gradle如何找到要运行的[测试](#sec:test_detection)（[测试检测](#sec:test_detection)）
* 如何利用主要框架的机制将测试分组在一起（[测试分组](#test_grouping)）

但是首先，我们看一下Gradle中JVM测试的基础。

## [](#sec:java_testing_basics)[基础知识](#sec:java_testing_basics)

所有JVM测试都围绕一种任务类型：[Test]()。这将使用任何受支持的测试库（JUnit，JUnit Platform或TestNG）运行一系列测试用例，并整理结果。然后，您可以通过[TestReport]()任务类型的实例将这些结果转换为报告。

为了进行操作，`Test`任务类型仅需要两条信息：

* 在哪里可以找到已编译的测试类（属性：[Test.getTestClassesDirs（）]()）
* 执行类路径，其中应包括被测类以及您正在使用的测试库（属性：[Test.getClasspath（）]()）

当您使用JVM语言插件（例如[Java插件）时]()，您将自动获得以下信息：

* `test`用于单元测试的专用源集
* 一个`test`类型的任务`Test`运行这些单元测试

JVM语言插件使用源集来配置具有适当执行类路径和包含已编译测试类的目录的任务。另外，他们将`test`任务附加到`check`[生命周期任务]()。

这也是考虑到值得铭记的是，`test`源集合自动创建[相应的依赖关系配置]()\-其中最有用的是`testImplementation`和`testRuntimeOnly`\-该插件扎入`test`任务的类路径。

在大多数情况下，您要做的就是配置适当的编译和运行时依赖项，并将所有必要的配置添加到`test`任务中。以下示例显示了一个简单的设置，该设置使用JUnit 4.x，并将测试JVM的最大堆大小更改为1 GB：

示例1.“测试”任务的基本配置

`Groovy``Kotlin`

build.gradle

dependencies \{ testImplementation 'junit:junit:4.13' \} test \{ useJUnit\(\) maxHeapSize = '1G' \}

 

1

dependencies \{

2

 testImplementation 'junit:junit:4.13'

3

\}

4

5

test \{

6

 useJUnit\(\)

7

8

 maxHeapSize \= '1G'

9

\}

该[测试]()任务有很多通用的配置选项，以及一些框架，具体的，你可以在找到描述[JUnitOptions]()，[JUnitPlatformOptions]()和[TestNGOptions]()。在本章的其余部分中，我们将讨论很多。

如果要`Test`使用自己的测试类集来设置自己的任务，那么最简单的方法是创建自己的源集和`Test`任务实例，如[配置集成测试中](#sec:configuring_java_integration_tests)所示。

## [](#sec:test_execution)[测试执行](#sec:test_execution)

Gradle在独立于主构建过程的单独（“分叉”）JVM中执行测试。这样可以防止类路径污染和构建过程中过多的内存消耗。它还允许您使用与构建使用的JVM参数不同的JVM参数运行测试。

您可以通过`Test`任务的多个属性来控制如何启动测试过程，包括以下内容：

`maxParallelForks`—默认值：1

通过将此属性设置为大于1的值，可以并行运行测试。这可能会使测试套件更快地完成，尤其是在多核CPU上运行它们时。使用并行测试执行时，请确保您的测试正确地相互隔离。与文件系统交互的测试特别容易发生冲突，从而导致间歇性的测试失败。

您的测试可以通过使用`org.gradle.test.worker`属性值来区分并行测试过程，该属性值对于每个过程都是唯一的。您可以将其用于任何所需的内容，但是对于文件名和其他资源标识符尤其有用，它可以防止我们刚才提到的那种冲突。

`forkEvery`—默认值：0（无最大值）

此属性指定Gradle在处置之前应创建的新测试类的最大数量。这主要用作管理泄漏测试或框架的静态状态，这些静态状态无法在测试之间清除或重置。

**警告：较低的值（非0）会严重损害测试的性能**

`ignoreFailures`—默认值：false

如果此属性为`true`，则在测试完成后，即使其中一些失败，Gradle也会继续进行项目的构建。请注意，默认情况下`Test`，无论此设置如何，任务始终执行其检测到的每个测试。

`failFast`—（自Gradle 4.6起）默认值：false

`true`如果您希望构建失败并在其中一项测试失败后立即完成，则将其设置为。当您具有长期运行的测试套件时，这可以节省大量时间，并且在连续集成服务器上运行构建时特别有用。如果在运行所有测试之前构建失败，则测试报告仅包括已成功完成或未成功完成的测试结果。

您还可以通过使用`--fail-fast`命令行选项来启用此行为。

`testLogging`—默认值：_未设置_

此属性表示一组选项，用于控制记录哪些测试事件以及在什么级别进行记录。您还可以通过此属性配置其他日志记录行为。有关更多详细信息，请参见[TestLoggingContainer]()。

有关所有可用配置选项的详细信息，请参见[测试]()。

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-note"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><div class="paragraph"><p style="font-size: 1rem;"><font><font>如果配置不正确，测试过程可能会意外退出。</font><font>例如，如果Java可执行文件不存在或提供了无效的JVM参数，则测试过程将无法启动。</font><font>同样，如果测试对测试过程进行了编程更改，这也会导致意外失败。</font></font></p></div><div class="paragraph"><p style="font-size: 1rem;"><font><font>例如，如果</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);"><a href="https://docs.oracle.com/javase/8/docs/api/java/lang/SecurityManager.html" style="color:rgb(29, 162, 189);text-decoration:none;">SecurityManager</a></code><font><font>在测试中修改了a<span>&nbsp;</span></font><font>，则可能会出现问题</font><font>，因为Gradle的内部消息传递取决于反射和套接字通信，如果安全管理器上的权限发生更改，则可能会中断该通信。</font><font>在这种特殊情况下，您应该</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">SecurityManager</code><font><font>在测试后</font><font>还原原始文件</font><font>，以便gradle测试工作程序进程可以继续运行。</font></font></p></div></td></tr></tbody></table>

## [](#test_filtering)[测试过滤](#test_filtering)

运行测试套件的子集是常见的要求，例如，当您修复错误或开发新的测试用例时。Gradle提供了两种机制来执行此操作：

* 过滤（首选选项）
* 测试包含/排除

过滤取代了包含/排除机制，但您仍可能在野外遇到后者。

使用Gradle的测试过滤，您可以根据以下条件选择要运行的测试：

* 完全限定的类名或完全限定的方法名，例如`org.gradle.SomeTest`，`org.gradle.SomeTest.someMethod`
* 如果模式以大写字母开头，则为简单的类名或方法名，例如`SomeTest`，`SomeTest.someMethod`（自Gradle 4.7起）
* '\*'通配符匹配

您可以在构建脚本中或通过`--tests`命令行选项启用筛选。这是每次构建运行时都会应用的一些过滤器的示例：

示例2.在构建脚本中过滤测试

`Groovy``Kotlin`

build.gradle

test \{ filter \{ //include specific method in any of the tests includeTestsMatching "\*UiCheck" //include all tests from package includeTestsMatching "org.gradle.internal.\*" //include all integration tests includeTestsMatching "\*IntegTest" \} \}

 

1

test \{

2

 filter \{

3

 //include specific method in any of the tests

4

 includeTestsMatching "\*UiCheck"

5

6

 //include all tests from package

7

 includeTestsMatching "org.gradle.internal.\*"

8

9

 //include all integration tests

10

 includeTestsMatching "\*IntegTest"

11

 \}

12

\}

有关在构建脚本中声明过滤器的更多详细信息和示例，请参见[TestFilter]()参考。

命令行选项对于执行单个测试方法特别有用。使用时`--tests`，请注意仍会尊重构建脚本中声明的包含。也可以提供多个`--tests`选项，所有选项都会生效。以下各节提供了几个使用命令行选项的示例。

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-note"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><div class="paragraph"><p style="font-size: 1rem;"><font><font>并不是所有的测试框架都可以很好地与过滤一起使用。</font><font>某些高级的综合测试可能不完全兼容。</font><font>但是，绝大多数测试和用例都可以与Gradle的过滤机制完美配合。</font></font></p></div></td></tr></tbody></table>

以下两节介绍简单类/方法名称和完全限定名称的特殊情况。

### [](#simple_name_pattern)[简单名称模式](#simple_name_pattern)

从4.7开始，Gradle将以大写字母开头的模式作为简单的类名或类名+方法名。例如，以下命令行运行测试用例中的所有`SomeTestClass`测试，也可以只运行其中的一个，而不管它位于哪个程序包中：

\# Executes all tests in SomeTestClass gradle test --tests SomeTestClass # Executes a single specified test in SomeTestClass gradle test --tests SomeTestClass.someSpecificMethod gradle test --tests SomeTestClass.\*someMethod\*

 

1

\# Executes all tests in SomeTestClass

2

gradle test \--tests SomeTestClass

3

4

\# Executes a single specified test in SomeTestClass

5

gradle test \--tests SomeTestClass.someSpecificMethod

6

7

gradle test \--tests SomeTestClass.\*someMethod\*

### [](#full_qualified_name_pattern)[完全限定的名称模式](#full_qualified_name_pattern)

在4.7之前，或者如果模式不是以大写字母开头，则Gradle会将模式视为完全合格。因此，如果要使用测试类名称而不考虑其包，请使用`--tests *.SomeTestClass`。这里还有更多示例：

\# specific class gradle test --tests org.gradle.SomeTestClass # specific class and method gradle test --tests org.gradle.SomeTestClass.someSpecificMethod # method name containing spaces gradle test --tests "org.gradle.SomeTestClass.some method containing spaces" # all classes at specific package \(recursively\) gradle test --tests 'all.in.specific.package\*' # specific method at specific package \(recursively\) gradle test --tests 'all.in.specific.package\*.someSpecificMethod' gradle test --tests '\*IntegTest' gradle test --tests '\*IntegTest\*ui\*' gradle test --tests '\*ParameterizedTest.foo\*' # the second iteration of a parameterized test gradle test --tests '\*ParameterizedTest.\*\[2\]'

 

1

\# specific class

2

gradle test \--tests org.gradle.SomeTestClass

3

4

\# specific class and method

5

gradle test \--tests org.gradle.SomeTestClass.someSpecificMethod

6

7

\# method name containing spaces

8

gradle test \--tests "org.gradle.SomeTestClass.some method containing spaces"

9

10

\# all classes at specific package \(recursively\)

11

gradle test \--tests 'all.in.specific.package\*'

12

13

\# specific method at specific package \(recursively\)

14

gradle test \--tests 'all.in.specific.package\*.someSpecificMethod'

15

16

gradle test \--tests '\*IntegTest'

17

18

gradle test \--tests '\*IntegTest\*ui\*'

19

20

gradle test \--tests '\*ParameterizedTest.foo\*'

21

22

\# the second iteration of a parameterized test

23

gradle test \--tests '\*ParameterizedTest.\*\[2\]'

请注意，通配符'\*'对'。'没有特殊的理解。包装分离器。它是纯粹基于文本的。因此`--tests *.SomeTestClass`将匹配任何程序包，无论其“深度”如何。

您还可以将在命令行中定义的过滤器与[连续构建结合使用，]()以在每次对生产或测试源文件进行更改后立即重新执行测试的子集。每当更改触发测试运行时，以下命令将执行“ com.mypackage.foo”包或子包中的所有测试：

gradle test --continuous --tests "com.mypackage.foo.\*"

 

1

gradle test \--continuous \--tests "com.mypackage.foo.\*"

## [](#test_reporting)[测试报告](#test_reporting)

该`Test`任务默认情况下会生成以下结果：

* HTML测试报告
* XML测试结果的格式与Ant JUnit报告任务兼容-许多其他工具（例如CI服务器）都支持该格式
* `Test`任务用于生成其他格式的结果的有效二进制格式

在大多数情况下，你会用标准的HTML报告，它将自动从结果中工作_的所有_的`Test`任务，即使你明确地添加到构建自己的人。例如，如果您添加了一个`Test`用于集成测试的任务，则如果两个任务都运行，则报告将同时包含单元测试和集成测试的结果。

与许多测试配置选项不同，有几个项目级别的[约定属性会影响测试报告]()。例如，您可以像这样更改测试结果和报告的目的地：

例子3.更改默认的测试报告和结果目录

`Groovy``Kotlin`

build.gradle

reporting.baseDir = "my-reports" testResultsDirName = "\$buildDir/my-test-results" task showDirs \{ doLast \{ logger.quiet\(rootDir.toPath\(\).relativize\(project.reportsDir.toPath\(\)\).toString\(\)\) logger.quiet\(rootDir.toPath\(\).relativize\(project.testResultsDir.toPath\(\)\).toString\(\)\) \} \}

 

1

reporting.baseDir \= "my-reports"

2

testResultsDirName \= "\$buildDir/my-test-results"

3

4

task showDirs \{

5

 doLast \{

6

 logger.quiet\(rootDir.toPath\(\).relativize\(project.reportsDir.toPath\(\)\).toString\(\)\)

7

 logger.quiet\(rootDir.toPath\(\).relativize\(project.testResultsDir.toPath\(\)\).toString\(\)\)

8

 \}

9

\}

输出**`gradle -q showDirs`**

\> gradle -q showDirs 我的报告 建立/我的测试结果

单击链接到约定属性以获取更多详细信息。

还有一种独立的[TestReport]()任务类型，可用于生成自定义HTML测试报告。它所需要的只是一个值`destinationDir`以及要包含在报告中的测试结果。这是一个示例，可为所有子项目的单元测试生成组合报告：

例子4.为子项目创建一个单元测试报告

`Groovy``Kotlin`

buildSrc / src / main / groovy / myproject.java-conventions.gradle

plugins \{ id 'java' \} // Disable the test report for the individual test task test \{ reports.html.enabled = false \} // Share the test report data to be aggregated for the whole project configurations \{ binaryTestResultsElements \{ canBeResolved = false canBeConsumed = true attributes \{ attribute\(Category.CATEGORY\_ATTRIBUTE, objects.named\(Category, Category.DOCUMENTATION\)\) attribute\(DocsType.DOCS\_TYPE\_ATTRIBUTE, objects.named\(DocsType, 'test-report-data'\)\) \} outgoing.artifact\(test.binaryResultsDirectory\) \} \}

 

1

plugins \{

2

 id 'java'

3

\}

4

5

// Disable the test report for the individual test task

6

test \{

7

 reports.html.enabled \= false

8

\}

9

10

// Share the test report data to be aggregated for the whole project

11

configurations \{

12

 binaryTestResultsElements \{

13

 canBeResolved \= false

14

 canBeConsumed \= true

15

 attributes \{

16

 attribute\(Category.CATEGORY\_ATTRIBUTE, objects.named\(Category, Category.DOCUMENTATION\)\)

17

 attribute\(DocsType.DOCS\_TYPE\_ATTRIBUTE, objects.named\(DocsType, 'test-report-data'\)\)

18

 \}

19

 outgoing.artifact\(test.binaryResultsDirectory\)

20

 \}

21

\}

build.gradle

// A resolvable configuration to collect test reports data configurations \{ testReportData \{ canBeResolved = true canBeConsumed = false attributes \{ attribute\(Category.CATEGORY\_ATTRIBUTE, objects.named\(Category, Category.DOCUMENTATION\)\) attribute\(DocsType.DOCS\_TYPE\_ATTRIBUTE, objects.named\(DocsType, 'test-report-data'\)\) \} \} \} dependencies \{ testReportData project\(':core'\) testReportData project\(':util'\) \} tasks.register\('testReport', TestReport\) \{ destinationDir = file\("\$buildDir/reports/allTests"\) // Use test results from testReportData configuration testResultDirs.from\(configurations.testReportData\) \}

 

1

// A resolvable configuration to collect test reports data

2

configurations \{

3

 testReportData \{

4

 canBeResolved \= true

5

 canBeConsumed \= false

6

 attributes \{

7

 attribute\(Category.CATEGORY\_ATTRIBUTE, objects.named\(Category, Category.DOCUMENTATION\)\)

8

 attribute\(DocsType.DOCS\_TYPE\_ATTRIBUTE, objects.named\(DocsType, 'test-report-data'\)\)

9

 \}

10

 \}

11

\}

12

13

dependencies \{

14

 testReportData project\(':core'\)

15

 testReportData project\(':util'\)

16

\}

17

18

tasks.register\('testReport', TestReport\) \{

19

 destinationDir \= file\("\$buildDir/reports/allTests"\)

20

 // Use test results from testReportData configuration

21

 testResultDirs.from\(configurations.testReportData\)

22

\}

在此示例中，我们使用约定插件`myproject.java-conventions`将项目的测试结果暴露给Gradle的[变体感知依赖管理引擎]()。

该插件声明了一个耗材`binaryTestResultsElements`配置，该配置代表了该`test`任务的二进制测试结果。在聚合项目的构建文件中，我们声明`testReportData`配置并依赖于要从中聚合结果的所有项目。Gradle将从每个子项目中自动选择二进制测试结果变量，而不是从项目的jar文件中选择。最后，我们添加了一个`testReport`任务，该任务汇总了来自`testResultsDirs`属性的测试结果，其中包含从`testReportData`配置中解析的所有二进制测试结果。

您应该注意，该`TestReport`类型合并了多个测试任务的结果，并且需要汇总各个测试类的结果。这意味着，如果给定的测试类由多个测试任务执行，则测试报告将包括该类的执行，但是很难区分该类的各个执行及其输出。

## [](#sec:test_detection)[测试检测](#sec:test_detection)

默认情况下，Gradle将运行它检测到的所有测试，这是通过检查编译的测试类来完成的。根据所使用的测试框架，此检测使用不同的标准。

对于_JUnit_，Gradle会扫描JUnit 3和4测试类。如果某个类符合以下条件，则将其视为JUnit测试：

* 最终继承自`TestCase`或`GroovyTestCase`
* 带有注释`@RunWith`
* 包含带有注解的方法`@Test`或超类

对于_TestNG_，Gradle扫描使用注释的方法`@Test`。

请注意，不执行抽象类。此外，请注意Gradle会将继承树扫描到测试类路径上的jar文件中。因此，如果这些JAR包含测试类，它们也将运行。

如果你不想使用测试类的检测，你可以通过设置禁用它`scanForTestClasses`的性能[测试]()到`false`。执行此操作时，测试任务仅使用`includes`和`excludes`属性来查找测试类。

如果`scanForTestClasses`为false并且未指定包含或排除模式，则Gradle默认运行任何与模式`**/*Tests.class`和匹配的类，但`**/*Test.class`不包括与匹配的类`**/Abstract*.class`。

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-note"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><div class="paragraph"><p style="font-size: 1rem;"><font><font>对于</font></font><a href="http://junit.org/junit5/docs/current/user-guide" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>JUnit Platform</font></font></a><font><font>，仅</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">includes</code><font><font>和</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">excludes</code><font><font>用于过滤测试类-</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">scanForTestClasses</code><font><font>无效。</font></font></p></div></td></tr></tbody></table>

## [](#test_grouping)[测试分组](#test_grouping)

JUnit，JUnit Platform和TestNG允许对测试方法进行复杂的分组。

JUnit 4.8引入了将JUnit 4测试类和方法分组的类别的概念。\[[1](#_footnotedef_1 "查看脚注。")\][Test.useJUnit（org.gradle.api.Action）]()允许您指定要包括和排除的JUnit类别。例如，以下配置在任务中包括测试`CategoryA`，但不包括测试：`CategoryB``test`

例子5. JUnit类别

`Groovy``Kotlin`

build.gradle

test \{ useJUnit \{ includeCategories 'org.gradle.junit.CategoryA' excludeCategories 'org.gradle.junit.CategoryB' \} \}

 

1

test \{

2

 useJUnit \{

3

 includeCategories 'org.gradle.junit.CategoryA'

4

 excludeCategories 'org.gradle.junit.CategoryB'

5

 \}

6

\}

[JUnit Platform](http://junit.org/junit5/docs/current/user-guide)引入了[标记](http://junit.org/junit5/docs/current/user-guide/#writing-tests-tagging-and-filtering)来替换类别。您可以通过[Test.useJUnitPlatform（org.gradle.api.Action）]()来指定包含/排除的标签，如下所示：

例子6. JUnit平台标签

`Groovy``Kotlin`

build.gradle

test \{ useJUnitPlatform \{ includeTags 'fast' excludeTags 'slow' \} \}

 

1

test \{

2

 useJUnitPlatform \{

3

 includeTags 'fast'

4

 excludeTags 'slow'

5

 \}

6

\}

TestNG框架使用测试组的概念来达到类似的效果。\[[2](#_footnotedef_2 "查看脚注。")\]您可以通过[Test.useTestNG（org.gradle.api.Action）]()设置配置在测试执行过程中包括或排除哪些测试组，如下所示：

例子7.分组TestNG测试

`Groovy``Kotlin`

build.gradle

test \{ useTestNG \{ excludeGroups 'integrationTests' includeGroups 'unitTests' \} \}

 

1

test \{

2

 useTestNG \{

3

 excludeGroups 'integrationTests'

4

 includeGroups 'unitTests'

5

 \}

6

\}

## [](#using_junit5)[使用JUnit 5](#using_junit5)

[JUnit 5](http://junit.org/junit5)是著名的JUnit测试框架的最新版本。与之前的版本不同，JUnit 5是模块化的，并且由几个模块组成：

JUnit 5 = JUnit平台+ JUnit Jupiter + JUnit Vintage

JUnit平台是在JVM上启动测试框架的基础。JUnit Jupiter是新[编程模型](http://junit.org/junit5/docs/current/user-guide/#writing-tests)和[扩展模型](http://junit.org/junit5/docs/current/user-guide/#extensions)的组合，用于在JUnit 5中编写测试和扩展。JUnit Vintage提供了一种`TestEngine`在平台上运行基于JUnit 3和JUnit 4的测试的功能。

以下代码在中启用JUnit Platform支持`build.gradle`：

例子8.启用JUnit Platform来运行测试

`Groovy``Kotlin`

build.gradle

test \{ useJUnitPlatform\(\) \}

 

1

test \{

2

 useJUnitPlatform\(\)

3

\}

有关更多详细信息，请参见[Test.useJUnitPlatform（）]()。

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-note"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><div class="paragraph"><p style="font-size: 1rem;"><font><font>将JUnit 5与Gradle结合使用存在一些已知的局限性，例如，不会发现静态嵌套类中的测试。</font><font>这些将在Gradle的未来版本中修复。</font><font>如果您发现更多信息，请在</font><a href="https://github.com/gradle/gradle/issues/new" class="bare" style="color:rgb(29, 162, 189);text-decoration:none;"><font>https://github.com/gradle/gradle/issues/new</font></a><font>告诉我们</font></font><a href="https://github.com/gradle/gradle/issues/new" class="bare" style="color:rgb(29, 162, 189);text-decoration:none;"><font></font></a></p></div></td></tr></tbody></table>

### [](#compiling_and_executing_junit_jupiter_tests)[编译和执行JUnit Jupiter测试](#compiling_and_executing_junit_jupiter_tests)

要在Gradle中启用JUnit Jupiter支持，您需要做的就是添加以下依赖项：

例子9. JUnit Jupiter依赖

`Groovy``Kotlin`

build.gradle

dependencies \{ testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0' testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine' \}

 

1

dependencies \{

2

 testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'

3

 testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine'

4

\}

然后，您可以像平常一样将测试用例放入_src / test / java中_，并使用执行它们`gradle test`。

### [](#executing_legacy_tests_with_junit_vintage)[使用JUnit Vintage执行旧式测试](#executing_legacy_tests_with_junit_vintage)

如果要在JUnit Platform上运行JUnit 3/4测试，或者甚至将它们与Jupiter测试混合使用，则应添加额外的JUnit Vintage Engine依赖项：

例子10. JUnit Vintage依赖

`Groovy``Kotlin`

build.gradle

dependencies \{ testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0' testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine' testCompileOnly 'junit:junit:4.13' testRuntimeOnly 'org.junit.vintage:junit-vintage-engine' \}

 

1

dependencies \{

2

 testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'

3

 testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine'

4

 testCompileOnly 'junit:junit:4.13'

5

 testRuntimeOnly 'org.junit.vintage:junit-vintage-engine'

6

\}

这样，您可以用来`gradle test`在JUnit Platform上测试JUnit 3/4测试，而无需重写它们。

### [](#filtering_test_engine)[过滤测试引擎](#filtering_test_engine)

JUnit Platform允许您使用不同的测试引擎。JUnit当前提供了两种`TestEngine`开箱即用的实现：[junit-jupiter-engine](https://junit.org/junit5/docs/current/api/org.junit.jupiter.engine/module-summary.html)和[junit-vintage-engine](https://junit.org/junit5/docs/current/api/org.junit.vintage.engine/module-summary.html)。您还可以`TestEngine`按照[此处所述](https://junit.org/junit5/docs/current/user-guide/#launcher-api-engines-custom)编写和插入自己的实现。

默认情况下，将使用测试运行时类路径上的所有测试引擎。要显式控制特定的测试引擎实现，可以将以下设置添加到构建脚本：

例子11.过滤特定引擎

`Groovy``Kotlin`

build.gradle

test \{ useJUnitPlatform \{ includeEngines 'junit-vintage' // excludeEngines 'junit-jupiter' \} \}

 

1

test \{

2

 useJUnitPlatform \{

3

 includeEngines 'junit-vintage'

4

 // excludeEngines 'junit-jupiter'

5

 \}

6

\}

## [](#test_execution_order)[TestNG中的测试执行顺序](#test_execution_order)

当您使用_testng.xml_文件时，TestNG允许显式控制测试的执行顺序。没有这样的文件（或由[TestNGOptions.getSuiteXmlBuilder（）]()配置的等效文件[）]()，您将无法指定测试执行顺序。但是，您_可以_做的是控制 在下一个测试开始之前是否执行了测试的所有方面，包括其关联的方法`@BeforeXXX`和`@AfterXXX`方法，例如用`@Before/AfterClass`和注释的那些方法`@Before/AfterMethod`。您可以通过将[TestNGOptions.getPreserveOrder（）]()属性设置为来实现`true`。如果你把它设置为`false`，您可能会遇到的情况，其中执行顺序是这样的：`TestA.doBeforeClass()`→交通`TestB.doBeforeClass()`→交通`TestA`测试。

直接使用_testng.xml_文件时，保留测试顺序是默认行为，而Gradle的TestNG集成所使用的[TestNG API](https://jitpack.io/com/github/cbeust/testng/master/javadoc/org/testng/TestNG.html)默认以不可预测的顺序执行测试。\[[3](#_footnotedef_3 "查看脚注。")\]TestNG版本5.14.5引入了保留测试执行顺序的功能。将`preserveOrder`属性设置`true`为较旧的TestNG版本将导致构建失败。

例子12.保留TestNG测试的顺序

`Groovy``Kotlin`

build.gradle

test \{ useTestNG \{ preserveOrder true \} \}

 

1

test \{

2

 useTestNG \{

3

 preserveOrder true

4

 \}

5

\}

该`groupByInstance`属性控制测试是否应按实例而不是按类别分组。在[TestNG的文档](http://testng.org/doc/documentation-main.html#dependencies-with-annotations)详细解释了区别，但本质上，如果你有一个测试方法`A()`依赖于`B()`，每个AB配对，例如，通过例如确保分组`B(1)`\-`A(1)`，在下一配对之前执行。通过按组分组，所有`B()`方法都将运行，然后所有方法都将运行`A()`。

请注意，如果您使用数据提供程序对其进行参数化，则通常只有一个以上的测试实例。此外，TestNG版本6.1引入了按实例对测试进行分组的功能。将`groupByInstances`属性设置`true`为较旧的TestNG版本将导致构建失败。

例子13.按实例分组TestNG测试

`Groovy``Kotlin`

build.gradle

test \{ useTestNG \{ groupByInstances = true \} \}

 

1

test \{

2

 useTestNG \{

3

 groupByInstances \= true

4

 \}

5

\}

### [](#testNgParameterizedReporting)[TestNG参数化方法和报告](#testNgParameterizedReporting)

TestNG支持[参数化测试方法](http://testng.org/doc/documentation-main.html#parameters)，允许使用不同的输入多次执行特定的测试方法。Gradle在其测试方法执行的报告中包括参数值。

给定名为的参数化测试方法`aTestMethod`，该方法具有两个参数，它将以name报告`aTestMethod(toStringValueOfParam1, toStringValueOfParam2)`。这使得识别特定迭代的参数值变得容易。

## [](#sec:configuring_java_integration_tests)[配置集成测试](#sec:configuring_java_integration_tests)

项目的常见要求是以一种或另一种形式合并集成测试。他们的目的是验证项目的各个部分是否正常工作。与单元测试相比，这通常意味着它们需要特殊的执行设置和依赖性。

将集成测试添加到构建中的最简单方法是采取以下步骤：

1.  为他们创建一个新的[源集]()
2.  将所需的依赖项添加到该源集的适当配置中
3.  为该源集配置编译和运行时类路径
4.  创建任务以运行集成测试

您可能还需要执行一些其他配置，具体取决于集成测试采用的形式。我们将在讨论过程中进行讨论。

让我们从一个实际示例开始，该示例以新的源集为中心在构建脚本中实现前三个步骤`intTest`：

例子14.设置工作中的集成测试

`Groovy``Kotlin`

build.gradle

sourceSets \{ intTest \{ compileClasspath += sourceSets.main.output runtimeClasspath += sourceSets.main.output \} \} configurations \{ intTestImplementation.extendsFrom implementation intTestRuntimeOnly.extendsFrom runtimeOnly \} dependencies \{ intTestImplementation 'junit:junit:4.13' \}

 

1

sourceSets \{

2

 intTest \{

3

 compileClasspath += sourceSets.main.output

4

 runtimeClasspath += sourceSets.main.output

5

 \}

6

\}

7

8

configurations \{

9

 intTestImplementation.extendsFrom implementation

10

 intTestRuntimeOnly.extendsFrom runtimeOnly

11

\}

12

13

dependencies \{

14

 intTestImplementation 'junit:junit:4.13'

15

\}

这将设置一个新的源集`intTest`，该源集将自动创建：

* `intTestImplementation`，`intTestCompileOnly`，`intTestRuntimeOnly`配置（以及[其他一些]()不那么常见的需要）
* 一`compileIntTestJava`，将编译下的所有源文件任务_的src / intTest / JAVA_

该示例还执行以下操作，并非特定集成测试可能需要全部操作：

* 将生产类从`main`源集中添加到集成测试的编译和运行时类路径中-`sourceSets.main.output`是包含编译的生产类和资源的所有目录的[文件集合]()
* 使`intTestImplementation`配置从扩展`implementation`，这意味着所有声明的生产代码依赖项也将成为集成测试的依赖项
* `intTestRuntimeOnly`配置是否相同

在大多数情况下，您希望集成测试可以访问被测类，这就是为什么我们确保在本示例中将它们包括在编译和运行时类路径中的原因。但是某些类型的测试以不同的方式与生产代码交互。例如，您可能具有将应用程序作为可执行文件运行并验证输出的测试。对于Web应用程序，测试可能会通过HTTP与您的应用程序进行交互。由于在这种情况下测试不需要直接访问被测类，因此您无需将生产类添加到测试类路径中。

另一个常见步骤是也通过`intTestImplementation.extendsFrom testImplementation`\-将所有单元测试依赖项附加到集成测试中，但这仅在集成测试要求单元测试具有_全部_或几乎所有相同的依赖项时才有意义。

您还应注意该示例的其他两个方面：

* `+=`允许您向其添加路径和路径集合，`compileClasspath`而`runtimeClasspath`不是覆盖它们
* 如果要使用基于约定的配置（例如）`intTestImplementation`，则_必须_在新的源集_之后_声明依赖项

创建和配置源集会自动设置编译阶段，但是对于运行集成测试没有任何作用。因此，最后一个难题是一个自定义测试任务，该任务使用来自新源集的信息来配置其运行时类路径和测试类：

例子15.定义一个有效的集成测试任务

`Groovy``Kotlin`

build.gradle

task integrationTest\(type: Test\) \{ description = 'Runs integration tests.' group = 'verification' testClassesDirs = sourceSets.intTest.output.classesDirs classpath = sourceSets.intTest.runtimeClasspath shouldRunAfter test \} check.dependsOn integrationTest

 

1

task integrationTest\(type: Test\) \{

2

 description \= 'Runs integration tests.'

3

 group \= 'verification'

4

5

 testClassesDirs \= sourceSets.intTest.output.classesDirs

6

 classpath \= sourceSets.intTest.runtimeClasspath

7

 shouldRunAfter test

8

\}

9

10

check.dependsOn integrationTest

同样，我们正在访问源集以获取相关信息，即，已编译的测试类的位置（`testClassesDirs`属性）以及运行它们时需要在类路径上的内容 `classpath`。

用户通常希望在单元测试之后运行集成测试，因为它们通常运行速度较慢，并且您希望构建在单元测试之前失败而不是在集成测试之后失败。这就是上面的示例添加一个`shouldRunAfter()`声明的原因。相对`mustRunAfter()`于此，它是首选的，以便Gradle在并行执行构建时具有更大的灵活性。

## [](#sec:java_testing_modular)[测试Java模块](#sec:java_testing_modular)

如果您正在[开发Java模块]()，则本章中描述的所有内容仍然适用，并且可以使用任何受支持的测试框架。但是，根据测试执行期间是否需要可用的模块信息以及要强制执行的模块边界，需要考虑一些事项。在这种情况下，经常使用术语“_白盒测试”_（停用或放宽模块边界）和“_黑盒测试”_（已设置模块边界）。白盒测试用于/需要用于单元测试，黑盒测试符合功能或集成测试要求。

样本：[具有集成测试的Java Modules多项目]()

### [](#whitebox_unit_test_execution_on_the_classpath)[在类路径上执行白盒单元测试](#whitebox_unit_test_execution_on_the_classpath)

为模块中的函数或类编写单元测试的最简单设置是在测试执行期间_不_使用模块规范。为此，您只需要以与普通库一样的方式编写测试即可。如果您`module-info.java`的测试源集中（`src/test/java`）中没有文件，则在编译和测试运行时，该源集将被视为传统Java库。这意味着，所有依赖项（包括带有模块信息的Jar）都放在类路径上。好处是，您（或其他）模块的所有内部类都可以在测试中直接访问。对于单元测试，这可能是完全有效的设置，在这里，我们不在乎较大的模块结构，而仅在测试单个功能。

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-note"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><div class="paragraph"><p style="font-size: 1rem;"><font><font>如果您使用的是Eclipse：默认情况下，Eclipse还使用模块修补程序将单元测试作为模块运行（请参见</font></font><a href="#sec:java_testing_modular_patching" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>下文</font></font></a><font><font>）。</font><font>在导入的Gradle项目中，使用Eclipse测试运行器对模块进行单元测试可能会失败。</font><font>然后，您需要在测试运行配置中手动调整类路径/模块路径，或将测试执行委托给Gradle。</font><font>这仅涉及测试执行。</font><font>单元测试的编译和开发在Eclipse中运行良好。</font></font></p></div></td></tr></tbody></table>

### [](#blackbox_integration_testing)[黑盒整合测试](#blackbox_integration_testing)

对于集成测试，您可以选择将测试集本身定义为附加模块。您可以通过将`module-info.java`文件添加到相应的源集（例如`integrationTests/java/module-info.java`）中的方式类似于将主要源变成模块的方式。

您可以[在此处]()找到包含黑盒集成测试的完整示例。

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-note"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><div class="paragraph"><p style="font-size: 1rem;"><font><font>在Eclipse中，</font></font><a href="https://bugs.eclipse.org/bugs/show_bug.cgi?id=520667" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>当前不支持</font></font></a><font><font>在一个项目中编译多个模块</font><font>。</font><font>因此，仅在将测试移至单独的子项目的情况下，此处描述的集成测试（黑盒）设置才能在Eclipse中工作。</font></font></p></div></td></tr></tbody></table>

### [](#sec:java_testing_modular_patching)[白盒测试执行与模块修补](#sec:java_testing_modular_patching)

白盒测试的另一种方法是通过将测试_修补_到被测模块中来保留在模块世界中。这样，模块边界保持不变，但是测试本身成为被测试模块的一部分，然后可以访问模块的内部。

对于哪些用例而言，这是相关的，如何最好地做到这一点是讨论的主题。目前尚无一般最佳方法。因此，目前在Gradle中对此没有特别的支持。

但是，您可以为以下测试设置模块补丁：

* `module-info.java`在您的测试源集中添加一个，它是主要版本的副本，其中`module-info.java`包含测试所需的其他依赖项（例如`requires org.junit.jupiter.api`）。
* 用参数配置`testCompileJava`和`test`任务，以用测试类修补主类，如下所示。

例子16.使用命令行参数测试的补丁模块

`Groovy``Kotlin`

build.gradle

def moduleName = "org.gradle.sample" def patchArgs = \["--patch-module", "\$moduleName=\$\{tasks.compileJava.destinationDirectory.asFile.get\(\).path\}"\] tasks.compileTestJava \{ options.compilerArgs += patchArgs \} tasks.test \{ jvmArgs += patchArgs \}

 

1

def moduleName \= "org.gradle.sample"

2

def patchArgs \= \["--patch-module", "\$moduleName=\$\{tasks.compileJava.destinationDirectory.asFile.get\(\).path\}"\]

3

tasks.compileTestJava \{

4

 options.compilerArgs += patchArgs

5

\}

6

tasks.test \{

7

 jvmArgs += patchArgs

8

\}

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-note"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><div class="paragraph"><p style="font-size: 1rem;"><font><font>如果使用自定义参数进行修补，那么Eclipse和IDEA不会选择这些自定义参数。</font><font>您很可能会在IDE中看到无效的编译错误。</font></font></p></div></td></tr></tbody></table>

## [](#sec:skipping_java_tests)[跳过测试](#sec:skipping_java_tests)

如果要在运行构建时跳过测试，则有几种选择。您可以通过[命令行参数]()或[在构建脚本中进行操作]()。要在命令行上执行此操作，可以使用`-x`或`--exclude-task`选项，如下所示：

gradle build -x测试

这排除了该`test`任务及其_唯一_依赖的任何其他任务，即没有其他任务依赖于同一任务。这些任务不会被Gradle标记为“跳过”，而只会出现在已执行任务的列表中。

通过构建脚本跳过测试可以通过几种方法来完成。一种常见的方法是通过[Task.onlyIf（org.gradle.api.specs.Spec）]()方法使测试执行有条件。`test`如果项目具有名为的属性，以下示例将跳过任务`mySkipTests`：

例子17.根据项目属性跳过单元测试

`Groovy``Kotlin`

build.gradle

test.onlyIf \{ \!project.hasProperty\('mySkipTests'\) \}

 

1

test.onlyIf \{ \!project.hasProperty\('mySkipTests'\) \}

在这种情况下，Gradle会将跳过的测试标记为“跳过”，而不是将其从构建中排除。

## [](#sec:forcing_java_tests_to_run)[强制测试运行](#sec:forcing_java_tests_to_run)

在定义良好的版本中，您可以依靠Gradle仅在测试本身或生产代码发生更改时运行测试。但是，您可能会遇到测试依赖第三方服务或可能会更改但无法在构建中建模的其他情况。

您可以通过清除相关`Test`任务的输出（例如`test`）并再次运行测试来强制测试在这种情况下运行，如下所示：

gradle cleanTest测试

`cleanTest`基于[基础插件]()提供的[任务规则]()。您可以将其用于_任何_任务。

## [](#sec:debugging_java_tests)[运行测试时进行调试](#sec:debugging_java_tests)

在少数情况下，您希望在测试运行时调试代码，如果可以在此时附加调试器，则可能会有所帮助。您可以将[Test.getDebug（）]()属性设置为`true`或使用`--debug-jvm`命令行选项。

启用测试调试后，Gradle将挂起测试过程并监听端口5005。

您还可以在DSL中启用调试，还可以在其中配置其他属性：

测试\{ debugOptions \{ 启用=真 端口= 4455 服务器=真 暂停=真 \} \}

通过这种配置，测试JVM的行为就像传递`--debug-jvm`参数时一样，但是它将在端口4455上侦听。

## [](#sec:java_test_fixtures)[使用测试治具](#sec:java_test_fixtures)

### [](#producing_and_using_test_fixtures_within_a_single_project)[在单个项目中生产和使用测试夹具](#producing_and_using_test_fixtures_within_a_single_project)

测试装置通常用于设置被测代码，或提供旨在促进组件测试的实用程序。`java-test-fixtures`除了`java`或插件，Java项目还可以通过应用插件来启用测试装置支持`java-library`：

例子18.应用Java测试装置插件

`Groovy``Kotlin`

lib / build.gradle

plugins \{ // A Java Library id 'java-library' // which produces test fixtures id 'java-test-fixtures' // and is published id 'maven-publish' \}

 

1

plugins \{

2

 // A Java Library

3

 id 'java-library'

4

 // which produces test fixtures

5

 id 'java-test-fixtures'

6

 // and is published

7

 id 'maven-publish'

8

\}

这将自动创建一个`testFixtures`源集，您可以在其中编写测试装置。测试夹具的配置如下：

* 他们可以看到_主要的_源集类
* _测试_源可以看到_测试夹具_类

例如对于这个主类：

src / main / java / com / acme / Person.java

public class Person \{ private final String firstName; private final String lastName; public Person\(String firstName, String lastName\) \{ this.firstName = firstName; this.lastName = lastName; \} public String getFirstName\(\) \{ return firstName; \} public String getLastName\(\) \{ return lastName; \} // ...

 

1

public class Person \{

2

 private final String firstName;

3

 private final String lastName;

4

5

 public Person\(String firstName, String lastName\) \{

6

 this.firstName \= firstName;

7

 this.lastName \= lastName;

8

 \}

9

10

 public String getFirstName\(\) \{

11

 return firstName;

12

 \}

13

14

 public String getLastName\(\) \{

15

 return lastName;

16

 \}

17

18

 // ...

测试夹具可以写成`src/testFixtures/java`：

src / testFixtures / java / com / acme / Simpsons.java

public class Simpsons \{ private static final Person HOMER = new Person\("Homer", "Simpson"\); private static final Person MARGE = new Person\("Marjorie", "Simpson"\); private static final Person BART = new Person\("Bartholomew", "Simpson"\); private static final Person LISA = new Person\("Elisabeth Marie", "Simpson"\); private static final Person MAGGIE = new Person\("Margaret Eve", "Simpson"\); private static final List\<Person> FAMILY = new ArrayList\<Person>\(\) \{\{ add\(HOMER\); add\(MARGE\); add\(BART\); add\(LISA\); add\(MAGGIE\); \}\}; public static Person homer\(\) \{ return HOMER; \} public static Person marge\(\) \{ return MARGE; \} public static Person bart\(\) \{ return BART; \} public static Person lisa\(\) \{ return LISA; \} public static Person maggie\(\) \{ return MAGGIE; \} // ...

 

1

public class Simpsons \{

2

 private static final Person HOMER \= new Person\("Homer", "Simpson"\);

3

 private static final Person MARGE \= new Person\("Marjorie", "Simpson"\);

4

 private static final Person BART \= new Person\("Bartholomew", "Simpson"\);

5

 private static final Person LISA \= new Person\("Elisabeth Marie", "Simpson"\);

6

 private static final Person MAGGIE \= new Person\("Margaret Eve", "Simpson"\);

7

 private static final List\<Person\> FAMILY \= new ArrayList\<Person\>\(\) \{\{

8

 add\(HOMER\);

9

 add\(MARGE\);

10

 add\(BART\);

11

 add\(LISA\);

12

 add\(MAGGIE\);

13

 \}\};

14

15

 public static Person homer\(\) \{ return HOMER; \}

16

17

 public static Person marge\(\) \{ return MARGE; \}

18

19

 public static Person bart\(\) \{ return BART; \}

20

21

 public static Person lisa\(\) \{ return LISA; \}

22

23

 public static Person maggie\(\) \{ return MAGGIE; \}

24

25

 // ...

### [](#declaring_dependencies_of_test_fixtures)[声明测试夹具的依赖性](#declaring_dependencies_of_test_fixtures)

与[Java库插件]()类似，测试装置公开了API和实现配置：

例子19.声明测试夹具的依赖关系

`Groovy``Kotlin`

lib / build.gradle

dependencies \{ testImplementation 'junit:junit:4.13' // API dependencies are visible to consumers when building testFixturesApi 'org.apache.commons:commons-lang3:3.9' // Implementation dependencies are not leaked to consumers when building testFixturesImplementation 'org.apache.commons:commons-text:1.6' \}

 

1

dependencies \{

2

 testImplementation 'junit:junit:4.13'

3

4

 // API dependencies are visible to consumers when building

5

 testFixturesApi 'org.apache.commons:commons-lang3:3.9'

6

7

 // Implementation dependencies are not leaked to consumers when building

8

 testFixturesImplementation 'org.apache.commons:commons-text:1.6'

9

\}

值得注意的是，如果依赖性是测试夹具的_实现_依赖性，那么_在编译依赖于那些测试夹具_的_测试时_，实现依赖性_不会泄漏_到编译类路径中。这样可以改善关注点分离并更好地避免编译。

### [](#consuming_test_fixtures_of_another_project)[消耗另一个项目的测试装置](#consuming_test_fixtures_of_another_project)

测试装置不限于单个项目。通常，从属项目测试也需要依赖项的测试装置。使用`testFixtures`关键字可以很容易地做到这一点：

例子20.添加对另一个项目的测试装置的依赖

`Groovy``Kotlin`

build.gradle

dependencies \{ implementation\(project\(":lib"\)\) testImplementation 'junit:junit:4.13' testImplementation\(testFixtures\(project\(":lib"\)\)\) \}

 

1

dependencies \{

2

 implementation\(project\(":lib"\)\)

3

4

 testImplementation 'junit:junit:4.13'

5

 testImplementation\(testFixtures\(project\(":lib"\)\)\)

6

\}

### [](#publishing_test_fixtures)[发布测试装置](#publishing_test_fixtures)

使用该`java-test-fixtures`插件的优点之一是发布了测试装置。按照惯例，测试装置将与具有`test-fixtures`分类器的工件一起发布。对于Maven和Ivy，带有该分类器的工件都将与常规工件一起简单发布。但是，如果使用`maven-publish`或`ivy-publish`插件，则测试夹具会作为其他变体发布在[Gradle模块元数据中，](https://github.com/gradle/gradle/blob/master/subprojects/docs/src/docs/design/gradle-module-metadata-1.0-specification.md)并且您可以直接依赖另一个Gradle项目中外部库的测试夹具：

例子21.添加对外部库测试装置的依赖

`Groovy``Kotlin`

build.gradle

dependencies \{ // Adds a dependency on the test fixtures of Gson, however this // project doesn't publish such a thing functionalTest testFixtures\("com.google.code.gson:gson:2.8.5"\) \}

 

1

dependencies \{

2

 // Adds a dependency on the test fixtures of Gson, however this

3

 // project doesn't publish such a thing

4

 functionalTest testFixtures\("com.google.code.gson:gson:2.8.5"\)

5

\}

值得注意的是，如果外部项目_未_发布Gradle Module元数据，则解析将失败，并显示一条错误消息，指出无法找到这样的变体：

输出**`gradle dependencyInsight --configuration functionalTestClasspath --dependency gson`**

\> gradledependencyInsight-配置functionalTestClasspath --dependency gson
 >任务：dependencyInsight com.google.code.gson：gson：2.8.5失败 失败： -无法解析com.google.code.gson：gson：2.8.5。 -无法找到提供所需功能com.google.code.gson：gson-test-fixtures的com.google.code.gson：gson：2.8.5的变体： -变体编译提供com.google.code.gson：gson：2.8.5 -变体运行时提供com.google.code.gson：gson：2.8.5 -Variant platform-compile提供com.google.code.gson：gson衍生平台：2.8.5 -变体平台运行时提供com.google.code.gson：gson衍生平台：2.8.5 -各种强制平台编译提供com.google.code.gson：gson派生强制平台：2.8.5 -各种强制平台运行时提供com.google.code.gson：gson派生强制平台：2.8.5
 com.google.code.gson：gson：2.8.5失败 \\ --- functionalTestClasspath
 通过添加--scan选项，可以使用基于Web的可搜索依赖项报告。
 在0秒内成功建立 1个可执行的任务：1个已执行

错误消息中提到了缺少的`com.google.code.gson:gson-test-fixtures`功能，但实际上并未为此库定义。这是因为按照惯例，对于使用该`java-test-fixtures`插件的项目，Gradle会自动创建具有其名称为主要组件名称的功能的测试夹具变体，并带有附录`-test-fixtures`。

<table style="background:none;width:912px;"><tbody><tr><td class="icon" style="color:rgba(0, 0, 0, 0.8);width:80px;"><i class="fa icon-note"></i></td><td class="content" style="font-size:1.0625rem;color:rgba(0, 0, 0, 0.6);"><div class="paragraph"><p style="font-size: 1rem;"><font><font>如果您发布库并使用测试夹具，但是不想发布夹具，则可以停用</font></font><em style="font-style:italic;"><font><font>测试夹具变体的</font></font></em><font><font>发布，</font><font>如下所示。</font></font></p></div></td></tr></tbody></table>

例子22.禁用测试夹具变体的发布

`Groovy``Kotlin`

build.gradle

components.java.withVariantsFromConfiguration\(configurations.testFixturesApiElements\) \{ skip\(\) \} components.java.withVariantsFromConfiguration\(configurations.testFixturesRuntimeElements\) \{ skip\(\) \}

 

1

components.java.withVariantsFromConfiguration\(configurations.testFixturesApiElements\) \{ skip\(\) \}

2

components.java.withVariantsFromConfiguration\(configurations.testFixturesRuntimeElements\) \{ skip\(\) \}

* * *

[1](#_footnoteref_1)。JUnit Wiki包含有关如何使用JUnit类别的详细说明：[https](https://github.com/junit-team/junit/wiki/Categories):[//github.com/junit-team/junit/wiki/Categories](https://github.com/junit-team/junit/wiki/Categories)。

[2](#_footnoteref_2)。TestNG文档包含有关测试组的更多详细信息：[http](http://testng.org/doc/documentation-main.html#test-groups):[//testng.org/doc/documentation-main.html#test-groups](http://testng.org/doc/documentation-main.html#test-groups)。

[3](#_footnoteref_3)。TestNG文档包含有关处理`testng.xml`文件时的测试顺序的更多详细信息：[http](http://testng.org/doc/documentation-main.html#testng-xml):[//testng.org/doc/documentation-main.html#testng-xml](http://testng.org/doc/documentation-main.html#testng-xml)。