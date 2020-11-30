# Gradle Wrap


内容

  * [添加Gradle Wrap](#sec:adding_wrapper)
  * [使用Gradle Wrap](#sec:using_wrapper)
  * [升级Gradle Wrap](#sec:upgrading_wrapper)
  * [自定义Gradle Wrap](#customizing_wrapper)
  * [验证Gradle Wrapper JAR的完整性](#wrapper_checksum_verification)

建议执行任何Gradle构建的方法是在Gradle Wrapper（简称为“
Wrapper”）的帮助下。 Wrap是一个脚本，可调用Gradle的声明版本，并在必要时预先下载。因此，开发人员可以快速启动并运行Gradle项目，而无需遵循手动安装过程，从而节省了公司的时间和金钱。

![ Wrap工作流程](img/wrapper-workflow.png)

图1. Wrap工作流程

**简而言之，您将获得以下好处：**

  * 在给定的Gradle版本上标准化项目，从而实现更可靠，更可靠的构建。

  * 向不同的用户和执行环境（例如，IDE或Continuous Integration服务器）提供新的Gradle版本就像更改 Wrap定义一样简单。

**那么它是怎样工作的？ 对于用户而言，通常有三种不同的工作流程：**

  * 您设置了一个新的Gradle项目，并希望[将 Wrap添加](#sec:adding_wrapper)到其中。

  * 您想[使用](#sec:using_wrapper)已经提供[了 Wrap](#sec:using_wrapper)程序[的项目](#sec:using_wrapper)来[运行该项目](#sec:using_wrapper)。

  * 您想[将 Wrap升级](#sec:upgrading_wrapper)到新版本的Gradle。

以下各节将更详细地说明这些用例。

<h2 id = '#sec:adding_wrapper'> <a href = '#sec:adding_wrapper'>添加Gradle Wrap</a> </h2>

生成Wrapper文件需要在计算机上安装Gradle运行时的安装版本，如[Installation中所述](/md/安装Gradle.md#installation)。幸运的是，生成初始Wrapper文件是一个一次性的过程。

每个普通的Gradle构建都带有一个称为的内置任务`wrapper`。列出任务时，您将可以在“构建安装程序任务”组下找到列出[的任务](/md/命令行界面.md#sec:listing_tasks)。执行`wrapper`任务会在项目目录中生成必要的Wrapper文件。

运行 Wrap任务

    
    
    $ gradle wrapper
    > Task :wrapper
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

╔═════════════════════════════  

要使 Wrap文件可用于其他开发人员和执行环境，您需要将其检入版本控制。包括JAR文件在内的所有 Wrap程序文件都非常小。期望将JAR文件添加到版本控制中。一些组织不允许项目将二进制文件提交给版本控制。目前，该方法没有其他选择。  
  
╚═════════════════════════════    
  
生成的Wrapper属性文件，`gradle/wrapper/gradle-wrapper.properties`存储有关Gradle分布的信息。

  * 托管Gradle发行版的服务器。

  * Gradle分布的类型。默认情况下，该`-bin`发行版仅包含运行时，但不包含示例代码和文档。

  * 用于执行构建的Gradle版本。默认情况下，`wrapper`任务会选择与用于生成 Wrap文件的Gradle版本完全相同的Gradle版本。

`gradle/wrapper/gradle-wrapper.properties`

    
    
    distributionUrl=https\://services.gradle.org/distributions/gradle-6.7.1-bin.zip

所有这些方面都可以在以下命令行选项的帮助下配置 Wrap文件时进行配置。

`--gradle-version`

    

用于下载和执行 Wrap程序的Gradle版本。

`--distribution-type`

    

 Wrap使用的Gradle分布类型。可用的选项是`bin`和`all`。默认值为`bin`。

`--gradle-distribution-url`

    

指向Gradle分发ZIP文件的完整URL。使用此选项，`--gradle-version`并且`--distribution-
type`过时的网址已经包含此信息。如果您想在公司网络内部托管Gradle发行版，则此选项非常有价值。

`--gradle-distribution-sha256-sum`

    

SHA256哈希和用于[验证下载的Gradle分布](#sec:verification)。

让我们假设以下用例说明了命令行选项的用法。您想生成版本为6.7.1的Wrapper，并使用该`-all`发行版来使您的IDE启用代码补全功能并能够导航到Gradle源代码。这些要求由以下命令行执行捕获：

提供 Wrap任务的选项

    
    
    $ gradle wrapper --gradle-version 6.7.1 --distribution-type all
    > Task :wrapper
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

结果，您可以在 Wrap属性文件中找到所需的信息。

示例：生成的分发URL

    
    
    distributionUrl=https\://services.gradle.org/distributions/gradle-6.7.1-all.zip

让我们看一下以下项目布局，以说明预期的Wrapper文件：

`Groovy``Kotlin`

    
    
    .
    ├── a-subproject
    │   └── build.gradle
    ├── settings.gradle
    ├── gradle
    │   └── wrapper
    │       ├── gradle-wrapper.jar
    │       └── gradle-wrapper.properties
    ├── gradlew
    └── gradlew.bat
    
    
    .
    ├── a-subproject
    │   └── build.gradle.kts
    ├── settings.gradle.kts
    ├── gradle
    │   └── wrapper
    │       ├── gradle-wrapper.jar
    │       └── gradle-wrapper.properties
    ├── gradlew
    └── gradlew.bat

Gradle项目通常为每个子项目提供一个`settings.gradle(.kts)`文件和一个`build.gradle(.kts)`文件。 Wrap程序文件位于项目的`gradle`目录和根目录中。以下列表说明了它们的用途。

`gradle-wrapper.jar`

    

 WrapJAR文件，其中包含用于下载Gradle发行版的代码。

`gradle-wrapper.properties`

    

一个属性文件，负责配置Wrapper运行时行为，例如与该版本兼容的Gradle版本。请注意，更多常规设置（例如，[将 Wrap配置为使用代理](/md/Gradle环境搭建.md#sec:accessing_the_web_via_a_proxy)）需要进入[其他文件](/md/Gradle环境搭建.md#sec:gradle_configuration_properties)。

`gradlew`， `gradlew.bat`

    

一个外壳脚本和一个Windows批处理脚本，用于使用 Wrap程序执行构建。

您可以继续[使用 Wrap程序执行构建，](#sec:using_wrapper)而不必安装Gradle运行时。如果您正在处理的项目不包含那些Wrapper文件，则需要[生成它们](#sec:adding_wrapper)。

<h2 id = '#sec:using_wrapper'> <a href = '#sec:using_wrapper'>使用Gradle Wrap</a> </h2>

建议始终使用 Wrap执行构建，以确保可靠，受控和标准化地执行构建。使用Wrapper看起来几乎就像使用Gradle安装来运行构建。根据操作系统的不同，您可以运行`gradlew`或`gradlew.bat`代替`gradle`命令。以下控制台输出演示了Windows机器上 Wrap程序对基于Java的项目的使用。

使用 Wrap程序批处理文件执行构建

    
    
    $ gradlew.bat build
    Downloading https://services.gradle.org/distributions/gradle-5.0-all.zip
    .....................................................................................
    Unzipping C:\Documents and Settings\Claudia\.gradle\wrapper\dists\gradle-5.0-all\ac27o8rbd0ic8ih41or9l32mv\gradle-5.0-all.zip to C:\Documents and Settings\Claudia\.gradle\wrapper\dists\gradle-5.0-al\ac27o8rbd0ic8ih41or9l32mv
    Set executable permissions for: C:\Documents and Settings\Claudia\.gradle\wrapper\dists\gradle-5.0-all\ac27o8rbd0ic8ih41or9l32mv\gradle-5.0\bin\gradle
    
    BUILD SUCCESSFUL in 12s
    1 actionable task: 1 executed

如果Gradle发行版在计算机上不可用， Wrap程序将下载它并存储在本地文件系统中。只要Gradle属性中的分发URL不变，任何后续的构建调用都将重用现有的本地分发。

╔═════════════════════════════  

 Wrap程序外壳程序脚本和批处理文件位于单项目或多项目Gradle构建的根目录中。如果要从子项目目录执行构建，则需要引用这些文件的正确路径`../../gradlew
tasks`。  
  
╚═════════════════════════════    
  
<h2 id = '#sec:upgrading_wrapper'> <a href = '#sec:upgrading_wrapper'>升级Gradle Wrap</a> </h2>

项目通常会希望与时俱进并升级Gradle版本，以从新功能和改进中受益。升级Gradle版本的一种方法是手动更改`distributionUrl` Wrap`gradle-
wrapper.properties`文件中的属性。更好的建议选项是运行`wrapper`任务并提供目标Gradle版本，如[添加Gradle Wrap中所述](#sec:adding_wrapper)。使用该`wrapper`任务可确保对具有该特定Gradle版本的Wrapper
Shell脚本或批处理文件所做的任何优化都应用于项目。与往常一样，您应该将对Wrapper文件的更改提交给版本控制。

请注意，仅运行一次 Wrap程序任务将`gradle-wrapper.properties`仅更新，但`gradle-
wrapper.jar`不影响 Wrap程序本身。这通常很好，因为即使使用古老的 Wrap文件也可以运行Gradle的新版本。但是，如果您希望 **所有**
 Wrap文件都完全是最新的，则需要再次运行该`wrapper`任务。

使用Gradle`wrapper`任务生成 Wrap，指定版本。默认为当前版本。升级 Wrap程序后，可以通过执行来检查它是否是您期望的版本`./gradlew
--version`。

示例：升级 Wrap版本

    
    
    $ ./gradlew Wrap--gradle-version 6.7.1
    
    在4秒钟内成功建立
    1个可执行的任务：1个已执行

<h2 id = '#customizing_wrapper'> <a href = '#customizing_wrapper'>自定义Gradle Wrap</a> </h2>

Gradle的大多数用户对Wrapper的默认运行时行为感到满意。但是，组织策略，安全性约束或个人喜好可能需要您更深入地定制 Wrap。幸运的是，内置`wrapper`任务提供了许多选项，可以使运行时行为适应您的需求。大多数配置选项由基础任务类型[Wrapper](https://docs.gradle.org/6.7.1/dsl/org.gradle.api.tasks.wrapper.Wrapper.html)公开。

假设您`-all`每次升级 Wrap程序时都厌倦了在命令行上定义分发类型。您可以通过重新配置`wrapper`任务来节省一些键盘笔触。

示例1.定制 Wrap任务

`Groovy``Kotlin`

build.gradle

    
    
    tasks.named('wrapper') {
        distributionType = Wrapper.DistributionType.ALL
    }

build.gradle.kts

    
    
    tasks.named<Wrapper>("wrapper") {
        distributionType = Wrapper.DistributionType.ALL
    }

在适当的配置下，运行`./gradlew wrapper --gradle-version
6.7.1`足以`distributionUrl`在Wrapper属性文件中生成一个值，该值将请求`-all`分发。

生成的分发URL

    
    
    distributionUrl = https \：//services.gradle.org/distributions/gradle-6.7.1-all.zip

请查阅API文档，以获取有关可用配置选项的更多详细说明。您还可以在Gradle发行版中找到用于配置 Wrap的各种示例。

<h3 id = '#sec:authenticated_download'> <a href = '#sec:authenticated_download'>经过身份验证的Gradle发行版下载</a> </h3>

Gradle`Wrapper`可以使用HTTP基本身份验证从服务器下载Gradle发行版。这使您可以将Gradle分发托管在受保护的私有服务器上。您可以根据使用情况以两种不同的方式指定用户名和密码：作为系统属性或直接嵌入`distributionUrl`。系统属性中的凭据优先于中嵌入的凭据`distributionUrl`。

╔═════════════════════════════  

安全警告

HTTP基本身份验证仅应与`HTTPS`URL一起使用，而不应与普通URL一起使用`HTTP`。使用基本身份验证，用户凭据以明文形式发送。  
  
╚═════════════════════════════    
  
使用系统属性可以`.gradle/gradle.properties`在用户主目录中的文件中完成，也可以通过其他方式进行，请参阅[Gradle配置属性](/md/Gradle环境搭建.md#sec:gradle_configuration_properties)。

使用系统属性指定HTTP基本身份验证凭据

    
    
    systemProp.gradle.wrapperUser=username
    systemProp.gradle.wrapperPassword=password

嵌入在证书`distributionUrl`中`gradle/wrapper/gradle-
wrapper.properties`的文件也适用。请注意，此文件将提交到您的源代码管理系统中。嵌入的共享凭据`distributionUrl`仅应在受控环境中使用。

在中指定HTTP基本身份验证凭据 `distributionUrl`

    
    
    distributionUrl=https://username:password@somehost/path/to/gradle-distribution.zip

可以与已认证的代理或未认证的代理一起使用。有关如何配置使用[代理](/md/Gradle环境搭建.md#sec:accessing_the_web_via_a_proxy)的更多信息，请参见[通过代理访问网络](/md/Gradle环境搭建.md#sec:accessing_the_web_via_a_proxy)`Wrapper`。

<h3 id = '#sec:verification'> <a href = '#sec:verification'>验证下载的Gradle发行版</a> </h3>

Gradle Wrap可通过SHA-256哈希总和比较来验证下载的Gradle分发。通过防止中间人攻击者篡改下载的Gradle发行版，提高了针对目标攻击的安全性。

要启用此功能，请下载`.sha256`与要验证的Gradle发行版关联的文件。

<h4 id = '#downloading_the_sha_256_file'> <a href = '#downloading_the_sha_256_file'>下载SHA-256文件</a> </h4>

您可以`.sha256`从[稳定发行版](https://services.gradle.org/distributions/)
或[候选](https://services.gradle.org/distributions-snapshots/)
[发行版](https://services.gradle.org/distributions/)和
[每晚发行版](https://services.gradle.org/distributions-snapshots/)中下载文件。文件的格式是一行文本，它是相应zip文件的SHA-256哈希。

您还可以参考[Gradle分布校验和列表](https://gradle.org/release-checksums/)。

<h4 id = '#configuring_checksum_verification'> <a href = '#configuring_checksum_verification'>配置校验和验证</a> </h4>

将下载的哈希值添加到`gradle-wrapper.properties`使用`distributionSha256Sum`属性或`--gradle-
distribution-sha256-sum`在命令行上使用。

配置SHA-256校验和验证

    
    
    distributionSha256Sum=371cb9fbebbe9880d147f59bab36d61eee122854ef8c9ee1ecf12b82368bcf10

如果配置的校验和与在托管发行版的服务器上找到的校验和不匹配，则Gradle将报告构建失败。仅当尚未下载已配置的 Wrap发行版时才执行校验和验证。

<h2 id = '#wrapper_checksum_verification'> <a href = '#wrapper_checksum_verification'>验证Gradle Wrapper JAR的完整性</a> </h2>

 WrapJAR是一个二进制文件，将在开发人员和构建服务器的计算机上执行。与所有此类文件一样，在执行文件之前，应确保它是可信任的。由于通常将Wrapper
JAR检查到项目的版本控制系统中，恶意参与者有可能通过提交貌似仅升级Gradle版本的拉取请求来用修改后的JAR替换原始JAR。

为了验证 WrapJAR的完整性，Gradle创建了一个[GitHub Action](https://github.com/marketplace/actions/gradle-wrapper-validation)，
该[GitHub Action](https://github.com/marketplace/actions/gradle-wrapper-validation)
根据已知的良好校验和列表自动检查请求请求中的 WrapJAR。Gradle还会发布[所有发行版](https://gradle.org/release-checksums/)
的[校验和](https://gradle.org/release-checksums/)
（3.3至4.0.2版除外，该版本不会生成可复制的JAR），因此您可以手动验证 WrapJAR的完整性。

<h3 id = '#automatically_verifying_the_gradle_wrapper_jar_on_github'> <a href = '#automatically_verifying_the_gradle_wrapper_jar_on_github'>在GitHub上自动验证Gradle Wrapper JAR</a> </h3>

在[GitHub的行动](https://github.com/marketplace/actions/gradle-wrapper-validation)是从Gradle单独发布，所以请检查其如何将其应用到你的项目文档。

<h3 id = '#manually_verifying_the_gradle_wrapper_jar'> <a href = '#manually_verifying_the_gradle_wrapper_jar'>手动验证Gradle Wrapper JAR</a> </h3>

您可以通过在主要操作系统之一上运行以下命令来手动验证Wrapper JAR的校验和，以确保未被篡改：

在Linux上手动验证Wrapper JAR的校验和

    
    
    $ cd gradle/wrapper
    $ curl --location --output gradle-wrapper.jar.sha256 \
           https://services.gradle.org/distributions/gradle-6.7.1-wrapper.jar.sha256
    $ echo "  gradle-wrapper.jar" >> gradle-wrapper.jar.sha256
    $ sha256sum --check gradle-wrapper.jar.sha256
    gradle-wrapper.jar: OK

在macOS上手动验证Wrapper JAR的校验和

    
    
    $ cd gradle/wrapper
    $ curl --location --output gradle-wrapper.jar.sha256 \
           https://services.gradle.org/distributions/gradle-6.7.1-wrapper.jar.sha256
    $ echo "  gradle-wrapper.jar" >> gradle-wrapper.jar.sha256
    $ shasum --check gradle-wrapper.jar.sha256
    gradle-wrapper.jar: OK

在Windows上手动验证Wrapper JAR的校验和（使用PowerShell）

    
    
    > $expected = Invoke-RestMethod -Uri https://services.gradle.org/distributions/gradle-6.7.1-wrapper.jar.sha256
    > $actual = (Get-FileHash gradle\wrapper\gradle-wrapper.jar -Algorithm SHA256).Hash.ToLower()
    > @{$true = 'OK: Checksum match'; $false = "ERROR: Checksum mismatch!`nExpected: $expected`nActual:   $actual"}[$actual -eq $expected]
    OK: Checksum match

<h3 id = '#troubleshooting_a_checksum_mismatch'> <a href = '#troubleshooting_a_checksum_mismatch'>对校验和不匹配进行故障排除</a> </h3>

如果校验和与您期望的校验和不匹配，则可能是`wrapper`升级的Gradle发行版未执行任务。因此，您应该首先检查实际校验和是否与其他Gradle版本之一匹配。您可以在主要操作系统上运行以下命令来生成Wrapper
JAR的实际校验和：

在Linux上生成 WrapJAR的实际校验和

    
    
    $ sha256sum gradle/wrapper/gradle-wrapper.jar
    d81e0f23ade952b35e55333dd5f1821585e887c6d24305aeea2fbc8dad564b95  gradle/wrapper/gradle-wrapper.jar

在macOS上生成 WrapJAR的实际校验和

    
    
    $ shasum --algorithm=256 gradle/wrapper/gradle-wrapper.jar
    d81e0f23ade952b35e55333dd5f1821585e887c6d24305aeea2fbc8dad564b95  gradle/wrapper/gradle-wrapper.jar

在Windows上生成 WrapJAR的实际校验和（使用PowerShell）

    
    
    > (Get-FileHash gradle\wrapper\gradle-wrapper.jar -Algorithm SHA256).Hash.ToLower()
    d81e0f23ade952b35e55333dd5f1821585e887c6d24305aeea2fbc8dad564b95

一旦知道了实际的校验和，请检查它是否在[https://gradle.org/release-checksums/](https://gradle.org/release-checksums/)上列出。如果已列出，则您已验证 WrapJAR的完整性。如果生成Wrapper JAR的Gradle版本与中的版本不匹配`gradle/wrapper/gradle-
wrapper.properties`，则可以安全地`wrapper`再次运行任务以更新Wrapper JAR。

如果页面上未列出校验和，则 WrapJAR可能来自里程碑，候选发布版本或每晚构建，或者可能是Gradle
3.3至4.0.2生成的。您应该尝试找出它是如何生成的，但是除非有其他证明，否则应将其视为不可信的。如果您认为Wrapper
JAR受到威胁，请发送电子邮件至[security@gradle.com](mailto:security@gradle.com)告知Gradle团队。

