# 使用Worker API开发并行任务

version 6.9-20201127230042+0000

内容

  * [创建一个自定义任务类](#create_a_custom_task_class)
  * [转换为Worker API](#converting_to_the_worker_api)
  * [更改隔离模式](#changing_the_isolation_mode)
  * [创建一个工人守护进程](#creating_a_worker_daemon)

Worker
API提供了将任务动作的执行分解为离散的工作单元，然后同时并异步执行该工作的功能。这使Gradle可以充分利用可用资源并更快地完成构建。本节将引导您完成将现有自定义任务转换为使用Worker
API的过程。

本节假定您了解编写Gradle自定义任务的基础。有关该主题的更多信息，请参阅[关于定制任务](https://docs.gradle.org/nightly/userguide/custom_tasks.html)的[部分](https://docs.gradle.org/nightly/userguide/custom_tasks.html)。

您将首先创建一个自定义任务类，该类为可配置文件集生成MD5哈希值。然后，您将转换此自定义任务以使用Worker
API。然后，我们将探索以不同级别的隔离度运行任务。在此过程中，您将了解Worker API的基础知识及其提供的功能。

## [](#create_a_custom_task_class)[创建一个自定义任务类](#create_a_custom_task_class)

首先，您需要创建一个自定义任务，该任务会生成一组可配置文件的MD5哈希值。

在新目录中，创建一个`buildSrc/build.gradle(.kts)`文件。

`Groovy``Kotlin`

buildSrc / build.gradle

    
    
    repositories {
        jcenter()
    }
    
    dependencies {
        implementation 'commons-io:commons-io:2.5'
        implementation 'commons-codec:commons-codec:1.9' __**( 1)**
    }

buildSrc/build.gradle.kts

    
    
    repositories {
        jcenter()
    }
    
    dependencies {
        implementation("commons-io:commons-io:2.5")
        implementation("commons-codec:commons-codec:1.9") __**( 1)**
    }
╔═════════════════════════════  
①  您的自定义任务类将使用[Apache Commons Codec](https://commons.apache.org/proper/commons-codec/)生成MD5哈希。  
╚═════════════════════════════   
  
╔═════════════════════════════  
如果您不熟悉`buildSrc`，这是一个特殊目录，可让您定义和构建应在构建脚本中使用的自定义类。有关更多信息，请参见[有关组织构建逻辑的部分](https://docs.gradle.org/nightly/userguide/organizing_gradle_projects.html#sec:build_sources)。  
╚═════════════════════════════   
  
现在，在`buildSrc/src/main/java`目录中创建一个自定义任务类。您应该为此类命名`CreateMD5`。

buildSrc / src / main / java / CreateMD5.java

    
    
    import org.apache.commons.codec.digest.DigestUtils;
    import org.apache.commons.io.FileUtils;
    import org.gradle.api.file.DirectoryProperty;
    import org.gradle.api.file.RegularFile;
    import org.gradle.api.provider.Provider;
    import org.gradle.api.tasks.OutputDirectory;
    import org.gradle.api.tasks.SourceTask;
    import org.gradle.api.tasks.TaskAction;
    import org.gradle.workers.WorkerExecutor;
    
    import java.io.File;
    import java.io.FileInputStream;
    import java.io.InputStream;
    
    abstract public class CreateMD5 extends SourceTask { __**( 1)**
    
        @OutputDirectory
        abstract public DirectoryProperty getDestinationDirectory(); __**( 2)**
    
        @TaskAction
        public void createHashes() {
            for (File sourceFile : getSource().getFiles()) { __**( 3)**
                try {
                    InputStream stream = new FileInputStream(sourceFile);
                    System.out.println("Generating MD5 for " + sourceFile.getName() + "...");
                    // Artificially make this task slower.
                    Thread.sleep(3000); __**( 4)**
                    Provider<RegularFile> md5File = getDestinationDirectory().file(sourceFile.getName() + ".md5");  __**( 5)**
                    FileUtils.writeStringToFile(md5File.get().getAsFile(), DigestUtils.md5Hex(stream), (String) null);
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
═════════════════════════════  
① [SourceTask](https://docs.gradle.org/nightly/javadoc/org/gradle/api/tasks/SourceTask.html)是一种便利类型，用于对一组源文件进行操作的任务。  
②  任务的输出将进入已配置的目录。  
③  该任务遍历定义为“源文件”的所有文件，并为每个文件创建MD5哈希。  
④  插入人工睡眠以模拟对大型文件进行哈希处理（示例文件不会那么大）。  
⑤  每个文件的MD5哈希将写入输出目录中，扩展名为“ md5”。  
╚═════════════════════════════  
接下来，创建一个`build.gradle(.kts)`注册新`CreateMD5`任务的。

`Groovy``Kotlin`

build.gradle

    
    
    plugins { id 'base' } __**( 1)**
    
    tasks.register("md5", CreateMD5) {
        destinationDirectory = project.layout.buildDirectory.dir("md5") __**( 2)**
        source(project.layout.projectDirectory.file('src')) __**( 3)**
    }

build.gradle.kts

    
    
    plugins { id("base") } __**( 1)**
    
    tasks.register<CreateMD5>("md5") {
        destinationDirectory.set(project.layout.buildDirectory.dir("md5")) __**( 2)**
        source(project.layout.projectDirectory.file("src")) __**( 3)**
    }
═════════════════════════════  
①  应用`base`插件，以便您有一个`clean`任务可用来删除输出。  
②  MD5哈希文件将写入`build/md5`。  
③  此任务将为目录中的每个文件生成MD5哈希文件`src`。  
╚═════════════════════════════  
现在，您将需要一些源来生成MD5哈希值。在src目录中创建3个文件：

src / einstein.txt

    
    
    Intellectual growth should commence at birth and cease only at death.

src / feynman.txt

    
    
    I was born not knowing and have had only a little time to change that here and there.

src / oppenheimer.txt

    
    
    No man should escape our universities without knowing how little he knows.

此时，您可以尝试一下任务：

    
    
    $ gradle md5

您应该看到类似于以下内容的输出：

    
    
    > Task :md5
    Generating MD5 for einstein.txt...
    Generating MD5 for feynman.txt...
    Generating MD5 for oppenheimer.txt...
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

在`build/md5`目录中，您现在应该看到带有`md5`扩展名的相应文件，该扩展名包含`src`目录中文件的MD5哈希。请注意，该任务至少需要9秒钟才能运行，因为它一次哈希一个文件（即，每个文件约3秒哈希3个文件）。

## [](#converting_to_the_worker_api)[转换为Worker
API](#converting_to_the_worker_api)

尽管此任务按顺序处理每个文件，但是每个文件的处理都独立于任何其他文件。如果这项工作并行完成，并且可以利用多个处理器，那就太好了。这是Worker
API可以提供帮助的地方。

首先，您需要定义一个接口，该接口代表每个工作单元的参数并扩展`org.gradle.workers.WorkParameters`。为了生成MD5散列文件，工作单元将需要两个参数：要散列的文件和向其写入散列的文件。但是，无需创建具体的实现，因为Gradle将在运行时为我们生成一个实现。

buildSrc / src / main / java / MD5WorkParameters.java

    
    
    import org.gradle.api.file.RegularFileProperty;
    import org.gradle.workers.WorkParameters;
    
    public interface MD5WorkParameters extends WorkParameters {
        RegularFileProperty getSourceFile(); __**( 1)**
        RegularFileProperty getMD5File();
    }
═════════════════════════════  
①  使用`Property`对象表示源和MD5哈希文件。  
╚═════════════════════════════  
  
其次，您需要将自定义任务的一部分重构为一个单独的类，该部分将为每个文件进行工作。此类是您的“工作单元”实现，它应该是Extended的抽象类`org.gradle.workers.WorkAction`。

buildSrc / src / main / java / GenerateMD5.java

    
    
    import org.apache.commons.codec.digest.DigestUtils;
    import org.apache.commons.io.FileUtils;
    import org.gradle.workers.WorkAction;
    
    import java.io.File;
    import java.io.FileInputStream;
    import java.io.InputStream;
    
    public abstract class GenerateMD5 implements WorkAction<MD5WorkParameters> { __**( 1)**
        @Override
        public void execute() {
            try {
                File sourceFile = getParameters().getSourceFile().getAsFile().get();
                File md5File = getParameters().getMD5File().getAsFile().get();
                InputStream stream = new FileInputStream(sourceFile);
                System.out.println("Generating MD5 for " + sourceFile.getName() + "...");
                // Artificially make this task slower.
                Thread.sleep(3000);
                FileUtils.writeStringToFile(md5File, DigestUtils.md5Hex(stream), (String) null);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
    }
═════════════════════════════  
①  不要实现该`getParameters()`方法-Gradle将在运行时注入此方法。  
╚═════════════════════════════  
  
现在，您应该更改自定义任务类，以将工作提交给{api-reference} org / gradle / workers /
WorkerExecutor.html [WorkerExecutor]，而不是自己完成工作。

buildSrc / src / main / java / CreateMD5.java

    
    
    import org.gradle.api.Action;
    import org.gradle.api.file.RegularFile;
    import org.gradle.api.provider.Provider;
    import org.gradle.api.tasks.*;
    import org.gradle.workers.*;
    import org.gradle.api.file.DirectoryProperty;
    
    import javax.inject.Inject;
    import java.io.File;
    
    abstract public class CreateMD5 extends SourceTask {
    
        @OutputDirectory
        abstract public DirectoryProperty getDestinationDirectory();
    
        @Inject
        abstract public WorkerExecutor getWorkerExecutor(); __**( 1)**
    
        @TaskAction
        public void createHashes() {
            WorkQueue workQueue = getWorkerExecutor().noIsolation(); __**( 2)**
    
            for (File sourceFile : getSource().getFiles()) {
                Provider<RegularFile> md5File = getDestinationDirectory().file(sourceFile.getName() + ".md5");
                workQueue.submit(GenerateMD5.class, parameters -> { __**( 3)**
                    parameters.getSourceFile().set(sourceFile);
                    parameters.getMD5File().set(md5File);
                });
            }
        }
    }
═════════════════════════════  
① 您需要具有[WorkerExecutor](https://docs.gradle.org/nightly/javadoc/org/gradle/workers/WorkerExecutor.html)服务才能提交您的工作。创建一个带注释的抽象getter方法`javax.inject.Inject`，当任务创建时，Gradle将在运行时注入服务。  
②  在提交工作之前，您需要获取`WorkQueue`具有所需隔离模式的对象。稍后我们将详细讨论隔离模式。  
③  在提交工作单元时，在这种情况下，请指定工作单元实现`GenerateMD5`并配置其参数。  
╚═════════════════════════════  
此时，您应该可以再次尝试您的任务。

    
    
    $ gradle clean md5
    
    > Task :md5
    Generating MD5 for einstein.txt...
    Generating MD5 for feynman.txt...
    Generating MD5 for oppenheimer.txt...
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

尽管工作单元是并行执行的，但MD5哈希文件的生成顺序可能不同，结果应与以前相同。但是，您应该注意的一件事是任务运行得更快。这是因为Worker
API对每个文件并行而不是按顺序执行MD5计算。

## [](#changing_the_isolation_mode)[更改隔离模式](#changing_the_isolation_mode)

隔离模式控制Gradle将工作项彼此隔离以及与Gradle运行时的其余部分隔离的强烈程度。有三种方法，`WorkerExecutor`即控制这样的：
`noIsolation()`，`classLoaderIsolation()`和`processIsolation()`。的`noIsolation()`模式是最低的隔离级别，它将阻止工作单元更改项目状态。这是最快的隔离模式，因为它需要最少的开销来设置要执行的工作项，因此您可能希望在简单的情况下使用此模式。但是，它将对所有工作单元使用单个共享的类加载器。这意味着每个工作单元都可能通过静态类状态相互影响。这也意味着每个工作单元都使用buildscript类路径上相同版本的库。如果希望用户能够将任务配置为与其他（但兼容）版本的
[Apache Commons Codec](https://commons.apache.org/proper/commons-
codec/)库一起运行，则需要使用其他隔离模式。

首先，您需要将依赖项更改`buildSrc/build.gradle`为`compileOnly`。这告诉Gradle在构建类时应使用此依赖项，但不应将其放在构建脚本的类路径中。

`Groovy``Kotlin`

buildSrc / build.gradle

    
    
    repositories {
        jcenter()
    }
    
    dependencies {
        implementation 'commons-io:commons-io:2.5'
        compileOnly 'commons-codec:commons-codec:1.9'
    }

buildSrc/build.gradle.kts

    
    
    repositories {
        jcenter()
    }
    
    dependencies {
        implementation("commons-io:commons-io:2.5")
        compileOnly("commons-codec:commons-codec:1.9")
    }

接下来，您将需要更改`CreateMD5`任务，以允许用户配置他们要使用的编解码器库的版本。它将在运行时解析适当的库版本，并配置工作程序以使用该版本。该`classLoaderIsolation()`方法告诉Gradle使用隔离的类加载器在线程中运行此工作。

buildSrc / src / main / java / CreateMD5.java

    
    
    import org.gradle.api.Action;
    import org.gradle.api.file.ConfigurableFileCollection;
    import org.gradle.api.file.DirectoryProperty;
    import org.gradle.api.file.RegularFile;
    import org.gradle.api.provider.Provider;
    import org.gradle.api.tasks.*;
    import org.gradle.process.JavaForkOptions;
    import org.gradle.workers.*;
    
    import javax.inject.Inject;
    import java.io.File;
    import java.util.Set;
    
    abstract public class CreateMD5 extends SourceTask {
    
        @InputFiles
        abstract public ConfigurableFileCollection getCodecClasspath(); __**( 1)**
    
        @OutputDirectory
        abstract public DirectoryProperty getDestinationDirectory();
    
        @Inject
        abstract public WorkerExecutor getWorkerExecutor();
    
        @TaskAction
        public void createHashes() {
            WorkQueue workQueue = getWorkerExecutor().classLoaderIsolation(workerSpec -> {
                workerSpec.getClasspath().from(getCodecClasspath()); __**( 2)**
            });
    
            for (File sourceFile : getSource().getFiles()) {
                Provider<RegularFile> md5File = getDestinationDirectory().file(sourceFile.getName() + ".md5");
                workQueue.submit(GenerateMD5.class, parameters -> {
                    parameters.getSourceFile().set(sourceFile);
                    parameters.getMD5File().set(md5File);
                });
            }
        }
    }
═════════════════════════════  
①  公开编解码器库类路径的输入属性。  
② 创建工作队列时，请在[ClassLoaderWorkerSpec](https://docs.gradle.org/nightly/javadoc/org/gradle/workers/ClassLoaderWorkerSpec.html)上配置类路径。  
╚═════════════════════════════  
接下来，您将需要配置构建，以便它具有一个存储库以在任务执行时查找编解码器版本。我们还将创建一个依赖项来从此存储库解析我们的编解码器库。

`Groovy``Kotlin`

build.gradle

    
    
    plugins { id 'base' }
    
    repositories {
        jcenter() __**( 1)**
    }
    
    configurations.create('codec') { __**( 2)**
        attributes {
            attribute(Usage.USAGE_ATTRIBUTE, objects.named(Usage, Usage.JAVA_RUNTIME))
        }
        visible = false
        canBeConsumed = false
        canBeResolved = true
    }
    
    dependencies {
        codec 'commons-codec:commons-codec:1.10' __**( 3)**
    }
    
    tasks.register('md5', CreateMD5) {
        codecClasspath.from(configurations.codec) __**( 4)**
        destinationDirectory = project.layout.buildDirectory.dir('md5')
        source(project.layout.projectDirectory.file('src'))
    }

build.gradle.kts

    
    
    plugins { id("base") }
    
    repositories {
        jcenter() __**( 1)**
    }
    
    val codec = configurations.create("codec") { __**( 2)**
        attributes {
            attribute(Usage.USAGE_ATTRIBUTE, objects.named(Usage.JAVA_RUNTIME))
        }
        isVisible = false
        isCanBeConsumed = false
        isCanBeResolved = true
    }
    
    dependencies {
        codec("commons-codec:commons-codec:1.10") __**( 3)**
    }
    
    tasks.register<CreateMD5>("md5") {
        codecClasspath.from(codec) __**( 4)**
        destinationDirectory.set(project.layout.buildDirectory.dir("md5"))
        source(project.layout.projectDirectory.file("src"))
    }
═════════════════════════════  
①  添加一个存储库以解析编解码器库-该存储库可以与用于构建`CreateMD5`任务类的存储库不同。  
②  添加 _配置_ 以解析我们的编解码器库版本。  
③  配置[Apache Commons Codec](https://commons.apache.org/proper/commons-codec/)的备用兼容版本。  
④  配置`md5`任务以将配置用作其类路径。请注意，在实际执行任务之前，不会解析该配置。  
╚═════════════════════════════  
现在，如果您运行任务，则可以使用编解码器库的配置版本按预期工作：

    
    
    $ gradle clean md5
    
    > Task :md5
    Generating MD5 for einstein.txt...
    Generating MD5 for feynman.txt...
    Generating MD5 for oppenheimer.txt...
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

## [](#creating_a_worker_daemon)[创建一个工人守护进程](#creating_a_worker_daemon)

有时，在执行工作项时需要进一步隔离。例如，外部库可能依赖于要设置的某些系统属性，这些属性可能在工作项之间发生冲突。或者库可能与Gradle所运行的JDK版本不兼容，并且可能需要与其他版本一起运行。Worker
API可以使用`processIsolation()`导致工作在单独的“
worker守护程序”中执行的方法来容纳这一点。这些工作程序守护程序进程将在构建之间持久存在，并且可以在后续构建中重用。但是，如果系统资源不足，Gradle将停止所有未使用的工作程序守护程序。

要利用worker守护程序，只需`processIsolation()`在创建时使用方法`WorkQueue`。您可能还需要为新过程配置自定义设置。

buildSrc / src / main / java / CreateMD5.java

    
    
    import org.gradle.api.Action;
    import org.gradle.api.file.ConfigurableFileCollection;
    import org.gradle.api.file.DirectoryProperty;
    import org.gradle.api.file.RegularFile;
    import org.gradle.api.provider.Provider;
    import org.gradle.api.tasks.*;
    import org.gradle.process.JavaForkOptions;
    import org.gradle.workers.*;
    
    import javax.inject.Inject;
    import java.io.File;
    import java.util.Set;
    
    abstract public class CreateMD5 extends SourceTask {
    
        @InputFiles
        abstract public ConfigurableFileCollection getCodecClasspath(); __**( 1)**
    
        @OutputDirectory
        abstract public DirectoryProperty getDestinationDirectory();
    
        @Inject
        abstract public WorkerExecutor getWorkerExecutor();
    
        @TaskAction
        public void createHashes() {
            __**( 1)**
            WorkQueue workQueue = getWorkerExecutor().processIsolation(workerSpec -> {
                workerSpec.getClasspath().from(getCodecClasspath());
                workerSpec.forkOptions(options -> {
                    options.setMaxHeapSize("64m"); __**( 2)**
                });
            });
    
            for (File sourceFile : getSource().getFiles()) {
                Provider<RegularFile> md5File = getDestinationDirectory().file(sourceFile.getName() + ".md5");
                workQueue.submit(GenerateMD5.class, parameters -> {
                    parameters.getSourceFile().set(sourceFile);
                    parameters.getMD5File().set(md5File);
                });
            }
        }
    }
════════════════════════════  
①  将隔离模式更改为`PROCESS`。  
②  为新过程设置`{api-reference}org/gradle/process/JavaForkOptions.html[JavaForkOptions]`
╚═════════════════════════════  
现在，您应该能够运行您的任务，它将按预期运行，但可以使用辅助守护程序：

    $ gradle clean md5
    
    > Task :md5
    Generating MD5 for einstein.txt...
    Generating MD5 for feynman.txt...
    Generating MD5 for oppenheimer.txt...
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

请注意，执行时间可能会有些长。这是因为Gradle必须为每个辅助守护程序启动一个新进程，这很昂贵。但是，如果再次运行任务，则会看到它运行得更快。这是因为在初始构建期间启动的辅助守护程序已经保留，并且可以在后续构建期间立即使用。

    
    
    $ gradle clean md5
    
    > Task :md5
    Generating MD5 for einstein.txt...
    Generating MD5 for feynman.txt...
    Generating MD5 for oppenheimer.txt...
    
    BUILD SUCCESSFUL in 0s
    1 actionable task: 1 executed

