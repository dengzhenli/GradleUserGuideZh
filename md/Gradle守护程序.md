# Gradle守护程序

内容

  * [为什么Gradle Daemon对性能很重要](#sec:why_the_daemon)
  * [运行守护程序状态](#sec:status)
  * [禁用守护程序](#sec:disabling_the_daemon)
  * [停止现有的守护程序](#sec:stopping_an_existing_daemon)
  * [常问问题](#daemon_faq)
  * [工具和IDE](#sec:tools_and_ides)
  * [Gradle守护程序如何使构建更快？](#sec:how_does_the_gradle_daemon_make_builds_faster)
  * [观看文件系统](#sec:daemon_watch_fs)

> 守护程序是一种计算机程序，它作为后台进程运行，而不是受交互式用户的直接控制。

—维基百科

Gradle在Java虚拟机（JVM）上运行，并使用一些支持库，这些库需要很短的初始化时间。结果，有时启动似乎有些慢。解决此问题的方法是Gradle
_Daemon_
：这是一个长期存在的后台进程，与以前相比，它可以更快地执行构建。通过避免昂贵的引导过程以及利用缓存（将有关项目的数据保留在内存中），我们可以实现这一目标。使用Daemon运行Gradle构建与没有运行没有什么不同。只需配置您是否要使用它-
其他所有事情都由Gradle透明地处理。

## [](#sec:why_the_daemon)[为什么Gradle Daemon对性能很重要](#sec:why_the_daemon)

守护进程是一个长期存在的过程，因此我们不仅可以避免每次构建都需要启动JVM的成本，而且还可以在内存中缓存有关项目结构，文件，任务等的信息。

推理很简单：通过重用以前构建的计算来提高构建速度。但是，这样做的好处是巨大的：通常，在以后的构建中，我们估计构建时间减少了15-75％。我们建议您使用`--profile`来对构建进行概要分析，以了解Gradle
Daemon对您的影响。

默认情况下，Gradle Daemon从Gradle 3.0开始启用，因此您无需做任何事情即可从中受益。

## [](#sec:status)[运行守护程序状态](#sec:status)

要获取正在运行的Gradle守护程序及其状态的列表，请使用`--status`命令。

样本输出：

    
    
      PID VERSION                 STATUS
      28411 3.0                     IDLE
      34247 3.0                     BUSY

当前，给定的Gradle版本只能连接到相同版本的守护程序。这意味着状态输出将仅显示正在调用的Gradle版本的守护程序，而不显示任何其他版本的守护程序。Gradle的未来版本将解除此约束，并将显示所有Gradle版本的正在运行的守护程序。

## [](#sec:disabling_the_daemon)[禁用守护程序](#sec:disabling_the_daemon)

Gradle守护程序默认情况下处于启用状态，我们建议始终启用它。有几种方法可以禁用守护程序，但是最常见的一种方法是添加行

    org.gradle.daemon=false

到文件`«USER_HOME»/.gradle/gradle.properties`，`«USER_HOME»`是您的主目录。通常是以下之一，具体取决于您的平台：

  * `C:\Users\<username>` （Windows Vista和7+）

  * `/Users/<username>` （苹果系统）

  * `/home/<username>` （Linux）

如果该文件不存在，则使用文本编辑器创建它。您可以在后面的“守护[程序常见问题”中](#daemon_faq)找到其他禁用（启用）守护[程序的](#daemon_faq)方法的详细信息。该部分还包含有关守护程序如何工作的更多详细信息。

请注意，启用了守护程序后，无论特定构建使用的Gradle版本如何，所有构建都将利用速度提升。

╔═════════════════════════════

持续集成

从Gradle 3.0开始，我们默认启用Daemon并建议将其用于开发人员的计算机和Continuous
Integration服务器。但是，如果您怀疑Daemon使您的CI构建不稳定，则可以将其禁用以对每个构建使用全新的运行时，因为运行时与任何先前的构建
_完全_ 隔离。  
  
╚═════════════════════════════
  
##
[](#sec:stopping_an_existing_daemon)[停止现有的守护程序](#sec:stopping_an_existing_daemon)

如前所述，守护进程是一个后台进程。不过，您不必担心计算机上会构建Gradle进程。与可用的系统内存总量相比，每个守护程序都会监视其内存使用情况，并且在可用系统内存不足时空闲时将自行停止。如果出于任何原因要显式停止运行Daemon进程，只需使用命令`gradle
--stop`。

这将终止所有与用于执行命令的相同Gradle版本一起启动的Daemon进程。如果安装了Java开发工具包（JDK），则可以通过运行`jps`命令轻松地验证守护程序是否已停止。您会看到名称为的所有正在运行的守护程序`GradleDaemon`。

##
[](#daemon_faq)[常问问题](#daemon_faq)

###
[](#sec:ways_to_disable_gradle_daemon)[如何禁用Gradle守护程序？](#sec:ways_to_disable_gradle_daemon)

有两种建议的方法可以为环境持久禁用守护程序：

  * 通过环境变量：将标志添加`-Dorg.gradle.daemon=false`到`GRADLE_OPTS`环境变量

  * 通过属性文件：添加`org.gradle.daemon=false`到`«GRADLE_USER_HOME»/gradle.properties`文件

  
╔═════════════════════════════

注意，`«GRADLE_USER_HOME»`默认为`«USER_HOME»/.gradle`，其中`«USER_HOME»`是当前用户的主目录。可以通过`-g`和`--gradle-
user-home`命令行开关以及`GRADLE_USER_HOME`环境变量和`org.gradle.user.home`JVM系统属性来配置此位置。  
  
╚═════════════════════════════
  
两种方法具有相同的效果。使用哪一个取决于个人喜好。大多数Gradle用户选择第二个选项，然后将条目添加到用户`gradle.properties`文件中。

在Windows上，此命令将为当前用户禁用守护程序：

    
    
    (if not exist "%USERPROFILE%/.gradle" mkdir "%USERPROFILE%/.gradle") && (echo. >> "%USERPROFILE%/.gradle/gradle.properties" && echo org.gradle.daemon=false >> "%USERPROFILE%/.gradle/gradle.properties")

在类似UNIX的操作系统上，以下Bash shell命令将为当前用户禁用守护程序：

    
    
    mkdir -p ~/.gradle && echo "org.gradle.daemon=false" >> ~/.gradle/gradle.properties

一旦以此方式为构建环境禁用了守护程序，除非使用该`--daemon`选项明确请求，否则不会启动Gradle守护程序。

使用Gradle命令行界面时，`--daemon`和`--no-
daemon`命令行选项启用和禁用守护程序用于单独的构建调用。在考虑构建环境时，这些命令行选项具有 _最高_
优先级。通常，为环境（例如，用户帐户）启用守护程序更为方便，以便所有构建都使用守护程序而无需记住要提供该`--daemon`选项。

### [](#sec:why_is_there_more_than_one_daemon_process_on_my_machine)[为什么我的机器上有多个守护进程？](#sec:why_is_there_more_than_one_daemon_process_on_my_machine)

Gradle为什么要创建一个新的守护程序，而不使用已经运行的守护程序，有几个原因。基本规则是，如果没有可用的空闲或兼容守护程序，则Gradle将启动新的守护程序。Gradle将杀死任何闲置了3小时或更长时间的守护程序，因此您不必担心手动清理它们。

idle（ 空闲）


空闲的守护程序是当前未执行构建或未执行其他有用工作的守护程序。

compatible（兼容）


兼容的守护程序是可以（或可以使其）满足所请求构建环境的要求的守护程序。用于执行构建的Java运行时是构建环境的一个示例方面。另一个示例是构建运行时所需的JVM系统属性集。

守护程序可能无法满足请求的构建环境的某些方面。如果守护程序与Java 8运行时一起运行，但是请求的环境要求Java
10，则该守护程序不兼容，必须启动另一个守护程序。而且，一旦JVM启动，就无法更改Java运行时的某些属性。例如，不可能更改`-Xmx1024m`正在运行的JVM的内存分配（例如），默认文本编码，默认语言环境等。

“请求的构建环境”通常是从构建客户端（例如Gradle命令行客户端，IDE等）环境的各个方面隐式构建的，并通过命令行开关和设置显式构建的。有关如何指定和控制[构建环境](https://docs.gradle.org/6.7.1/userguide/build_environment.html#build_environment)的详细信息，请参见[构建](https://docs.gradle.org/6.7.1/userguide/build_environment.html#build_environment)环境。

以下JVM系统属性实际上是不可变的。如果请求的构建环境需要这些属性中的任何一个，且其值与守护程序的JVM具有的该属性的值不同，则守护程序不兼容。

  * file.encoding

  * 用户语言

  * 用户国家

  * 用户变量

  * java.io.tmpdir

  * javax.net.ssl.keyStore

  * javax.net.ssl.keyStorePassword

  * javax.net.ssl.keyStoreType

  * javax.net.ssl.trustStore

  * javax.net.ssl.trustStorePassword

  * javax.net.ssl.trustStoreType

  * com.sun.management.jmxremote

由启动参数控制的以下JVM属性也实际上是不可变的。为了使守护程序兼容，请求的构建环境和守护程序的环境的相应属性必须完全匹配。

  * 最大堆大小（即-Xmx JVM参数）

  * 最小堆大小（即-Xms JVM参数）

  * 引导类路径（即-Xbootclasspath参数）

  * “断言”状态（即-ea参数）

所需的Gradle版本是所请求构建环境的另一方面。守护进程与特定的Gradle运行时耦合。在使用不同Gradle版本的会话中处理多个Gradle项目是导致有多个正在运行的Daemon进程的常见原因。

### [](#sec:how_much_memory_does_the_daemon_use_and_can_i_give_it_more)[守护程序使用多少内存，我可以给它更多空间吗？](#sec:how_much_memory_does_the_daemon_use_and_can_i_give_it_more)

如果请求的构建环境未指定最大堆大小，则守护程序将使用最多512MB的堆。它将使用JVM的默认最小堆大小。对于大多数构建来说，512MB绰绰有余。包含数百个子项目的较大内部版本，大量配置和源代码可能需要或在具有更多内存的情况下表现更好。

要增加守护程序可以使用的内存量，请在请求的构建环境中指定适当的标志。有关详细信息，请参见[构建环境](https://docs.gradle.org/6.7.1/userguide/build_environment.html#build_environment)。

### [](#sec:how_can_i_stop_a_daemon)[如何停止守护程序？](#sec:how_can_i_stop_a_daemon)

闲置3个小时或更短的时间后，守护进程将自动终止。如果您希望在此之前停止Daemon进程，则可以通过操作系统终止该进程或运行`gradle
--stop`命令。该`--stop`开关使Gradle请求 _所有_ 正在运行 _的，与用于运行命令的Gradle版本相同的_ Daemon进程自行终止。

### [](#sec:what_can_go_wrong_with_daemon)[守护程序会出什么问题？](#sec:what_can_go_wrong_with_daemon)

在日常开发中，使Daemon健壮，透明且不引人注目的工程方面已经付出了相当大的努力。但是，守护进程有时会被破坏或耗尽。Gradle构建从多个源执行任意代码。虽然Gradle本身是为Daemon设计并经过严格测试的，但用户构建脚本和第三方插件可能会通过内存泄漏或全局状态损坏等缺陷破坏Daemon进程的稳定性。

通过运行无法正确释放资源的构建，还可能破坏守护程序（通常是构建环境）的稳定性。当使用Microsoft
Windows时，这是一个特别棘手的问题，因为它对读取或写入后无法关闭文件的程序的宽容度较小。

Gradle主动监视堆使用情况，并尝试检测何时泄漏开始耗尽守护程序中的可用堆空间。当检测到问题时，Gradle守护程序将完成当前正在运行的构建，并在下一个构建中主动重新启动该守护程序。默认情况下启用此监视，但是可以通过将`org.gradle.daemon.performance.enable-
monitoring`system属性设置为false来禁用此监视。

如果怀疑Daemon进程变得不稳定，则可以将其杀死。回想一下，`--no-
daemon`可以为构建指定开关，以防止使用守护程序。这对于诊断守护程序是否实际上是问题的元凶很有用。

## [](#sec:tools_and_ides)[工具和IDE](#sec:tools_and_ides)

IDE和其他工具用于与Gradle集成的[Gradle Tooling API](https://docs.gradle.org/6.7.1/userguide/third_party_integration.html#embedding)
_始终_ 使用Gradle守护程序执行构建。如果要在IDE中执行Gradle构建，则使用的是Gradle Daemon，而无需为您的环境启用它。

## [](#sec:how_does_the_gradle_daemon_make_builds_faster)[Gradle守护程序如何使构建更快？](#sec:how_does_the_gradle_daemon_make_builds_faster)

Gradle守护程序是一个 _长期存在的_
构建过程。在两次构建之间，它空闲地等待下一次构建。这具有明显的好处，即对于多个构建只需要一次将Gradle加载到内存中，而不是对于每个构建一次。这本身就是一项重大的性能优化，但并非止于此。

关于现代JVM性能的故事的重要部分是运行时代码优化。例如，HotSpot（Oracle提供的JVM实现，用作OpenJDK的基础）在运行时对代码进行优化。优化是渐进的，不是瞬时的。也就是说，在执行过程中对代码进行了逐步优化，这意味着纯粹由于此优化过程而使得后续构建可以更快。使用HotSpot进行的实验表明，需要5到10次构建才能稳定优化。守护程序的第一个构建和第10个构建之间的可感知构建时间差异可能非常明显。

守护程序还允许跨构建更有效地进行内存缓存。例如，构建所需的类（例如，插件，构建脚本）可以保存在构建之间的内存中。同样，Gradle可以维护构建数据的内存缓存，例如用于增量构建的任务输入和输出的哈希值。

为了检测文件系统上的更改并计算需要重建的内容，Gradle会在每次构建过程中收集有关文件系统状态的大量信息。当[监视的文件系统](#sec:daemon_watch_fs)被启用，后台程序可以重新使用来自上次编译已经收集到的信息。这可以为增量构建节省大量时间，而两次构建之间对文件系统的更改次数通常很少。

## [](#sec:daemon_watch_fs)[观看文件系统](#sec:daemon_watch_fs)

为了检测文件系统上的更改并计算需要重建的内容，Gradle会在每次构建（即 _虚拟文件系统_ ）期间收集有关内存中 _文件系统的信息_
。通过监视文件系统，Gradle可以使虚拟文件系统与文件系统保持同步，即使在两次构建之间也是如此。这样做可以使守护程序节省时间来从磁盘重建虚拟文件系统以进行下一个构建。对于增量构建，构建之间通常只有几处更改。因此，增量构建可以重用上次构建中的大部分虚拟文件系统，并从监视文件系统中获得最大收益。

Gradle使用操作系统功能来监视文件系统。它支持以下操作系统和文件系统上的功能：

  * Windows 10和NTFS

  * 使用ext3和ext4的Linux（Ubuntu 16.04或更高版本，CentOS 8或更高版本，Red Hat Enterprise Linux 8或更高版本，Amazon Linux 2），

  * APFS和HFS +上的macOS 10.14（Mojave）或更高版本。

不支持NFS和SMB等网络文件系统。不支持FAT文件系统。

监视文件系统是一项实验性功能，默认情况下处于禁用状态。您可以通过以下两种方式启用该功能：

`--watch-fs`在命令行上运行

    

这样就可以仅监视此构建的文件系统。

放在`org.gradle.vfs.watch=true`你的`gradle.properties`

    

除非使用显式禁用，否则这可以监视文件系统的所有构建`--no-watch-fs`。

### [](#sec:daemon_watch_fs_troubleshooting)[对文件系统监视进行故障排除](#sec:daemon_watch_fs_troubleshooting)

局限性

    

当前监视文件系统具有以下限制：

  * 如果您的构建中有符号链接，则这些位置将不会带来性能优势。

  * 在Windows上，我们不支持网络驱动器（它们可能会工作，但我们尚未对其进行测试）。

启用详细日志记录

    

您可以使用该`org.gradle.vfs.verbose`标志指示Gradle提供有关虚拟文件系统状态以及从文件系统接收到的事件的更多信息。这将在构建的开始和结束时产生以下输出：

    
    
    $ gradle assemble --watch-fs -Dorg.gradle.vfs.verbose=true
    Received 3 file system events since last build while watching 1 hierarchies
    Virtual file system retained information about 2 files, 2 directories and 0 missing files since last build
    > Task :compileJava NO-SOURCE
    > Task :processResources NO-SOURCE
    > Task :classes UP-TO-DATE
    > Task :jar UP-TO-DATE
    > Task :assemble UP-TO-DATE
    
    BUILD SUCCESSFUL in 58ms
    1 actionable task: 1 up-to-date
    Received 5 file system events during the current build while watching 1 hierarchies
    Virtual file system retains information about 3 files, 2 directories and 2 missing files until next build

请注意，在Windows和macOS上，Gradle可能会报告自上次构建以来所收到的更改，即使您没有进行任何更改。这些是有关Gradle自己的缓存更改的无害通知，可以安全地忽略。

Gradle无法接收我的某些更改

    

_如果您遇到这种情况， 请[在Gradle社区Slack上告诉我们](https://gradle-community.slack.com/app_redirect?channel=file-system-watching)。_
如果您的构建正确地声明了其输入和输出，则不应发生这种情况。因此，这可能是我们需要修复的错误，或者您的构建缺少某些输入或输出的声明。

由于丢失状态，VFS状态被丢弃

    

如果您`Dropped VFS state due to lost state`在构建期间收到消息，
 _请[ 在](https://gradle-community.slack.com/app_redirect?channel=file-system-watching)发生这种情况时
 [在Gradle社区Slack上告诉我们](https://gradle-community.slack.com/app_redirect?channel=file-system-watching)。_ 此消息表示：

  * 守护程序收到一些未知的文件系统事件，

  * 发生了太多更改，并且监视API无法处理它。

在这两种情况下，构建都无法从文件系统监视中受益。

macOS上的打开文件过多

    

如果您`java.io.IOException: Too many open
files`在macOS上收到错误消息，则需要提高打开文件的限制，请参见[此处](https://superuser.com/a/443168/8117)。

### [](#sec:inotify_watches_limit)[Linux专用说明](#sec:inotify_watches_limit)

文件系统监视在Linux上使用[inotify](http://en.wikipedia.org/wiki/Inotify)。根据构建的大小，可能有必要增加inotify限制。如果您使用的是IDE，那么过去您可能已经不得不增加限制。

文件系统监视对每个监视目录使用一个inotify监视。您可以通过运行以下命令查看每个用户当前的inotify监视限制：

    
    
    cat /proc/sys/fs/inotify/max_user_watches

要将限制增加到例如512K手表，请运行以下命令：

    
    
    echo 524288 | sudo tee -a /etc/sysctl.conf
    sudo sysctl -p --system

每个使用的inotify手表最多占用1KB的内存。假设inotify使用所有512K手表，那么将使用大约500MB的空间来监视文件系统。如果环境受内存限制，则可能要禁用文件系统监视。

