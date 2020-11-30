# 目录和文件Gradle的使用


内容

  * [Gradle用户主目录](#dir:gradle_user_home)
  * [项目根目录](#dir:project_root)

Gradle使用两个主要目录来执行和管理其工作：[Gradle用户主目录](#dir:gradle_user_home)和[Project根目录](#dir:project_root)。以下两节描述了每个文件中存储的内容以及如何清理临时文件和目录。

<h2 id = '#dir:gradle_user_home'> <a href = '#dir:gradle_user_home'>Gradle用户主目录</a> </h2>

Gradle用户主目录（`$USER_HOME/.gradle`默认情况下）用于存储全局配置属性和初始化脚本以及缓存和日志文件。它的大致结构如下：

    
    
    ├──caches  __**(1)**
    │├──4.8 __**(2)**
    │├──4.9 __**(2)**
    │├──⋮
    │├──jars-3 __**(3)**
    │└──modules-2 __**(3)**
    ├──daemon  __**(4)**
    │├──⋮
    │├──4.8
    │└──4.9
    ├──init.d __**(5)**
    │└──my-setup.gradle
    ├──jdks __**(6)**
    │├──⋮
    │└──jdk-14.0.2 + 12
    ├──wrapper
    │└──dists __**(7)**
    │├──⋮
    │├──gradle-4.8-bin
    │├──gradle-4.9-all
    │└──gradle-4.9-bin
    └──gradle.properties __**(8)**
    
════════════════════════════    
①  全局缓存目录（适用于所有非项目专用的目录） 
②  特定于版本的缓存（例如，支持增量构建）  
③  共享缓存（例如，依赖项的工件）  
④ [Gradle守护程序的](/md/Gradle守护程序.md#gradle_daemon) 注册表和日志[](/md/Gradle守护程序.md#gradle_daemon)  
⑤ 全局[初始化脚本](/md/初始化脚本.md#init_scripts)  
⑥ [工具链支持](/md/JVM项目的工具链.md#sec:provisioning) 下载的JDK[](/md/JVM项目的工具链.md#sec:provisioning)  
⑦  [Gradle Wrapper](/md/gradle_wrapper.md#gradle_wrapper) 下载的发行版[](/md/gradle_wrapper.md#gradle_wrapper)  
⑧ 全局[Gradle配置属性](/md/Gradle环境搭建.md#sec:gradle_configuration_properties)  

╚═════════════════════════════    
<h3 id = '#dir:gradle_user_home:cache_cleanup'> <a href = '#dir:gradle_user_home:cache_cleanup'>清理缓存和分发</a> </h3>

从版本4.10开始，Gradle会自动清除其用户主目录。当Gradle守护程序停止或关闭时，清理将在后台运行。如果使用`--no-
daemon`，它将在带有可视进度指示器的构建会话之后在前台运行。

定期（最多每24小时）应用以下清理策略：

  * `caches/<gradle-version>/`检查特定于版本的缓存是否仍在使用。如果未使用，则在闲置30天后删除发布版本的目录，在闲置7天后删除快照版本。

  * 检查`caches/`（例如`jars-*`）中的共享缓存是否仍在使用。如果没有Gradle版本仍在使用它们，则将其删除。

  * 检查当前Gradle版本在`caches/`（例如`jars-3`或`modules-2`）中使用的共享缓存中的文件上次访问的时间。取决于是可以在本地重新创建文件，还是必须再次从远程存储库下载文件，此文件将在之后被删除。分别是7天或30天没有访问。

  * `wrapper/dists/`检查Gradle发行版中是否仍在使用它们，即是否存在相应的特定于版本的缓存目录。未使用的发行版将被删除。

<h2 id = '#dir:project_root'> <a href = '#dir:project_root'>项目根目录</a> </h2>

项目根目录包含项目中所有的源文件。此外，它还包含Gradle生成的文件和目录，例如`.gradle`和`build`。通常前者通常签入到源代码管理中，而后者是Gradle用于支持增量构建等功能的临时文件。总体而言，典型项目根目录的结构大致如下：

    
    
    ├──.gradle __**(1)**
    │├──4.8 __**(2)**
    │├──4.9 __**(2)**
    │└──⋮
    ├──build  __**(3)**
    ├──gradle
    │└──wrapper  __**(4)**
    ├──gradle.properties __**(5)**
    ├──gradlew __**(6)**
    ├──gradlew.bat __**(6)**
    ├──settings.gradle _或_ settings.gradle.kts __ **(7)**
    ├──子项目一 __**(8)**
    | └──build.gradle _或_ build.gradle.kts __ **(9)**
    ├──子项目二 __**(8)**
    | └──build.gradle _或_ build.gradle.kts __ **(9)**
    └──

①  Gradle生成的特定于项目的缓存目录  
②  特定于版本的缓存（例如，支持增量构建）  
③  Gradle在其中生成所有构建工件的该项目的构建目录。  
④ 包含JAR文件和[Gradle包装器的](/md/gradle_wrapper.md#gradle_wrapper) 配置[](/md/gradle_wrapper.md#gradle_wrapper)  
⑤ 项目特定的[Gradle配置属性](/md/Gradle环境搭建.md#sec:gradle_configuration_properties)  
⑥  使用[Gradle Wrapper](/md/gradle_wrapper.md#gradle_wrapper) 执行构建的脚本[](/md/gradle_wrapper.md#gradle_wrapper)  
⑦ 该项目的[设置文件](/md/构建生命周期.md#sec:settings_file) ， 其中定义子项目的列表  
⑧  通常，一个项目被组织成一个或多个子项目  
⑨  每个子项目都有自己的Gradle构建脚本  
  
╚═════════════════════════════    
<h3 id = '#dir:project_root:cache_cleanup'> <a href = '#dir:project_root:cache_cleanup'>项目缓存清理</a> </h3>

从版本4.10开始，Gradle会自动清除特定于项目的缓存目录。构建项目后，`.gradle/<gradle-
version>/`将定期（最多每24小时）检查特定于版本的缓存目录中是否仍在使用它们。如果7天未使用它们，则将其删除。

