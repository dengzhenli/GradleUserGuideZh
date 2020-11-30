# 安装Gradle

内容

* [先决条件](#sec_prerequisites)
* [使用软件包管理器进行安装](#installing_with_a_package_manager)
* [手动安装](#installing_manually)
* [验证安装](#sec_running_and_testing_your_installation)
* [下一步](#sec_installation_next_steps)

您可以在Linux，macOS或Windows上安装Gradle构建工具。本文档涵盖使用软件包管理器（如SDKMAN）进行安装！或Homebrew，以及手动安装。

建议使用[Gradle wrapper](/md/gradle_wrapper.md#sec_upgrading_wrapper) 升级Gradle。

您可以在“[发行版”页面](https_//gradle_org/releases) 上找到所有发行版及其校验和。

<h2 id = '#sec_prerequisites'> <a href = '#sec_prerequisites'>先决条件</a> </h2>

Gradle可在所有主要操作系统上运行，并且仅需要[Java开发套件](https://jdk.java.net/)版本8或更高版本即可运行。要检查，请运行`java -version`。您应该会看到以下内容：

```java
java -version
java version "1.8.0_151"
Java(TM) SE Runtime Environment (build 1.8.0_151-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.151-b12, mixed mode)
```

Gradle附带了自己的Groovy库，因此不需要安装Groovy。Gradle将忽略任何现有的Groovy安装。

Gradle使用在路径中找到的任何JDK。或者，您可以将`JAVA_HOME`环境变量设置为指向所需JDK的安装目录。

[请参阅Java，Groovy，Kotlin和Android的完整兼容性说明。](/md/兼容性说明.md#compatibility)

<h2 id = '#installing_with_a_package_manager'> <a href = '#installing_with_a_package_manager'>使用软件包管理器进行安装</a> </h2>

[SDKMAN！](http://sdkman.io)是用于在大多数类Unix系统（macOS，Linux，Cygwin，Solaris和FreeBSD）上管理多个软件开发套件的并行版本的工具。我们部署并维护可从SDKMAN获得的版本。

```java
❯ sdk install gradle
```

[Homebrew](http://brew.sh)是“ macOS缺少的软件包管理器”。

```java
❯ brew install gradle
```

可以使用其他软件包管理器，但是它们分发的Gradle版本不受Gradle，Inc.的控制。Linux软件包管理器可能会分发与正式版本（从SDKMAN！或更低版本提供）相比不兼容或不完整的Gradle修改版。 ）。

[↓继续下一步](#sec_installation_next_steps)

<h2 id = '#installing_manually'> <a href = '#installing_manually'>手动安装</a> </h2>

<h3 id = 'https_//gradle_org/releases'> <a href = 'https_//gradle_org/releases'>步骤1.](#step_1_download_the_latest_gradle_distribution)[下载</a> </h3>

分发ZIP文件有两种形式：

* 仅二进制（bin）
* 完整（全部）包含文档和来源

需要使用旧版本吗？请参阅[发布页面](https_//gradle_org/releases)。

<h3 id = '#step_2_unpack_the_distribution'> <a href = '#step_2_unpack_the_distribution'>步骤2.拆开发行包</a> </h3>

<h4 id = '#linux_macos_users'> <a href = '#linux_macos_users'>Linux和MacOS用户</a> </h4>

在您选择的目录中解压缩分发zip文件，例如：
```java
❯ mkdir /opt/gradle
❯ unzip -d /opt/gradle gradle-6.7.1-bin.zip
❯ ls /opt/gradle/gradle-6.7.1
LICENSE  NOTICE  bin  README  init.d  lib  media
```
<h4 id = '#microsoft_windows_users'> <a href = '#microsoft_windows_users'>Microsoft Windows用户</a> </h4>

用文件管理器创建`C:\Gradle`目录。

打开第二个文件资源管理器窗口，然后转到下载Gradle发行版的目录。双击ZIP存档以显示内容。将内容文件夹`gradle-6.7.1`拖到新创建的`C:\Gradle`文件夹中。

或者，您可以`C:\Gradle`使用所选的存档器工具将Gradle发行版ZIP打包。

<h3 id = '#step_3_configure_your_system_environment'> <a href = '#step_3_configure_your_system_environment'>步骤3.配置您的系统环境</a> </h3>

要运行Gradle，从Gradle网站到解压缩文件的路径必须在终端的路径上。对于每个操作系统，执行此操作的步骤都不同。

<h4 id = '#linux_macos_users_2'> <a href = '#linux_macos_users_2'>Linux和MacOS用户</a> </h4>

配置`PATH`环境变量以包含`bin`解压缩发行版的目录，例如：
```java
❯ export PATH=$PATH:/opt/gradle/gradle-6.7.1/bin
```
或者，您也可以添加环境变量`GRADLE_HOME`并将其指向未压缩的发行版。`PATH`您可以`_$GRADLE_HOME_/bin`向自己的而不是向其中添加特定版本的Gradle`PATH`。当升级到其他版本的Gradle时，只需更改`GRADLE_HOME`环境变量即可。

<h4 id = '#microsoft_windows_users_2'> <a href = '#microsoft_windows_users_2'>Microsoft Windows用户</a> </h4>

在**文件管理器**上单击鼠标右键`This PC`（或`Computer`）图标，然后点击`Properties`→交通`Advanced System Settings`→交通`Environmental Variables`。

在`System Variables`选择下`Path`，然后单击确定`Edit`。为添加一个条目`C:\Gradle\gradle-6.7.1\bin`。单击确定保存。

或者，您也可以添加环境变量`GRADLE_HOME`并将其指向未压缩的发行版。`Path`您可以`_%GRADLE_HOME%_/bin`向自己的而不是向其中添加特定版本的Gradle`Path`。当升级到其他版本的Gradle时，只需更改`GRADLE_HOME`环境变量即可。

[↓继续下一步](#sec_installation_next_steps)

<h2 id = '#sec_running_and_testing_your_installation'> <a href = '#sec_running_and_testing_your_installation'>验证安装</a> </h2>

打开控制台（或Windows命令提示符）并运行`gradle \-v`以运行gradle并显示版本，例如：

```java
❯ gradle -v

------------------------------------------------------------
Gradle 6.7.1
------------------------------------------------------------

(environment specific information)
```
如果遇到麻烦，请参阅“[排除安装故障](/md/对构建进行故障排除.md#sec_troubleshooting_installation) ”[部分](/md/对构建进行故障排除.md#sec_troubleshooting_installation) 。


您可以通过下载SHA-256文件（可从[发行版中获得](https_//gradle_org/releases) ）并按照以下[验证指示信息](/md/gradle_wrapper.md#sec_verification) 来验证Gradle分发的完整性。

<h2 id = '#sec_installation_next_steps'> <a href = '#sec_installation_next_steps'>下一步</a> </h2>

现在您已经安装了Gradle，请使用以下资源进行入门：

* 通过遵循我们的[分步示例](https://docs.gradle.org/6.7.1/samples/index.html) 之一，创建您的第一个Gradle项目。
* 与核心工程师一起注册[Gradle现场入门培训](https://gradle.org/training/intro-to-gradle/) 。
* 了解如何通过[命令行界面](/md/命令行界面.md#command_line_interface) 完成常见任务。
* [配置Gradle执行](/md/Gradle环境搭建.md#build_environment) ，例如使用HTTP代理下载依赖项。
* 订阅[Gradle通讯](https://newsletter.gradle.com/) 以获取每月发布和社区更新。
