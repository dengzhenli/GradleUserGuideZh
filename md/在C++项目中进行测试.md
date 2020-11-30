# 在C ++项目中进行测试


内容

  * [基础知识](#sec_cpp_testing_basics)
  * [测试执行](#sec_cpp_test_execution)

在原生生态系统中进行测试有多种形式。

有不同的测试库和框架，以及许多不同类型的测试。无论它们是频繁执行还是不频繁执行，所有这些都需要成为构建的一部分。本章致力于说明Gradle如何处理内部版本之间以及内部内部的不同要求，并大量介绍了Gradle如何与基于可执行文件的测试框架（例如[Google Test）](https://github.com/google/googletest)集成。

与[在Java和JVM项目中](/md/在Java和JVM项目中进行测试.md#java_testing)进行[测试](/md/在Java和JVM项目中进行测试.md#java_testing)相比，在Gradle中测试C
++项目非常有限。在本章中，我们解释了控制测试运行方式的方法（[测试执行](#sec_cpp_test_execution)）。

但是首先，我们看一下Gradle中本机测试的基础。

<h2 id = '#sec_cpp_testing_basics'> <a href = '#sec_cpp_testing_basics'>基础知识</a> </h2>

所有C
++测试都围绕一种任务类型运行：[RunTestExecutable](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.tasks.RunTestExecutable.html)。这将运行使用任何测试框架构建的单个测试可执行文件，并使用可执行文件的退出代码断言执行成功。没有收集测试用例结果，也没有生成报告。

为了进行操作，[RunTestExecutable](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.tasks.RunTestExecutable.html)任务类型仅需要一条信息：

  * 在哪里找到构建的测试可执行文件（属性：[RunTestExecutable.getExecutable（）](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.tasks.RunTestExecutable.html#org.gradle.nativeplatform.test.tasks.RunTestExecutable:executable)）

使用[C++单元测试插件时，](https://docs.gradle.org/6.7.1/userguide/cpp_unit_test_plugin.html)您将自动获得以下信息：

  * 专用的[unitTest](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.cpp.CppTestSuite.html)扩展，用于配置测试组件及其变体

  * 运行测试可执行文件的[RunTestExecutable](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.tasks.RunTestExecutable.html)`run`类型的任务[](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.tasks.RunTestExecutable.html)

测试插件会适当配置所需的信息。另外，他们将`run`任务附加到`check`生命周期任务。它还创建`testImplementation`依赖项配置。只能将测试编译，链接和运行时所需的依赖项添加到此配置中。该`unitTest`脚本块的行为类似于一个`application`或`library`脚本块。

该[RunTestExecutable](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.tasks.RunTestExecutable.html)任务有许多配置选项。在本章的其余部分中，我们将介绍其中的一些。

<h2 id = '#sec_cpp_test_execution'> <a href = '#sec_cpp_test_execution'>测试执行</a> </h2>

Gradle在单独的（“分叉”）过程中执行测试。

您可以通过[RunTestExecutable](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.tasks.RunTestExecutable.html)任务上的几个属性来控制如何启动测试过程，包括以下内容：

`ignoreFailures` -默认值：false

    

如果此属性为`true`，则在测试完成后，即使其中一些失败，Gradle也会继续进行项目的构建。请注意，默认情况下，不管该设置如何，[RunTestExecutable](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.tasks.RunTestExecutable.html)任务类型始终执行它检测到的每个测试。

有关所有可用配置选项的详细信息，请参见[RunTestExecutable](https://docs.gradle.org/6.7.1/dsl/org.gradle.nativeplatform.test.tasks.RunTestExecutable.html)。

