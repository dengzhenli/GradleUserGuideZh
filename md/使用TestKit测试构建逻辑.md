# 使用TestKit测试构建逻辑


内容

  * [用法](#用法)
  * [使用Gradle Runner进行功能测试](#使用Gradle%20Runner进行功能测试)
  * [将被测插件放入测试版本](#将被测插件放入测试版本)
  * [控制构建环境](#控制构建环境)
  * [用于测试的Gradle版本](#用于测试的Gradle版本)
  * [调试构建逻辑](#调试构建逻辑)
  * [使用构建缓存进行测试](#使用构建缓存进行测试)

Gradle TestKit（也称为TestKit）是一个有助于测试Gradle插件和一般构建逻辑的库。目前，它专注于 _功能_
测试。也就是说，通过将其作为程序执行的构建的一部分进行测试来测试构建逻辑。随着时间的流逝，TestKit可能会扩展以方便其他类型的测试。

## [用法](#用法)

要使用TestKit，请在插件的版本中包含以下内容：

示例1.声明TestKit依赖项

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        testImplementation gradleTestKit()
    }

build.gradle.kts

    
    
    dependencies {
        testImplementation(gradleTestKit())
    }

其中`gradleTestKit()`包含TestKit的类以及[Gradle Tooling
API客户端](/md/Gradle和第三方工具_md#使用Tooling%20API嵌入Gradle)。它不包括[JUnit](http://junit.org/)，[TestNG](http://testng.org/)或任何其他测试执行框架的版本。必须明确声明这种依赖性。

示例2.声明JUnit依赖项

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        testImplementation 'junit:junit:4.13'
    }

build.gradle.kts

    
    
    dependencies {
        testImplementation("junit:junit:4.13")
    }

## [使用Gradle%20Runner进行功能测试](#使用Gradle%20Runner进行功能测试)

所述[GradleRunner](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html)便于编程执行Gradle构建，和检查结果。

可以（例如以编程方式或从模板中）创建人为构建的练习“被测逻辑”。然后可以潜在地以各种方式（例如，任务和参数的不同组合）执行构建。然后可以通过断言以下内容（可能组合使用）来验证逻辑的正确性：

  * 构建的输出；

  * 构建的日志（即控制台输出）；

  * 由构建执行的任务集及其结果（例如FAILED，UP-TO-DATE等）。

在创建并配置了[Runner](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#build--)实例之后，可以根据预期结果通过[GradleRunner.build（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#build--)或[GradleRunner.buildAndFail（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#buildAndFail--)方法执行构建。

以下内容演示了Java JUnit测试中Gradle Runner的用法：

### [示例：将GradleRunner与Java和JUnit一起使用](#示例：将GradleRunner与Java和JUnit一起使用)

BuildLogicFunctionalTest.java

    
    
    import org.gradle.testkit.runner.BuildResult;
    import org.gradle.testkit.runner.GradleRunner;
    import org.junit.Before;
    import org.junit.Rule;
    import org.junit.Test;
    import org.junit.rules.TemporaryFolder;
    
    import java.io.BufferedWriter;
    import java.io.File;
    import java.io.FileWriter;
    import java.io.IOException;
    import java.util.Collections;
    
    import static org.junit.Assert.assertEquals;
    import static org.junit.Assert.assertTrue;
    
    import static org.gradle.testkit.runner.TaskOutcome.*;
    
    public class BuildLogicFunctionalTest {
        @Rule public final TemporaryFolder testProjectDir = new TemporaryFolder();
        private File settingsFile;
        private File buildFile;
    
        @Before
        public void setup() throws IOException {
            settingsFile = testProjectDir.newFile("settings.gradle");
            buildFile = testProjectDir.newFile("build.gradle");
        }
    
        @Test
        public void testHelloWorldTask() throws IOException {
            writeFile(settingsFile, "rootProject.name = 'hello-world'");
            String buildFileContent = "task helloWorld {" +
                                      "    doLast {" +
                                      "        println 'Hello world!'" +
                                      "    }" +
                                      "}";
            writeFile(buildFile, buildFileContent);
    
            BuildResult result = GradleRunner.create()
                .withProjectDir(testProjectDir.getRoot())
                .withArguments("helloWorld")
                .build();
    
            assertTrue(result.getOutput().contains("Hello world!"));
            assertEquals(SUCCESS, result.task(":helloWorld").getOutcome());
        }
    
        private void writeFile(File destination, String content) throws IOException {
            BufferedWriter output = null;
            try {
                output = new BufferedWriter(new FileWriter(destination));
                output.write(content);
            } finally {
                if (output != null) {
                    output.close();
                }
            }
        }
    }

以下内容演示了在Kotlin JUnit测试中Gradle运行器的用法：

### [示例：将GradleRunner与Kotlin和JUnit一起使用](#示例：将GradleRunner与Kotlin和JUnit一起使用)

BuildLogicFunctionalTest.kt

    
    
    import org.gradle.testkit.runner.BuildResult
    import org.gradle.testkit.runner.GradleRunner
    import org.gradle.testkit.runner.TaskOutcome
    import org.junit.Assert.assertEquals
    import org.junit.Assert.assertTrue
    import org.junit.Before
    import org.junit.Rule
    import org.junit.Test
    import org.junit.rules.TemporaryFolder
    import kotlin.jvm.JvmField
    import java.io.File
    
    class BuildLogicFunctionalTest {
    
        @Rule @JvmField
        val testProjectDir: TemporaryFolder = TemporaryFolder()
        private lateinit var settingsFile: File
        private lateinit var buildFile: File
    
        @Before
        fun setup() {
            settingsFile = testProjectDir.newFile("settings.gradle.kts")
            buildFile = testProjectDir.newFile("build.gradle.kts")
        }
    
        @Test
        fun `test helloWorld task`() {
    
            settingsFile.writeText("""
                rootProject.name = "hello-world"
            """.trimIndent())
            buildFile.writeText("""
                tasks.register("helloWorld") {
                    doLast {
                        println("Hello world!")
                    }
                }
            """.trimIndent())
    
            val result = GradleRunner.create()
                .withProjectDir(testProjectDir.root)
                .withArguments("helloWorld")
                .build()
    
            assertTrue(result.output.contains("Hello world!"))
            assertEquals(TaskOutcome.SUCCESS, result.task(":helloWorld")?.outcome)
        }
    }

可以使用任何测试执行框架。

由于Gradle构建脚本也可以用Groovy编程语言编写，因此用Groovy编写Gradle功能测试通常是一种有效的选择。此外，建议使用（基于Groovy的）[Spock测试执行框架，](https://code.google.com/p/spock/)因为它比JUnit的使用具有许多引人注目的功能。

以下内容演示了Groovy Spock测试中Gradle运行器的用法：

### [示例：将GradleRunner与Groovy和Spock结合使用](#示例：将GradleRunner与Groovy和Spock结合使用)

BuildLogicFunctionalTest.groovy

    
    
    import org.gradle.testkit.runner.GradleRunner
    import static org.gradle.testkit.runner.TaskOutcome.*
    import org.junit.Rule
    import org.junit.rules.TemporaryFolder
    import spock.lang.Specification
    
    class BuildLogicFunctionalTest extends Specification {
        @Rule TemporaryFolder testProjectDir = new TemporaryFolder()
        File settingsFile
        File buildFile
    
        def setup() {
            settingsFile = testProjectDir.newFile('settings.gradle')
            buildFile = testProjectDir.newFile('build.gradle')
        }
    
        def "hello world task prints hello world"() {
            given:
            settingsFile << "rootProject.name = 'hello-world'"
            buildFile << """
                task helloWorld {
                    doLast {
                        println 'Hello world!'
                    }
                }
            """
    
            when:
            def result = GradleRunner.create()
                .withProjectDir(testProjectDir.root)
                .withArguments('helloWorld')
                .build()
    
            then:
            result.output.contains('Hello world!')
            result.task(":helloWorld").outcome == SUCCESS
        }
    }

实施任何自定义构建逻辑（如插件和任务类型）通常是一种惯例，该逻辑本质上比独立项目中的外部类复杂。这种方法背后的主要驱动力是将编译后的代码捆绑到一个JAR文件中，将其发布到二进制存储库中，并在各个项目中重复使用。

## [将被测插件放入测试版本](#将被测插件放入测试版本)

GradleRunner使用[Tooling
API](/md/Gradle和第三方工具_md#使用Tooling%20API嵌入Gradle)执行构建。这意味着构建是在单独的过程中执行的（即，执行测试的过程不是同一过程）。因此，测试版本与测试过程不会共享相同的类路径或类加载器，并且测试代码不会隐式地提供给测试版本。

从2.13版开始，Gradle提供了一种常规机制，可以将被测代码注入测试版本中。

### [使用JavaGradle插件开发插件自动注入](#使用JavaGradle插件开发插件自动注入)

在[Java的Gradle插件开发的插件](https://docs.gradle.org/6.7.1/userguide/java_gradle_plugin.html#java_gradle_plugin)可以用来协助Gradle插件的开发。从Gradle
2.13版本开始，该插件提供了与TestKit的直接集成。当应用于项目时，该插件会自动将`gradleTestKit()`依赖项添加到测试编译配置中。此外，它会自动为测试中的代码生成类路径，并通过[GradleRunner.withPluginClasspath（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withPluginClasspath--)将其注入`GradleRunner`用户创建的任何实例。重要的是要注意，该机制当前
_仅_
在使用[插件DSL](/md/使用Gradle插件_md#通过插件DSL应用插件)应用被测插件
_时才_
有效。如果[目标Gradle版本](#用于测试的Gradle版本) 在2.8之前的版本中，不会执行自动插件类路径注入。

该插件使用以下约定来应用TestKit依赖项并注入类路径：

  * 包含正在测试的代码的源集： `sourceSets.main`

  * 用于注入插件类路径的源集： `sourceSets.test`

这些约定中的任何一个都可以在[GradlePluginDevelopmentExtension](https://docs.gradle.org/6.7.1/javadoc/org/gradle/plugin/devel/GradlePluginDevelopmentExtension.html)类的帮助下进行重新配置。

以下基于Groovy的示例演示了如何使用Java Gradle插件开发插件应用的标准约定自动注入插件类路径。

例子3.使用Java Gradle Development插件生成插件元数据

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'groovy'
        id 'java-gradle-plugin'
    }
    
    dependencies {
        testImplementation('org.spockframework:spock-core:1.3-groovy-2.4') {
            exclude module: 'groovy-all'
        }
    }

build.gradle.kts

    
    
    plugins {
        groovy
        `java-gradle-plugin`
    }
    
    dependencies {
        testImplementation("org.spockframework:spock-core:1.3-groovy-2.4") {
            exclude(module = "groovy-all")
        }
    }

### [示例：自动将测试类下的代码注入到测试版本中](#示例：自动将测试类下的代码注入到测试版本中)

src / test / groovy / org / gradle / sample / BuildLogicFunctionalTest.groovy

    
    
    def "hello world task prints hello world"() {
        given:
        settingsFile << "rootProject.name = 'hello-world'"
        buildFile << """
            plugins {
                id 'org.gradle.sample.helloworld'
            }
        """
    
        when:
        def result = GradleRunner.create()
            .withProjectDir(testProjectDir.root)
            .withArguments('helloWorld')
            .withPluginClasspath()
            .build()
    
        then:
        result.output.contains('Hello world!')
        result.task(":helloWorld").outcome == SUCCESS
    }

以下构建脚本演示了如何为使用自定义`Test`源集的项目重新配置Java Gradle插件开发插件提供的约定。

示例4.重新配置Java Gradle Development插件的类路径生成约定

`Groovy``Kotlin`

build.gradle

    
    
    plugins {
        id 'groovy'
        id 'java-gradle-plugin'
    }
    
    sourceSets {
        functionalTest {
            groovy {
                srcDir file('src/functionalTest/groovy')
            }
            resources {
                srcDir file('src/functionalTest/resources')
            }
            compileClasspath += sourceSets.main.output + configurations.testRuntimeClasspath
            runtimeClasspath += output + compileClasspath
        }
    }
    
    task functionalTest(type: Test) {
        testClassesDirs = sourceSets.functionalTest.output.classesDirs
        classpath = sourceSets.functionalTest.runtimeClasspath
    }
    
    check.dependsOn functionalTest
    
    gradlePlugin {
        testSourceSets sourceSets.functionalTest
    }
    
    dependencies {
        functionalTestImplementation('org.spockframework:spock-core:1.3-groovy-2.4') {
            exclude module: 'groovy-all'
        }
    }

build.gradle.kts

    
    
    plugins {
        groovy
        `java-gradle-plugin`
    }
    
    sourceSets {
        create("functionalTest") {
            withConvention(GroovySourceSet::class) {
                groovy {
                    srcDir(file("src/functionalTest/groovy"))
                }
            }
            resources {
                srcDir(file("src/functionalTest/resources"))
            }
            compileClasspath += sourceSets.main.get().output + configurations.testRuntimeClasspath
            runtimeClasspath += output + compileClasspath
        }
    }
    
    tasks.register<Test>("functionalTest") {
        testClassesDirs = sourceSets["functionalTest"].output.classesDirs
        classpath = sourceSets["functionalTest"].runtimeClasspath
    }
    
    tasks.check { dependsOn(tasks["functionalTest"]) }
    
    gradlePlugin {
        testSourceSets(sourceSets["functionalTest"])
    }
    
    dependencies {
        "functionalTestImplementation"("org.spockframework:spock-core:1.3-groovy-2.4") {
            exclude(module = "groovy-all")
        }
    }

### [使用2_13之前的Gradle版本](#使用2_13之前的Gradle版本)

对于较早版本的Gradle（2.13之前的版本），可以通过一些额外的配置来手动使被测代码可用。下面的示例演示如何让生成的文件包含被测代码的实现类路径，并使其在测试运行时可用。

例子5.使测试类路径下的代码可用于测试

`Groovy``Kotlin`

build.gradle

    
    
    // Write the plugin's classpath to a file to share with the tests
    task createClasspathManifest {
        def outputDir = file("$buildDir/$name")
    
        inputs.files(sourceSets.main.runtimeClasspath)
            .withPropertyName("runtimeClasspath")
            .withNormalizer(ClasspathNormalizer)
        outputs.dir(outputDir)
            .withPropertyName("outputDir")
    
        doLast {
            outputDir.mkdirs()
            file("$outputDir/plugin-classpath.txt").text = sourceSets.main.runtimeClasspath.join("\n")
        }
    }
    
    // Add the classpath file to the test runtime classpath
    dependencies {
        testRuntimeOnly files(createClasspathManifest)
    }

build.gradle.kts

    
    
    // Write the plugin's classpath to a file to share with the tests
    tasks.register("createClasspathManifest") {
        val outputDir = file("$buildDir/$name")
    
        inputs.files(sourceSets.main.get().runtimeClasspath)
            .withPropertyName("runtimeClasspath")
            .withNormalizer(ClasspathNormalizer::class)
        outputs.dir(outputDir)
            .withPropertyName("outputDir")
    
        doLast {
            outputDir.mkdirs()
            file("$outputDir/plugin-classpath.txt").writeText(sourceSets.main.get().runtimeClasspath.joinToString("\n"))
        }
    }
    
    // Add the classpath file to the test runtime classpath
    dependencies {
        testRuntimeOnly(files(tasks["createClasspathManifest"]))
    }

然后，测试可以读取该值，并使用[GradleRunner.withPluginClasspath（java.lang.Iterable）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withPluginClasspath-
java.lang.Iterable-)方法将类路径注入测试版本。然后可以使用该类路径通过插件DSL在测试版本中定位插件（请参阅[插件](/md/使用Gradle插件.md#plugins)）。通过插件DSL应用插件需要定义插件标识符。以下是在Spock
Framework`setup()`方法中执行此操作的示例（在Groovy中），该方法类似于JUnit`@Before`方法。

### [示例：将测试类下的代码注入测试版本](#示例：将测试类下的代码注入测试版本)

src / test / groovy / org / gradle / sample / BuildLogicFunctionalTest.groovy

    
    
        List<File> pluginClasspath
    
        def setup() {
            settingsFile = testProjectDir.newFile('settings.gradle')
            buildFile = testProjectDir.newFile('build.gradle')
    
            def pluginClasspathResource = getClass().classLoader.findResource("plugin-classpath.txt")
            if (pluginClasspathResource == null) {
                throw new IllegalStateException("Did not find plugin classpath resource, run `testClasses` build task.")
            }
    
            pluginClasspath = pluginClasspathResource.readLines().collect { new File(it) }
        }
    
        def "hello world task prints hello world"() {
            given:
            buildFile << """
                plugins {
                    id 'org.gradle.sample.helloworld'
                }
            """
    
            when:
            def result = GradleRunner.create()
                .withProjectDir(testProjectDir.root)
                .withArguments('helloWorld')
                .withPluginClasspath(pluginClasspath)
                .build()
    
            then:
            result.output.contains('Hello world!')
            result.task(":helloWorld").outcome == SUCCESS
        }

当作为Gradle构建的一部分执行功能测试时，此方法效果很好。从IDE执行功能测试时，还有一些额外的注意事项。即，类路径清单文件指向Gradle而不是IDE生成的类文件等。这意味着在更改被测代码的源代码之后，必须由Gradle重新编译源代码。同样，如果被测代码的有效类路径发生变化，则必须重新生成清单。无论哪种情况，执行`testClasses`构建任务都将确保一切都是最新的。

一些IDE提供了一个方便的选项，可以将“测试类路径的生成和执行”委派给构建。在IntelliJ中，您可以在``偏好设置...''>``构建，执行，部署''>``构建工具''>``Gradle''>``Runner''>``委派IDE生成/运行操作''中找到该选项。请查阅IDE的文档以获取更多信息。

### [使用2_8之前的Gradle版本](#使用2_8之前的Gradle版本)

当使用早于2.8的Gradle版本执行构建时，[GradleRunner.withPluginClasspath（java.lang.Iterable）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withPluginClasspath-
java.lang.Iterable-)方法将不起作用（请参阅[用于测试的版本](#sub:gradle-
runner-gradle-version)），因为在此类Gradle版本中不支持此功能。

相反，必须通过构建脚本本身注入代码。下面的示例演示了如何完成此操作。

### [示例：将测试类下的代码注入2_8之前的Gradle版本的测试版本中](#示例：将测试类下的代码注入测试版本_for_gradle_versions_prior_to_2_8)

src / test / groovy / org / gradle / sample / BuildLogicFunctionalTest.groovy

    
    
        List<File> pluginClasspath
    
        def setup() {
            settingsFile = testProjectDir.newFile('settings.gradle')
            buildFile = testProjectDir.newFile('build.gradle')
    
            def pluginClasspathResource = getClass().classLoader.findResource("plugin-classpath.txt")
            if (pluginClasspathResource == null) {
                throw new IllegalStateException("Did not find plugin classpath resource, run `testClasses` build task.")
            }
    
            pluginClasspath = pluginClasspathResource.readLines().collect { new File(it) }
        }
    
        def "hello world task prints hello world with pre Gradle 2.8"() {
            given:
            def classpathString = pluginClasspath
                .collect { it.absolutePath.replace('\\', '\\\\') } // escape backslashes in Windows paths
                .collect { "'$it'" }
                .join(", ")
    
            buildFile << """
                buildscript {
                    dependencies {
                        classpath files($classpathString)
                    }
                }
                apply plugin: "org.gradle.sample.helloworld"
            """
    
            when:
            def result = GradleRunner.create()
                .withProjectDir(testProjectDir.root)
                .withArguments('helloWorld')
                .withGradleVersion("2.7")
                .build()
    
            then:
            result.output.contains('Hello world!')
            result.task(":helloWorld").outcome == SUCCESS
        }

## [控制构建环境](#控制构建环境)

运行程序通过在JVM的temp目录内的目录（即`java.io.tmpdir`，通常由system属性指定的位置）中指定专用的“工作目录”，在隔离的环境中执行测试构建`/tmp`。默认的Gradle用户主目录（例如`~/.gradle/gradle.properties`）中的任何配置都不会用于测试执行。TestKit并未公开对环境的各个方面进行细粒度控制的机制（例如JDK）。未来版本的TestKit将提供改进的配置选项。

TestKit使用专用的守护程序进程，这些进程在测试执行后会自动关闭。

## [用于测试的Gradle版本](#用于测试的Gradle版本)

Gradle运行器需要Gradle发行版才能执行构建。TestKit并不依赖于Gradle的所有实现。

默认情况下，运行程序将尝试根据从何处`GradleRunner`加载类来查找Gradle发行版。也就是说，期望该类是从Gradle发行版加载的，就像使用`gradleTestKit()`依赖声明时一样。

当将跑步者用作 _Gradle执行_ 的测试的一部分（例如执行`test`插件项目的任务）时，跑步者将使用与执行测试相同的发行版。当将运行程序用作
_IDE执行_ 的测试的一部分时，将使用与导入项目时相同的Gradle发行版。这意味着该插件将使用与其构建时相同的Gradle版本进行有效测试。

另外，可以通过以下任何一种`GradleRunner`方法指定要使用的Gradle的不同版本和特定版本：

  * [GradleRunner.withGradleVersion（java.lang.String）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withGradleVersion-java.lang.String-)

  * [GradleRunner.withGradleInstallation（java.io.File）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withGradleInstallation-java.io.File-)

  * [GradleRunner.withGradleDistribution（java.net.URI）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withGradleDistribution-java.net.URI-)

它可以潜在地用于跨Gradle版本测试构建逻辑。以下内容演示了编写为Groovy Spock测试的跨版本兼容性测试：

### [示例：指定用于测试执行的Gradle版本](#示例：指定用于测试执行的Gradle版本)

BuildLogicFunctionalTest.groovy

    
    
    import org.gradle.testkit.runner.GradleRunner
    import static org.gradle.testkit.runner.TaskOutcome.*
    import org.junit.Rule
    import org.junit.rules.TemporaryFolder
    import spock.lang.Specification
    import spock.lang.Unroll
    
    class BuildLogicFunctionalTest extends Specification {
        @Rule final TemporaryFolder testProjectDir = new TemporaryFolder()
        File settingsFile
        File buildFile
    
        def setup() {
            settingsFile = testProjectDir.newFile('settings.gradle')
            buildFile = testProjectDir.newFile('build.gradle')
        }
    
        @Unroll
        def "can execute hello world task with Gradle version #gradleVersion"() {
            given:
            buildFile << """
                task helloWorld {
                    doLast {
                        logger.quiet 'Hello world!'
                    }
                }
            """
    
            when:
            def result = GradleRunner.create()
                .withGradleVersion(gradleVersion)
                .withProjectDir(testProjectDir.root)
                .withArguments('helloWorld')
                .build()
    
            then:
            result.output.contains('Hello world!')
            result.task(":helloWorld").outcome == SUCCESS
    
            where:
            gradleVersion << ['2.6', '2.7']
        }
    }

### [使用不同的Gradle版本进行测试时的功能支持](#使用不同的Gradle版本进行测试时的功能支持)

可以使用GradleRunner在Gradle
1.0及更高版本中执行构建。但是，早期版本不支持某些运行器功能。在这种情况下，跑步者在尝试使用功能时会抛出异常。

下表列出了对使用的Gradle版本敏感的功能。

表1. Gradle版本兼容性 

特征 | 最低版本 | 描述  
---|---|---  
检查执行的任务|2.5|使用[BuildResult.getTasks（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/BuildResult.html#getTasks--)和类似方法检查执行的任务。  
[插件类路径注入](#将被测插件放入测试版本)|2.8|通过[GradleRunner.withPluginClasspath（java.lang.Iterable）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withPluginClasspath-java.lang.Iterable-)注入测试中的代码。  
[在调试模式下检查构建输出](#调试构建逻辑)|2.9|使用[BuildResult.getOutput（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/BuildResult.html#getOutput--)在调试模式下运行时检查构建的文本输出。  
[自动插件类路径注入](#使用JavaGradle插件开发插件自动注入)|2.13|通过应用JavaGradle插件开发插件，通过[GradleRunner.withPluginClasspath（）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withPluginClasspath--)自动注入被测代码。  
设置构建要使用的环境变量。|3.5|Gradle Tooling API仅支持在更高版本中设置环境变量。  
  
## [调试构建逻辑](#调试构建逻辑)

跑步者使用[Tooling API](/md/Gradle和第三方工具_md#使用Tooling%20API嵌入Gradle)执行构建。这意味着构建是在单独的过程中执行的（即，执行测试的过程不是同一过程）。因此，以调试方式执行
_测试_ 不允许您调试调试逻辑。在IDE中设置的任何断点都不会因测试版本执行的代码而跳闸。

TestKit提供了两种不同的方式来启用调试模式：

  * 设置“`org.gradle.testkit.debug`系统属性”来`true`为JVM _使用_ 的`GradleRunner`（与所述流道执行即不生成）;

  * 调用[GradleRunner.withDebug（boolean）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withDebug-boolean-)方法。

当需要启用调试支持而不对流道配置进行临时更改时，可以使用系统属性方法。大多数IDE提供了设置JVM系统属性以执行测试的功能，并且可以使用此功能来设置此系统属性。

## [使用构建缓存进行测试](#使用构建缓存进行测试)

要在测试中启用[构建缓存](/md/构建缓存.md#build_cache)，可以将`--build-
cache`参数传递给[GradleRunner](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html)或使用[启用构建缓存中](/md/构建缓存_md#启用构建缓存)描述的其他方法之一。然后，可以在缓存插件的自定义任务时检查任务结果[TaskOutcome.FROM_CACHE](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/TaskOutcome.html#FROM_CACHE)。此结果仅对Gradle
3.5及更高版本有效。

### [示例：测试可缓存任务](#示例：测试可缓存任务)

BuildLogicFunctionalTest.groovy

    
    
    def "cacheableTask is loaded from cache"() {
        given:
        buildFile << """
            plugins {
                id 'org.gradle.sample.helloworld'
            }
        """
    
        when:
        def result = runner()
            .withArguments( '--build-cache', 'cacheableTask')
            .build()
    
        then:
        result.task(":cacheableTask").outcome == SUCCESS
    
        when:
        new File(testProjectDir.root, 'build').deleteDir()
        result = runner()
            .withArguments( '--build-cache', 'cacheableTask')
            .build()
    
        then:
        result.task(":cacheableTask").outcome == FROM_CACHE
    }

请注意，TestKit在测试之间重用了Gradle用户主目录（请参阅[GradleRunner.withTestKitDir（java.io.File）](https://docs.gradle.org/6.7.1/javadoc/org/gradle/testkit/runner/GradleRunner.html#withTestKitDir-java.io.File-)），其中包含本地构建缓存的默认位置。为了使用构建缓存进行测试，应在测试之间清理构建缓存目录。完成此操作的最简单方法是将本地构建缓存配置为使用临时目录。

### [示例：在测试之间清理构建缓存](#示例：在测试之间清理构建缓存)

BuildLogicFunctionalTest.groovy

    
    
    @Rule final TemporaryFolder testProjectDir = new TemporaryFolder()
    File buildFile
    File localBuildCacheDirectory
    
    def setup() {
        localBuildCacheDirectory = testProjectDir.newFolder('local-cache')
        testProjectDir.newFile('settings.gradle') << """
            buildCache {
                local {
                    directory '${localBuildCacheDirectory.toURI()}'
                }
            }
        """
        buildFile = testProjectDir.newFile('build.gradle')
    }

