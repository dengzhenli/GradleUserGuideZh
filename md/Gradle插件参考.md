# Gradle插件参考


内容

  * [JVM语言和框架](#jvm_languages_and_frameworks)
  * [母语](#native_languages)
  * [包装与配送](#packaging_and_distribution)
  * [代码分析](#code_analysis)
  * [IDE整合](#ide_integration)
  * [效用](#utility)

此页面包含Gradle本身提供的所有核心插件的链接和简短描述。

<h2 id = '#jvm_languages_and_frameworks'> <a href = '#jvm_languages_and_frameworks'>JVM语言和框架</a> </h2>

[Java](https://docs.gradle.org/6.7.1/userguide/java_plugin.html)

    

提供对构建任何类型的Java项目的支持。

[Java库](/md/Java库插件.md)

    

提供对构建Java库的支持。

[Java平台](/md/Java平台插件.md)

    

提供对构建Java平台的支持。

[Groovy](/md/Groovy插件.md)

    

为构建任何类型的[Groovy](https://groovy-lang.org/)项目提供支持。

[Scala](/md/Scala插件.md)

    

为构建任何类型的[Scala](https://www.scala-lang.org/)项目提供支持。

[ANTLR](https://docs.gradle.org/6.7.1/userguide/antlr_plugin.html)

    

提供对使用[ANTLR](http://www.antlr.org/)生成解析器的支持。

<h2 id = '#native_languages'> <a href = '#native_languages'>母语</a> </h2>

[C++应用](https://docs.gradle.org/6.7.1/userguide/cpp_application_plugin.html)

    

提供对在Windows，Linux和macOS上构建C ++应用程序的支持。

[C++库](https://docs.gradle.org/6.7.1/userguide/cpp_library_plugin.html)

    

提供对在Windows，Linux和macOS上构建C ++库的支持。

[C++单元测试](https://docs.gradle.org/6.7.1/userguide/cpp_unit_test_plugin.html)

    

提供对在Windows，Linux和macOS上构建和运行基于C ++可执行文件的测试的支持。

[Swift](https://docs.gradle.org/6.7.1/userguide/swift_application_plugin.html)

    

提供对在Linux和macOS上构建Swift应用程序的支持。

[Swift Library](https://docs.gradle.org/6.7.1/userguide/swift_library_plugin.html)

    

提供对在Linux和macOS上构建Swift库的支持。

[XCTest](https://docs.gradle.org/6.7.1/userguide/xctest_plugin.html)

    

提供对在Linux和macOS上构建和运行基于XCTest的测试的支持。

<h2 id = '#packaging_and_distribution'> <a href = '#packaging_and_distribution'>packaging and distribution</a> </h2>

[application](/md/Java应用插件.md)

    

提供对构建基于JVM的可运行应用程序的支持。

[war](https://docs.gradle.org/6.7.1/userguide/war_plugin.html)

    

提供对构建和打包基于WAR的Java Web应用程序的支持。

[ear](https://docs.gradle.org/6.7.1/userguide/ear_plugin.html)

    

提供对构建和打包Java EE应用程序的支持。

[Maven发布](/md/Maven发布插件.md)

    

提供对将[工件发布](/md/将项目发布为模块.md)到兼容Maven的存储库的支持。

[Ivy出版](/md/Ivy发布插件.md)

    

提供对将[工件发布](/md/将项目发布为模块.md)到兼容Ivy的存储库的支持。

[旧版Maven插件](https://docs.gradle.org/6.7.1/userguide/maven_plugin.html)

    

使用[旧版机制](https://docs.gradle.org/6.7.1/userguide/artifact_management.html)向与Maven兼容的存储库发布工件提供支持。

[distribution](https://docs.gradle.org/6.7.1/userguide/distribution_plugin.html)

    

轻松创建项目的ZIP和tarball发行版。

[Java库发行](https://docs.gradle.org/6.7.1/userguide/java_library_distribution_plugin.html)

    

提供对创建包含其运行时依赖项的Java库项目的ZIP分发的支持。

<h2 id = '#code_analysis'> <a href = '#code_analysis'>代码分析</a> </h2>

[Checkstyle](https://docs.gradle.org/6.7.1/userguide/checkstyle_plugin.html)

    

使用[Checkstyle](https://checkstyle.org/index.html)对项目的Java源文件执行质量检查，并生成关联的报告。

[PMD](https://docs.gradle.org/6.7.1/userguide/pmd_plugin.html)

    

使用[PMD](http://pmd.github.io/)对项目的Java源文件执行质量检查，并生成关联的报告。

[JaCoCo](https://docs.gradle.org/6.7.1/userguide/jacoco_plugin.html)

    

使用[JaCoCo](http://www.eclemma.org/jacoco/)为您的Java项目提供代码覆盖率指标。

[CodeNarc](https://docs.gradle.org/6.7.1/userguide/codenarc_plugin.html)

    

使用[CodeNarc](http://codenarc.sourceforge.net/index.html)对Groovy源文件执行质量检查，并生成关联的报告。

<h2 id = '#ide_integration'> <a href = '#ide_integration'>IDE整合</a> </h2>

[Eclipse](https://docs.gradle.org/6.7.1/userguide/eclipse_plugin.html)

    

为可以由IDE打开的构建生成Eclipse项目文件。这套插件还可以用于微调[Buildship的](http://projects.eclipse.org/projects/tools.buildship)Gradle构建导入过程。

[IntelliJ IDEA](https://docs.gradle.org/6.7.1/userguide/idea_plugin.html)

    

为可以由IDE打开的构建生成IDEA项目文件。它也可以用于为Gradle构建微调IDEA的导入过程。

[Visual Studio](https://docs.gradle.org/6.7.1/userguide/visual_studio_plugin.html)

    

生成可通过IDE打开的Visual Studio解决方案和项目文件。

[Xcode](https://docs.gradle.org/6.7.1/userguide/xcode_plugin.html)

    

为可以由IDE打开的内部版本生成Xcode工作区和项目文件。

<h2 id = '#utility'> <a href = '#utility'>utility</a> </h2>
[base](https://docs.gradle.org/6.7.1/userguide/base_plugin.html)

    

提供常见的生命周期任务，例如`clean`和大多数构建通用的其他功能。

[build init](https://docs.gradle.org/6.7.1/userguide/build_init_plugin.html)

    

生成指定类型的新Gradle构建，例如Java库。它还可以从Maven POM生成构建脚本-
有关更多详细信息，请参见[从Maven迁移到Gradle](/md/从Apache Maven迁移.md)。

[签名](https://docs.gradle.org/6.7.1/userguide/signing_plugin.html)

    

为生成的文件和工件进行数字签名提供支持。

[插件开发](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html)

    

使开发和发布Gradle插件更加容易。

[项目报告插件](https://docs.gradle.org/6.7.1/userguide/project_report_plugin.html)

    

帮助生成包含有关构建的有用信息的报告。

