# 使用TestKit测试构建逻辑

内容

* [用法](#sec:testkit_usage)
* [使用Gradle Runner进行功能测试](#sec:functional_testing_with_the_gradle_runner)
* [将被测插件放入测试版本](#sub:test-kit-classpath-injection)
* [控制构建环境](#sec:controlling_the_build_environment)
* [用于测试的Gradle版本](#sub:gradle-runner-gradle-version)
* [调试构建逻辑](#sub:test-kit-debug)
* [使用构建缓存进行测试](#sub:test-kit-build-cache)

Gradle TestKit（又名TestKit）是一个有助于测试Gradle插件和一般构建逻辑的库。目前，它专注于_功能_测试。也就是说，通过将其作为程序执行的构建的一部分进行测试来测试构建逻辑。随着时间的流逝，TestKit可能会扩展以方便其他类型的测试。

## [](#sec:testkit_usage)[用法](#sec:testkit_usage)

要使用TestKit，请在插件的版本中包含以下内容：

示例1.声明TestKit依赖项

`Groovy``Kotlin`

build.gradle

dependencies \{ testImplementation gradleTestKit\(\) \}

 

1

dependencies \{

2

 testImplementation gradleTestKit\(\)

3

\}

其中`gradleTestKit()`包含TestKit的类以及[Gradle Tooling API客户端]()。它不包括[JUnit](http://junit.org)，[TestNG](http://testng.org)或任何其他测试执行框架的版本。必须明确声明这种依赖性。

示例2.声明JUnit依赖项

`Groovy``Kotlin`

build.gradle

dependencies \{ testImplementation 'junit:junit:4.13' \}

 

1

dependencies \{

2

 testImplementation 'junit:junit:4.13'

3

\}

## [](#sec:functional_testing_with_the_gradle_runner)[使用Gradle Runner进行功能测试](#sec:functional_testing_with_the_gradle_runner)

所述[GradleRunner]()便于编程执行摇篮构建，和检查结果。

可以（例如以编程方式或从模板中）创建人为构建的练习“被测逻辑”。然后可以潜在地以各种方式（例如，任务和参数的不同组合）执行构建。然后可以通过断言以下内容（可能组合使用）来验证逻辑的正确性：

* 构建的输出；
* 构建的日志（即控制台输出）；
* 由构建执行的任务集及其结果（例如FAILED，UP-TO-DATE等）。

在创建并配置了[Runner]()实例之后，可以根据预期结果通过[GradleRunner.build（）]()或[GradleRunner.buildAndFail（）]()方法执行构建。

以下内容演示了Java JUnit测试中Gradle Runner的用法：

### [](#example_using_gradlerunner_with_java_and_junit)[示例：将GradleRunner与Java和JUnit一起使用](#example_using_gradlerunner_with_java_and_junit)

BuildLogicFunctionalTest.java

import org.gradle.testkit.runner.BuildResult; import org.gradle.testkit.runner.GradleRunner; import org.junit.Before; import org.junit.Rule; import org.junit.Test; import org.junit.rules.TemporaryFolder; import java.io.BufferedWriter; import java.io.File; import java.io.FileWriter; import java.io.IOException; import java.util.Collections; import static org.junit.Assert.assertEquals; import static org.junit.Assert.assertTrue; import static org.gradle.testkit.runner.TaskOutcome.\*; public class BuildLogicFunctionalTest \{ \@Rule public final TemporaryFolder testProjectDir = new TemporaryFolder\(\); private File settingsFile; private File buildFile; \@Before public void setup\(\) throws IOException \{ settingsFile = testProjectDir.newFile\("settings.gradle"\); buildFile = testProjectDir.newFile\("build.gradle"\); \} \@Test public void testHelloWorldTask\(\) throws IOException \{ writeFile\(settingsFile, "rootProject.name = 'hello-world'"\); String buildFileContent = "task helloWorld \{" + " doLast \{" + " println 'Hello world\!'" + " \}" + "\}"; writeFile\(buildFile, buildFileContent\); BuildResult result = GradleRunner.create\(\) .withProjectDir\(testProjectDir.getRoot\(\)\) .withArguments\("helloWorld"\) .build\(\); assertTrue\(result.getOutput\(\).contains\("Hello world\!"\)\); assertEquals\(SUCCESS, result.task\(":helloWorld"\).getOutcome\(\)\); \} private void writeFile\(File destination, String content\) throws IOException \{ BufferedWriter output = null; try \{ output = new BufferedWriter\(new FileWriter\(destination\)\); output.write\(content\); \} finally \{ if \(output \!= null\) \{ output.close\(\); \} \} \} \}

 

1

import org.gradle.testkit.runner.BuildResult;

2

import org.gradle.testkit.runner.GradleRunner;

3

import org.junit.Before;

4

import org.junit.Rule;

5

import org.junit.Test;

6

import org.junit.rules.TemporaryFolder;

7

8

import java.io.BufferedWriter;

9

import java.io.File;

10

import java.io.FileWriter;

11

import java.io.IOException;

12

import java.util.Collections;

13

14

import static org.junit.Assert.assertEquals;

15

import static org.junit.Assert.assertTrue;

16

17

import static org.gradle.testkit.runner.TaskOutcome.\*;

18

19

public class BuildLogicFunctionalTest \{

20

 \@Rule public final TemporaryFolder testProjectDir \= new TemporaryFolder\(\);

21

 private File settingsFile;

22

 private File buildFile;

23

24

 \@Before

25

 public void setup\(\) throws IOException \{

26

 settingsFile \= testProjectDir.newFile\("settings.gradle"\);

27

 buildFile \= testProjectDir.newFile\("build.gradle"\);

28

 \}

29

30

 \@Test

31

 public void testHelloWorldTask\(\) throws IOException \{

32

 writeFile\(settingsFile, "rootProject.name = 'hello-world'"\);

33

 String buildFileContent \= "task helloWorld \{" +

34

 "    doLast \{" +

35

 "        println 'Hello world\!'" +

36

 "    \}" +

37

 "\}";

38

 writeFile\(buildFile, buildFileContent\);

39

40

 BuildResult result \= GradleRunner.create\(\)

41

 .withProjectDir\(testProjectDir.getRoot\(\)\)

42

 .withArguments\("helloWorld"\)

43

 .build\(\);

44

45

 assertTrue\(result.getOutput\(\).contains\("Hello world\!"\)\);

46

 assertEquals\(SUCCESS, result.task\(":helloWorld"\).getOutcome\(\)\);

47

 \}

48

49

 private void writeFile\(File destination, String content\) throws IOException \{

50

 BufferedWriter output \= null;

51

 try \{

52

 output \= new BufferedWriter\(new FileWriter\(destination\)\);

53

 output.write\(content\);

54

 \} finally \{

55

 if \(output \!= null\) \{

56

 output.close\(\);

57

 \}

58

 \}

59

 \}

60

\}

以下内容演示了在Kotlin JUnit测试中Gradle运行器的用法：

### [](#example_using_gradlerunner_with_kotlin_and_junit)[示例：将GradleRunner与Kotlin和JUnit一起使用](#example_using_gradlerunner_with_kotlin_and_junit)

BuildLogicFunctionalTest.kt

import org.gradle.testkit.runner.BuildResult import org.gradle.testkit.runner.GradleRunner import org.gradle.testkit.runner.TaskOutcome import org.junit.Assert.assertEquals import org.junit.Assert.assertTrue import org.junit.Before import org.junit.Rule import org.junit.Test import org.junit.rules.TemporaryFolder import kotlin.jvm.JvmField import java.io.File class BuildLogicFunctionalTest \{ \@Rule \@JvmField val testProjectDir: TemporaryFolder = TemporaryFolder\(\) private lateinit var settingsFile: File private lateinit var buildFile: File \@Before fun setup\(\) \{ settingsFile = testProjectDir.newFile\("settings.gradle.kts"\) buildFile = testProjectDir.newFile\("build.gradle.kts"\) \} \@Test fun \`test helloWorld task\`\(\) \{ settingsFile.writeText\(""" rootProject.name = "hello-world" """.trimIndent\(\)\) buildFile.writeText\(""" tasks.register\("helloWorld"\) \{ doLast \{ println\("Hello world\!"\) \} \} """.trimIndent\(\)\) val result = GradleRunner.create\(\) .withProjectDir\(testProjectDir.root\) .withArguments\("helloWorld"\) .build\(\) assertTrue\(result.output.contains\("Hello world\!"\)\) assertEquals\(TaskOutcome.SUCCESS, result.task\(":helloWorld"\)\?.outcome\) \} \}

 

1

import org.gradle.testkit.runner.BuildResult

2

import org.gradle.testkit.runner.GradleRunner

3

import org.gradle.testkit.runner.TaskOutcome

4

import org.junit.Assert.assertEquals

5

import org.junit.Assert.assertTrue

6

import org.junit.Before

7

import org.junit.Rule

8

import org.junit.Test

9

import org.junit.rules.TemporaryFolder

10

import kotlin.jvm.JvmField

11

import java.io.File

12

13

class BuildLogicFunctionalTest \{

14

15

 \@Rule \@JvmField

16

 val testProjectDir: TemporaryFolder \= TemporaryFolder\(\)

17

 private lateinit var settingsFile: File

18

 private lateinit var buildFile: File

19

20

 \@Before

21

 fun setup\(\) \{

22

 settingsFile \= testProjectDir.newFile\("settings.gradle.kts"\)

23

 buildFile \= testProjectDir.newFile\("build.gradle.kts"\)

24

 \}

25

26

 \@Test

27

 fun \`test helloWorld task\`\(\) \{

28

29

 settingsFile.writeText\("""

30

 rootProject.name \= "hello-world"

31

 """.trimIndent\(\)\)

32

 buildFile.writeText\("""

33

 tasks.register\("helloWorld"\) \{

34

 doLast \{

35

 println\("Hello world\!"\)

36

 \}

37

 \}

38

 """.trimIndent\(\)\)

39

40

 val result \= GradleRunner.create\(\)

41

 .withProjectDir\(testProjectDir.root\)

42

 .withArguments\("helloWorld"\)

43

 .build\(\)

44

45

 assertTrue\(result.output.contains\("Hello world\!"\)\)

46

 assertEquals\(TaskOutcome.SUCCESS, result.task\(":helloWorld"\)\?.outcome\)

47

 \}

48

\}

可以使用任何测试执行框架。

由于Gradle构建脚本也可以用Groovy编程语言编写，因此用Groovy编写Gradle功能测试通常是一种有效的选择。此外，建议使用（基于Groovy的）[Spock测试执行框架，](https://code.google.com/p/spock/)因为它比JUnit的使用具有许多引人注目的功能。

以下内容演示了Groovy Spock测试中Gradle Runner的用法：

### [](#example_using_gradlerunner_with_groovy_and_spock)[示例：将GradleRunner与Groovy和Spock结合使用](#example_using_gradlerunner_with_groovy_and_spock)

BuildLogicFunctionalTest.groovy

import org.gradle.testkit.runner.GradleRunner import static org.gradle.testkit.runner.TaskOutcome.\* import org.junit.Rule import org.junit.rules.TemporaryFolder import spock.lang.Specification class BuildLogicFunctionalTest extends Specification \{ \@Rule TemporaryFolder testProjectDir = new TemporaryFolder\(\) File settingsFile File buildFile def setup\(\) \{ settingsFile = testProjectDir.newFile\('settings.gradle'\) buildFile = testProjectDir.newFile\('build.gradle'\) \} def "hello world task prints hello world"\(\) \{ given: settingsFile \<\< "rootProject.name = 'hello-world'" buildFile \<\< """ task helloWorld \{ doLast \{ println 'Hello world\!' \} \} """ when: def result = GradleRunner.create\(\) .withProjectDir\(testProjectDir.root\) .withArguments\('helloWorld'\) .build\(\) then: result.output.contains\('Hello world\!'\) result.task\(":helloWorld"\).outcome == SUCCESS \} \}

 

1

import org.gradle.testkit.runner.GradleRunner

2

import static org.gradle.testkit.runner.TaskOutcome.\*

3

import org.junit.Rule

4

import org.junit.rules.TemporaryFolder

5

import spock.lang.Specification

6

7

class BuildLogicFunctionalTest extends Specification \{

8

 \@Rule TemporaryFolder testProjectDir \= new TemporaryFolder\(\)

9

 File settingsFile

10

 File buildFile

11

12

 def setup\(\) \{

13

 settingsFile \= testProjectDir.newFile\('settings.gradle'\)

14

 buildFile \= testProjectDir.newFile\('build.gradle'\)

15

 \}

16

17

 def "hello world task prints hello world"\(\) \{

18

 given:

19

 settingsFile \<\< "rootProject.name = 'hello-world'"

20

 buildFile \<\< """

21

 task helloWorld \{

22

 doLast \{

23

 println 'Hello world\!'

24

 \}

25

 \}

26

 """

27

28

 when:

29

 def result \= GradleRunner.create\(\)

30

 .withProjectDir\(testProjectDir.root\)

31

 .withArguments\('helloWorld'\)

32

 .build\(\)

33

34

 then:

35

 result.output.contains\('Hello world\!'\)

36

 result.task\(":helloWorld"\).outcome \== SUCCESS

37

 \}

38

\}

实施任何自定义构建逻辑（如插件和任务类型）通常是一种惯例，该逻辑本质上比独立项目中的外部类复杂。这种方法背后的主要驱动力是将编译后的代码捆绑到一个JAR文件中，将其发布到二进制存储库中，并在各个项目中重复使用。

## [](#sub:test-kit-classpath-injection)[将被测插件放入测试版本](#sub:test-kit-classpath-injection)

GradleRunner使用[Tooling API]()执行构建。这意味着构建是在单独的过程中执行的（即，执行测试的过程不是同一过程）。因此，测试版本与测试过程不会共享相同的类路径或类加载器，并且测试代码也不能隐式地用于测试版本。

从2.13版开始，Gradle提供了一种常规机制，可以将被测代码注入测试版本中。

### [](#sub:test-kit-automatic-classpath-injection)[使用Java Gradle插件开发插件自动注入](#sub:test-kit-automatic-classpath-injection)

在[Java的摇篮插件开发的插件]()可以用来协助摇篮插件的开发。从Gradle 2.13版本开始，该插件提供了与TestKit的直接集成。当应用于项目时，该插件会自动将`gradleTestKit()`依赖项添加到测试编译配置中。此外，它会自动为被测试的代码生成类路径，并通过[GradleRunner.withPluginClasspath（）]()将其注入`GradleRunner`用户创建的任何实例。重要的是要注意，该机制当前_仅_在使用[插件DSL]()应用被测插件_时才_有效。如果[目标Gradle版本](#sub:gradle-runner-gradle-version)在2.8之前的版本中，不会执行自动插件类路径注入。

该插件使用以下约定来应用TestKit依赖项并注入类路径：

* 包含正在测试的代码的源集：`sourceSets.main`
* 用于注入插件类路径的源集：`sourceSets.test`

这些约定中的任何一个都可以在[GradlePluginDevelopmentExtension]()类的帮助下进行重新配置。

以下基于Groovy的示例演示了如何使用Java Gradle插件开发插件应用的标准约定自动注入插件类路径。

示例3.使用Java Gradle Development插件生成插件元数据

`Groovy``Kotlin`

build.gradle

plugins \{ id 'groovy' id 'java-gradle-plugin' \} dependencies \{ testImplementation\('org.spockframework:spock-core:1.3-groovy-2.4'\) \{ exclude module: 'groovy-all' \} \}

 

1

plugins \{

2

 id 'groovy'

3

 id 'java-gradle-plugin'

4

\}

5

6

dependencies \{

7

 testImplementation\('org.spockframework:spock-core:1.3-groovy-2.4'\) \{

8

 exclude module: 'groovy-all'

9

 \}

10

\}

### [](#example_automatically_injecting_the_code_under_test_classes_into_test_builds)[示例：自动将测试类下的代码注入到测试版本中](#example_automatically_injecting_the_code_under_test_classes_into_test_builds)

src / test / groovy / org / gradle / sample / BuildLogicFunctionalTest.groovy

def "hello world task prints hello world"\(\) \{ given: settingsFile \<\< "rootProject.name = 'hello-world'" buildFile \<\< """ plugins \{ id 'org.gradle.sample.helloworld' \} """ when: def result = GradleRunner.create\(\) .withProjectDir\(testProjectDir.root\) .withArguments\('helloWorld'\) .withPluginClasspath\(\) .build\(\) then: result.output.contains\('Hello world\!'\) result.task\(":helloWorld"\).outcome == SUCCESS \}

 

1

def "hello world task prints hello world"\(\) \{

2

 given:

3

 settingsFile \<\< "rootProject.name = 'hello-world'"

4

 buildFile \<\< """

5

 plugins \{

6

 id 'org.gradle.sample.helloworld'

7

 \}

8

 """

9

10

 when:

11

 def result \= GradleRunner.create\(\)

12

 .withProjectDir\(testProjectDir.root\)

13

 .withArguments\('helloWorld'\)

14

 .withPluginClasspath\(\)

15

 .build\(\)

16

17

 then:

18

 result.output.contains\('Hello world\!'\)

19

 result.task\(":helloWorld"\).outcome \== SUCCESS

20

\}

以下构建脚本演示了如何为使用自定义`Test`源集的项目重新配置Java Gradle插件开发插件提供的约定。

示例4.重新配置Java Gradle Development插件的类路径生成约定

`Groovy``Kotlin`

build.gradle

plugins \{ id 'groovy' id 'java-gradle-plugin' \} sourceSets \{ functionalTest \{ groovy \{ srcDir file\('src/functionalTest/groovy'\) \} resources \{ srcDir file\('src/functionalTest/resources'\) \} compileClasspath += sourceSets.main.output + configurations.testRuntimeClasspath runtimeClasspath += output + compileClasspath \} \} task functionalTest\(type: Test\) \{ testClassesDirs = sourceSets.functionalTest.output.classesDirs classpath = sourceSets.functionalTest.runtimeClasspath \} check.dependsOn functionalTest gradlePlugin \{ testSourceSets sourceSets.functionalTest \} dependencies \{ functionalTestImplementation\('org.spockframework:spock-core:1.3-groovy-2.4'\) \{ exclude module: 'groovy-all' \} \}

 

1

plugins \{

2

 id 'groovy'

3

 id 'java-gradle-plugin'

4

\}

5

6

sourceSets \{

7

 functionalTest \{

8

 groovy \{

9

 srcDir file\('src/functionalTest/groovy'\)

10

 \}

11

 resources \{

12

 srcDir file\('src/functionalTest/resources'\)

13

 \}

14

 compileClasspath += sourceSets.main.output + configurations.testRuntimeClasspath

15

 runtimeClasspath += output + compileClasspath

16

 \}

17

\}

18

19

task functionalTest\(type: Test\) \{

20

 testClassesDirs \= sourceSets.functionalTest.output.classesDirs

21

 classpath \= sourceSets.functionalTest.runtimeClasspath

22

\}

23

24

check.dependsOn functionalTest

25

26

gradlePlugin \{

27

 testSourceSets sourceSets.functionalTest

28

\}

29

30

dependencies \{

31

 functionalTestImplementation\('org.spockframework:spock-core:1.3-groovy-2.4'\) \{

32

 exclude module: 'groovy-all'

33

 \}

34

\}

### [](#sec:working_with_gradle_versions_prior_to_213)[使用2.13之前的Gradle版本](#sec:working_with_gradle_versions_prior_to_213)

对于Gradle的早期版本（2.13之前的版本），可以通过一些额外的配置来手动使被测代码可用。下面的示例演示如何让生成的文件包含被测代码的实现类路径，并使其在测试运行时可用。

例子5.使测试类路径下的代码可用于测试

`Groovy``Kotlin`

build.gradle

// Write the plugin's classpath to a file to share with the tests task createClasspathManifest \{ def outputDir = file\("\$buildDir/\$name"\) inputs.files\(sourceSets.main.runtimeClasspath\) .withPropertyName\("runtimeClasspath"\) .withNormalizer\(ClasspathNormalizer\) outputs.dir\(outputDir\) .withPropertyName\("outputDir"\) doLast \{ outputDir.mkdirs\(\) file\("\$outputDir/plugin-classpath.txt"\).text = sourceSets.main.runtimeClasspath.join\("\\n"\) \} \} // Add the classpath file to the test runtime classpath dependencies \{ testRuntimeOnly files\(createClasspathManifest\) \}

 

1

// Write the plugin's classpath to a file to share with the tests

2

task createClasspathManifest \{

3

 def outputDir \= file\("\$buildDir/\$name"\)

4

5

 inputs.files\(sourceSets.main.runtimeClasspath\)

6

 .withPropertyName\("runtimeClasspath"\)

7

 .withNormalizer\(ClasspathNormalizer\)

8

 outputs.dir\(outputDir\)

9

 .withPropertyName\("outputDir"\)

10

11

 doLast \{

12

 outputDir.mkdirs\(\)

13

 file\("\$outputDir/plugin-classpath.txt"\).text \= sourceSets.main.runtimeClasspath.join\("\\n"\)

14

 \}

15

\}

16

17

// Add the classpath file to the test runtime classpath

18

dependencies \{

19

 testRuntimeOnly files\(createClasspathManifest\)

20

\}

然后，测试可以读取该值，并使用方法[GradleRunner.withPluginClasspath（java.lang.Iterable）]()将类路径注入测试版本。然后可以使用该类路径通过插件DSL在测试版本中定位插件（请参阅[插件]()）。通过插件DSL应用插件需要定义插件标识符。以下是从Spock Framework`setup()`方法中执行此操作的示例（在Groovy中），该方法类似于JUnit`@Before`方法。

### [](#example_injecting_the_code_under_test_classes_into_test_builds)[示例：将测试类下的代码注入测试版本](#example_injecting_the_code_under_test_classes_into_test_builds)

src / test / groovy / org / gradle / sample / BuildLogicFunctionalTest.groovy

List\<File> pluginClasspath def setup\(\) \{ settingsFile = testProjectDir.newFile\('settings.gradle'\) buildFile = testProjectDir.newFile\('build.gradle'\) def pluginClasspathResource = getClass\(\).classLoader.findResource\("plugin-classpath.txt"\) if \(pluginClasspathResource == null\) \{ throw new IllegalStateException\("Did not find plugin classpath resource, run \`testClasses\` build task."\) \} pluginClasspath = pluginClasspathResource.readLines\(\).collect \{ new File\(it\) \} \} def "hello world task prints hello world"\(\) \{ given: buildFile \<\< """ plugins \{ id 'org.gradle.sample.helloworld' \} """ when: def result = GradleRunner.create\(\) .withProjectDir\(testProjectDir.root\) .withArguments\('helloWorld'\) .withPluginClasspath\(pluginClasspath\) .build\(\) then: result.output.contains\('Hello world\!'\) result.task\(":helloWorld"\).outcome == SUCCESS \}

 

1

 List\<File\> pluginClasspath

2

3

 def setup\(\) \{

4

 settingsFile \= testProjectDir.newFile\('settings.gradle'\)

5

 buildFile \= testProjectDir.newFile\('build.gradle'\)

6

7

 def pluginClasspathResource \= getClass\(\).classLoader.findResource\("plugin-classpath.txt"\)

8

 if \(pluginClasspathResource \== null\) \{

9

 throw new IllegalStateException\("Did not find plugin classpath resource, run \`testClasses\` build task."\)

10

 \}

11

12

 pluginClasspath \= pluginClasspathResource.readLines\(\).collect \{ new File\(it\) \}

13

 \}

14

15

 def "hello world task prints hello world"\(\) \{

16

 given:

17

 buildFile \<\< """

18

 plugins \{

19

 id 'org.gradle.sample.helloworld'

20

 \}

21

 """

22

23

 when:

24

 def result \= GradleRunner.create\(\)

25

 .withProjectDir\(testProjectDir.root\)

26

 .withArguments\('helloWorld'\)

27

 .withPluginClasspath\(pluginClasspath\)

28

 .build\(\)

29

30

 then:

31

 result.output.contains\('Hello world\!'\)

32

 result.task\(":helloWorld"\).outcome \== SUCCESS

33

 \}

当作为Gradle构建的一部分执行功能测试时，此方法效果很好。从IDE执行功能测试时，还有一些额外的注意事项。即，类路径清单文件指向Gradle而不是IDE生成的类文件等。这意味着在更改被测代码的源代码之后，必须由Gradle重新编译源代码。同样，如果被测代码的有效类路径发生变化，则必须重新生成清单。无论哪种情况，执行`testClasses`构建任务都将确保一切都是最新的。

一些IDE提供了一个方便的选项，可以将“测试类路径的生成和执行”委派给构建。在IntelliJ中，您可以在\`\`偏好设置...''>\`\`构建，执行，部署''>\`\`构建工具''>\`\`Gradle''>\`\`Runner''>\`\`委派IDE生成/运行操作以进行gradle''下找到此选项。请查阅IDE的文档以获取更多信息。

### [](#sec:working_with_gradle_versions_prior_to_28)[使用2.8之前的Gradle版本](#sec:working_with_gradle_versions_prior_to_28)

当使用早于2.8的Gradle版本执行构建时，[GradleRunner.withPluginClasspath（java.lang.Iterable）]()方法将不起作用（请参阅[用于测试的版本](#sub:gradle-runner-gradle-version)），因为在此类Gradle版本中不支持此功能。

相反，必须通过构建脚本本身注入代码。下面的示例演示了如何完成此操作。

### [](#example_injecting_the_code_under_test_classes_into_test_builds_for_gradle_versions_prior_to_2_8)[示例：将测试类下的代码注入2.8之前的Gradle版本的测试版本中](#example_injecting_the_code_under_test_classes_into_test_builds_for_gradle_versions_prior_to_2_8)

src / test / groovy / org / gradle / sample / BuildLogicFunctionalTest.groovy

List\<File> pluginClasspath def setup\(\) \{ settingsFile = testProjectDir.newFile\('settings.gradle'\) buildFile = testProjectDir.newFile\('build.gradle'\) def pluginClasspathResource = getClass\(\).classLoader.findResource\("plugin-classpath.txt"\) if \(pluginClasspathResource == null\) \{ throw new IllegalStateException\("Did not find plugin classpath resource, run \`testClasses\` build task."\) \} pluginClasspath = pluginClasspathResource.readLines\(\).collect \{ new File\(it\) \} \} def "hello world task prints hello world with pre Gradle 2.8"\(\) \{ given: def classpathString = pluginClasspath .collect \{ it.absolutePath.replace\('\\\\', '\\\\\\\\'\) \} // escape backslashes in Windows paths .collect \{ "'\$it'" \} .join\(", "\) buildFile \<\< """ buildscript \{ dependencies \{ classpath files\(\$classpathString\) \} \} apply plugin: "org.gradle.sample.helloworld" """ when: def result = GradleRunner.create\(\) .withProjectDir\(testProjectDir.root\) .withArguments\('helloWorld'\) .withGradleVersion\("2.7"\) .build\(\) then: result.output.contains\('Hello world\!'\) result.task\(":helloWorld"\).outcome == SUCCESS \}

 

1

 List\<File\> pluginClasspath

2

3

 def setup\(\) \{

4

 settingsFile \= testProjectDir.newFile\('settings.gradle'\)

5

 buildFile \= testProjectDir.newFile\('build.gradle'\)

6

7

 def pluginClasspathResource \= getClass\(\).classLoader.findResource\("plugin-classpath.txt"\)

8

 if \(pluginClasspathResource \== null\) \{

9

 throw new IllegalStateException\("Did not find plugin classpath resource, run \`testClasses\` build task."\)

10

 \}

11

12

 pluginClasspath \= pluginClasspathResource.readLines\(\).collect \{ new File\(it\) \}

13

 \}

14

15

 def "hello world task prints hello world with pre Gradle 2.8"\(\) \{

16

 given:

17

 def classpathString \= pluginClasspath

18

 .collect \{ it.absolutePath.replace\('\\\\', '\\\\\\\\'\) \} // escape backslashes in Windows paths

19

 .collect \{ "'\$it'" \}

20

 .join\(", "\)

21

22

 buildFile \<\< """

23

 buildscript \{

24

 dependencies \{

25

 classpath files\(\$classpathString\)

26

 \}

27

 \}

28

 apply plugin: "org.gradle.sample.helloworld"

29

 """

30

31

 when:

32

 def result \= GradleRunner.create\(\)

33

 .withProjectDir\(testProjectDir.root\)

34

 .withArguments\('helloWorld'\)

35

 .withGradleVersion\("2.7"\)

36

 .build\(\)

37

38

 then:

39

 result.output.contains\('Hello world\!'\)

40

 result.task\(":helloWorld"\).outcome \== SUCCESS

41

 \}

## [](#sec:controlling_the_build_environment)[控制构建环境](#sec:controlling_the_build_environment)

运行程序通过在JVM的temp目录（即`java.io.tmpdir`，通常由system属性指定的位置）内的目录中指定专用的“工作目录”，在隔离的环境中执行测试构建`/tmp`。默认的Gradle用户主目录（例如`~/.gradle/gradle.properties`）中的任何配置都不会用于测试执行。TestKit并未公开对环境的各个方面进行细粒度控制的机制（例如JDK）。未来版本的TestKit将提供改进的配置选项。

TestKit使用专用的守护程序进程，这些进程在测试执行后会自动关闭。

## [](#sub:gradle-runner-gradle-version)[用于测试的Gradle版本](#sub:gradle-runner-gradle-version)

Gradle运行器需要Gradle发行版才能执行构建。TestKit并不依赖于Gradle的所有实现。

默认情况下，运行程序将尝试根据从何处`GradleRunner`加载类来查找Gradle发行版。也就是说，期望该类是从Gradle发行版加载的，就像使用`gradleTestKit()`依赖声明时一样。

当将跑步者用作_Gradle执行_的测试的一部分（例如执行`test`插件项目的任务）时，跑步者将使用与执行测试相同的发行版。当将运行程序用作_IDE执行_的测试的一部分时，将使用与导入项目时相同的Gradle发行版。这意味着该插件将使用与其构建时相同的Gradle版本进行有效测试。

另外，可以通过以下任何一种`GradleRunner`方法指定要使用的Gradle的不同版本和特定版本：

* [GradleRunner.withGradleVersion（java.lang.String）]()
* [GradleRunner.withGradleInstallation（java.io.File）]()
* [GradleRunner.withGradleDistribution（java.net.URI）]()

它可以潜在地用于跨Gradle版本测试构建逻辑。以下内容演示了编写为Groovy Spock测试的跨版本兼容性测试：

### [](#example_specifying_a_gradle_version_for_test_execution)[示例：指定用于测试执行的Gradle版本](#example_specifying_a_gradle_version_for_test_execution)

BuildLogicFunctionalTest.groovy

import org.gradle.testkit.runner.GradleRunner import static org.gradle.testkit.runner.TaskOutcome.\* import org.junit.Rule import org.junit.rules.TemporaryFolder import spock.lang.Specification import spock.lang.Unroll class BuildLogicFunctionalTest extends Specification \{ \@Rule final TemporaryFolder testProjectDir = new TemporaryFolder\(\) File settingsFile File buildFile def setup\(\) \{ settingsFile = testProjectDir.newFile\('settings.gradle'\) buildFile = testProjectDir.newFile\('build.gradle'\) \} \@Unroll def "can execute hello world task with Gradle version #gradleVersion"\(\) \{ given: buildFile \<\< """ task helloWorld \{ doLast \{ logger.quiet 'Hello world\!' \} \} """ when: def result = GradleRunner.create\(\) .withGradleVersion\(gradleVersion\) .withProjectDir\(testProjectDir.root\) .withArguments\('helloWorld'\) .build\(\) then: result.output.contains\('Hello world\!'\) result.task\(":helloWorld"\).outcome == SUCCESS where: gradleVersion \<\< \['2.6', '2.7'\] \} \}

 

1

import org.gradle.testkit.runner.GradleRunner

2

import static org.gradle.testkit.runner.TaskOutcome.\*

3

import org.junit.Rule

4

import org.junit.rules.TemporaryFolder

5

import spock.lang.Specification

6

import spock.lang.Unroll

7

8

class BuildLogicFunctionalTest extends Specification \{

9

 \@Rule final TemporaryFolder testProjectDir \= new TemporaryFolder\(\)

10

 File settingsFile

11

 File buildFile

12

13

 def setup\(\) \{

14

 settingsFile \= testProjectDir.newFile\('settings.gradle'\)

15

 buildFile \= testProjectDir.newFile\('build.gradle'\)

16

 \}

17

18

 \@Unroll

19

 def "can execute hello world task with Gradle version #gradleVersion"\(\) \{

20

 given:

21

 buildFile \<\< """

22

 task helloWorld \{

23

 doLast \{

24

 logger.quiet 'Hello world\!'

25

 \}

26

 \}

27

 """

28

29

 when:

30

 def result \= GradleRunner.create\(\)

31

 .withGradleVersion\(gradleVersion\)

32

 .withProjectDir\(testProjectDir.root\)

33

 .withArguments\('helloWorld'\)

34

 .build\(\)

35

36

 then:

37

 result.output.contains\('Hello world\!'\)

38

 result.task\(":helloWorld"\).outcome \== SUCCESS

39

40

 where:

41

 gradleVersion \<\< \['2.6', '2.7'\]

42

 \}

43

\}

### [](#sub:test-kit-compatibility)[使用不同的Gradle版本进行测试时的功能支持](#sub:test-kit-compatibility)

可以使用GradleRunner在Gradle 1.0及更高版本中执行构建。但是，早期版本不支持某些运行器功能。在这种情况下，跑步者在尝试使用功能时会抛出异常。

下表列出了对使用的Gradle版本敏感的功能。

表1. Gradle版本兼容性
| 特征 | 最低版本 | 描述 |
| --- | --- | --- |
| 
检查执行的任务

 | 

2.5

 | 

使用[BuildResult.getTasks（）]()和类似方法检查执行的任务。

 |
| 

[插件类路径注入](#sub:test-kit-classpath-injection)

 | 

2.8

 | 

通过[GradleRunner.withPluginClasspath（java.lang.Iterable）]()注入测试中的代码。

 |
| 

[在调试模式下检查构建输出](#sub:test-kit-debug)

 | 

2.9

 | 

使用[BuildResult.getOutput（）]()在调试模式下运行时检查构建的文本输出。

 |
| 

[自动插件类路径注入](#sub:test-kit-automatic-classpath-injection)

 | 

2.13

 | 

通过应用Java Gradle插件开发插件，通过[GradleRunner.withPluginClasspath（）]()自动注入被测代码。

 |
| 

设置构建要使用的环境变量。

 | 

3.5

 | 

Gradle Tooling API仅支持在更高版本中设置环境变量。

 |

## [](#sub:test-kit-debug)[调试构建逻辑](#sub:test-kit-debug)

跑步者使用[Tooling API]()执行构建。这意味着构建是在单独的过程中执行的（即，执行测试的过程不是同一过程）。因此，以调试方式执行_测试_不允许您调试调试逻辑。在IDE中设置的任何断点都不会因测试版本执行的代码而跳闸。

TestKit提供了两种不同的方式来启用调试模式：

* 设置“`org.gradle.testkit.debug`系统属性”来`true`为JVM_使用_的`GradleRunner`（与所述流道执行即不生成）;
* 调用[GradleRunner.withDebug（boolean）]()方法。

当需要启用调试支持而不对流道配置进行临时更改时，可以使用系统属性方法。大多数IDE提供了设置JVM系统属性以执行测试的功能，并且可以使用此功能来设置此系统属性。

## [](#sub:test-kit-build-cache)[使用构建缓存进行测试](#sub:test-kit-build-cache)

要在测试中启用[构建缓存]()，可以将`--build-cache`参数传递给[GradleRunner]()或使用[启用构建缓存中]()描述的其他方法之一。然后，可以在缓存插件的自定义任务时检查任务结果[TaskOutcome.FROM\_CACHE]()。此结果仅对Gradle 3.5及更高版本有效。

### [](#example_testing_cacheable_tasks)[示例：测试可缓存任务](#example_testing_cacheable_tasks)

BuildLogicFunctionalTest.groovy

def "cacheableTask is loaded from cache"\(\) \{ given: buildFile \<\< """ plugins \{ id 'org.gradle.sample.helloworld' \} """ when: def result = runner\(\) .withArguments\( '--build-cache', 'cacheableTask'\) .build\(\) then: result.task\(":cacheableTask"\).outcome == SUCCESS when: new File\(testProjectDir.root, 'build'\).deleteDir\(\) result = runner\(\) .withArguments\( '--build-cache', 'cacheableTask'\) .build\(\) then: result.task\(":cacheableTask"\).outcome == FROM\_CACHE \}

 

1

def "cacheableTask is loaded from cache"\(\) \{

2

 given:

3

 buildFile \<\< """

4

 plugins \{

5

 id 'org.gradle.sample.helloworld'

6

 \}

7

 """

8

9

 when:

10

 def result \= runner\(\)

11

 .withArguments\( '--build-cache', 'cacheableTask'\)

12

 .build\(\)

13

14

 then:

15

 result.task\(":cacheableTask"\).outcome \== SUCCESS

16

17

 when:

18

 new File\(testProjectDir.root, 'build'\).deleteDir\(\)

19

 result \= runner\(\)

20

 .withArguments\( '--build-cache', 'cacheableTask'\)

21

 .build\(\)

22

23

 then:

24

 result.task\(":cacheableTask"\).outcome \== FROM\_CACHE

25

\}

请注意，TestKit在测试之间重用了Gradle用户主目录（请参阅[GradleRunner.withTestKitDir（java.io.File）]()），其中包含本地构建缓存的默认位置。为了使用构建缓存进行测试，应该在测试之间清理构建缓存目录。完成此操作的最简单方法是将本地构建缓存配置为使用临时目录。

### [](#example_clean_build_cache_between_tests)[示例：在测试之间清理构建缓存](#example_clean_build_cache_between_tests)

BuildLogicFunctionalTest.groovy

\@Rule final TemporaryFolder testProjectDir = new TemporaryFolder\(\) File buildFile File localBuildCacheDirectory def setup\(\) \{ localBuildCacheDirectory = testProjectDir.newFolder\('local-cache'\) testProjectDir.newFile\('settings.gradle'\) \<\< """ buildCache \{ local \{ directory '\$\{localBuildCacheDirectory.toURI\(\)\}' \} \} """ buildFile = testProjectDir.newFile\('build.gradle'\) \}

 

1

\@Rule final TemporaryFolder testProjectDir \= new TemporaryFolder\(\)

2

File buildFile

3

File localBuildCacheDirectory

4

5

def setup\(\) \{

6

 localBuildCacheDirectory \= testProjectDir.newFolder\('local-cache'\)

7

 testProjectDir.newFile\('settings.gradle'\) \<\< """

8

 buildCache \{

9

 local \{

10

 directory '\$\{localBuildCacheDirectory.toURI\(\)\}'

11

 \}

12

 \}

13

 """

14

 buildFile \= testProjectDir.newFile\('build.gradle'\)

15

\}word-wrap: break-word;text-decoration-skip-ink: none;}.wiz-editor-body ul,.wiz-editor-body ol {padding-left:32px;padding-left:2rem;}.wiz-editor-body ol.wiz-list-level1 > li {list-style-type:decimal;}.wiz-editor-body ol.wiz-list-level2 > li {list-style-type:lower-latin;}.wiz-editor-body ol.wiz-list-level3 > li {list-style-type:lower-roman;}.wiz-editor-body li.wiz-list-align-style {list-style-position: inside; margin-left: -1em;}.wiz-editor-body blockquote {padding: 0 12px;}.wiz-editor-body blockquote > :first-child {margin-top:0;}.wiz-editor-body blockquote > :last-child {margin-bottom:0;}.wiz-editor-body img {border:0;max-width:100%;height:auto !important;margin:2px 0;padding: 2px;vertical-align:bottom;}.wiz-editor-body table {border-collapse:collapse;border:1px solid #a7afbc;}.wiz-editor-body td,.wiz-editor-body th {padding:4px 8px;border-collapse:collapse;border:1px solid #a7afbc;min-height:28px;word-break:break-word;box-sizing: border-box;}.wiz-editor-body td > div:first-child {margin-top:0;}.wiz-editor-body td > div:last-child {margin-bottom:0;}.wiz-editor-body img.wiz-svg-image {box-shadow:1px 1px 4px #E8E8E8;}.wiz-editor-body .wiz-image-container {margin:0;max-width: 100%;display: inline-flex;flex-direction: column;}.wiz-editor-body .wiz-image-container .wiz-image-title {display:inline-block;text-align: center;color: #a7afbc;line-height: 18px;font-size: 12px;min-height: 18px;width: 100%;white-space: normal;}.wiz-hide {display:none !important;}.wiz-editor-body.wiz-editor-outline {padding-right:0; padding-left:0;}.wiz-editor-body.wiz-editor-outline .outline-container {margin:0; padding:0; line-height:1.5;}.wiz-editor-body.wiz-editor-outline .outline-container div {margin:0;}.wiz-editor-body.wiz-editor-outline .node {margin:0; padding: 0;}.wiz-editor-body.wiz-editor-outline .outline-container > .node {margin-right:24px; margin-left:30px;}.wiz-editor-body.wiz-editor-outline .node.collapsed .children {display:none;}.wiz-editor-body.wiz-editor-outline .node .row {position:relative; padding-left:26px;}.wiz-editor-body.wiz-editor-outline .node .operator-container {width:36px;position:absolute; top:4px; left:-18px;}.wiz-editor-body.wiz-editor-outline .node .operator-bar {position:absolute; top:0; left:0; right:0; bottom:0; display:flex; align-items:center; justify-content:center;}.wiz-editor-body.wiz-editor-outline .node .switch {width:18px; height:18px;display:flex;flex-direction: column;align-items: center;overflow: hidden;}.wiz-editor-body.wiz-editor-outline .node .switch i {font-size:20px;position:relative;left:-1px;top:-1px;}.wiz-editor-body.wiz-editor-outline .node .switch.active {cursor:pointer;color:transparent; transition:transform 200ms ease 0s;}.wiz-editor-body.wiz-editor-outline .node.collapsed .switch.active {transform:rotateZ(-90deg);}.wiz-editor-body.wiz-editor-outline .node .row:hover .switch.active {color:#505F79}.wiz-editor-body.wiz-editor-outline .node .dot {display:flex; align-items:center; justify-content:center; border-radius:100%; width:18px; height:18px;}.wiz-editor-body.wiz-editor-outline .node.collapsed .dot {background-color:rgba(80, 95, 121, .15);}.wiz-editor-body.wiz-editor-outline .node .dot-icon {background-color:#505F79; border-radius:100%; width:6px; height:6px;}.wiz-editor-body.wiz-editor-outline .node .child {margin-left:8px; border-left:1px solid #E6E9ED; padding-left:17px;}.wiz-editor-body.wiz-editor-outline .node .content {flex:1;outline:none; padding:4px 0;}.wiz-editor-body.wiz-editor-outline .node div.content {font-size:1rem;}.wiz-editor-body.wiz-editor-outline .node.complete > .row .content {text-decoration:line-through;color:#A7AFBC;}.wiz-editor-body.wiz-editor-outline .node .notes {outline:none; font-size:.8rem; color:#A7AFBC;}.wiz-editor-body.wiz-editor-outline .node .image {outline:none; padding-top:4px; padding-bottom:4px;}.wiz-editor-body.wiz-editor-outline .outline-container h1,.wiz-editor-body.wiz-editor-outline .outline-container h2,.wiz-editor-body.wiz-editor-outline .outline-container h3,.wiz-editor-body.wiz-editor-outline .outline-container h4,.wiz-editor-body.wiz-editor-outline .outline-container h5,.wiz-editor-body.wiz-editor-outline .outline-container h6 {margin:0;}body, .wiz-editor-body {  padding-left: 48px;  padding-right: 48px;}</style><style id="wiz_code_style">.wiz-editor-body .wiz-code-container{position: relative; padding:8px 0; margin: 5px 0;text-indent:0; text-align:left;}.CodeMirror {font-family: Consolas, "Liberation Mono", Menlo, Courier, monospace; color: black; font-size: 10.5pt; font-size: 0.875rem}.wiz-editor-body .wiz-code-container .CodeMirror div {margin-top: 0; margin-bottom: 0;}.CodeMirror-lines {padding: 4px 0;}.CodeMirror pre.CodeMirror-line,.CodeMirror pre.CodeMirror-line-like {padding: 0 4px;}.CodeMirror pre.CodeMirror-line {min-height: 24px;}.CodeMirror-scrollbar-filler, .CodeMirror-gutter-filler {background-color: white;}.CodeMirror-gutters {border-right: 1px solid #ddd; background-color: #f7f7f7; white-space: nowrap;}.CodeMirror-linenumbers {}.CodeMirror-linenumber {padding: 0 3px 0 5px; min-width: 20px; text-align: right; color: #999; white-space: nowrap;}.CodeMirror-guttermarker {color: black;}.CodeMirror-guttermarker-subtle {color: #999;}.CodeMirror-cursor {border-left: 1px solid black; border-right: none; width: 0;}.CodeMirror div.CodeMirror-secondarycursor {border-left: 1px solid silver;}.cm-fat-cursor .CodeMirror-cursor {width: auto; border: 0 !important; background: #7e7;}.cm-fat-cursor div.CodeMirror-cursors {z-index: 1;}.cm-fat-cursor-mark {background-color: rgba(20, 255, 20, 0.5);-webkit-animation: blink 1.06s steps(1) infinite;-moz-animation: blink 1.06s steps(1) infinite;animation: blink 1.06s steps(1) infinite;}.cm-animate-fat-cursor {width: auto; border: 0; -webkit-animation: blink 1.06s steps(1) infinite; -moz-animation: blink 1.06s steps(1) infinite; animation: blink 1.06s steps(1) infinite; background-color: #7e7;}@-moz-keyframes blink {  0% {}  50% { background-color: transparent; }  100% {}}@-webkit-keyframes blink {  0% {}  50% { background-color: transparent; }  100% {}}@keyframes blink {  0% {}  50% { background-color: transparent; }  100% {}}.CodeMirror-overwrite .CodeMirror-cursor {}.cm-tab { display: inline-block; text-decoration: inherit; }.CodeMirror-rulers {position: absolute; left: 0; right: 0; top: -50px; bottom: -20px; overflow: hidden;}.CodeMirror-ruler {border-left: 1px solid #ccc; top: 0; bottom: 0; position: absolute;}.cm-s-default .cm-header {color: blue;}.cm-s-default .cm-quote {color: #090;}.cm-negative {color: #d44;}.cm-positive {color: #292;}.cm-header, .cm-strong {font-weight: bold;}.cm-em {font-style: italic;}.cm-link {text-decoration: underline;}.cm-strikethrough {text-decoration: line-through;}.cm-s-default .cm-keyword {color: #708;}.cm-s-default .cm-atom {color: #219;}.cm-s-default .cm-number {color: #164;}.cm-s-default .cm-def {color: #00f;}.cm-s-default .cm-variable,.cm-s-default .cm-punctuation,.cm-s-default .cm-property,.cm-s-default .cm-operator {}.cm-s-default .cm-variable-2 {color: #05a;}.cm-s-default .cm-variable-3 {color: #085;}.cm-s-default .cm-comment {color: #a50;}.cm-s-default .cm-string {color: #a11;}.cm-s-default .cm-string-2 {color: #f50;}.cm-s-default .cm-meta {color: #555;}.cm-s-default .cm-qualifier {color: #555;}.cm-s-default .cm-builtin {color: #30a;}.cm-s-default .cm-bracket {color: #997;}.cm-s-default .cm-tag {color: #170;}.cm-s-default .cm-attribute {color: #00c;}.cm-s-default .cm-hr {color: #999;}.cm-s-default .cm-link {color: #00c;}.cm-s-default .cm-error {color: #f00;}.cm-invalidchar {color: #f00;}.CodeMirror-composing { border-bottom: 2px solid; }div.CodeMirror span.CodeMirror-matchingbracket {color: #0b0;}div.CodeMirror span.CodeMirror-nonmatchingbracket {color: #a22;}.CodeMirror-matchingtag { background: rgba(255, 150, 0, .3); }.CodeMirror-activeline-background {background: #e8f2ff;}.CodeMirror {position: relative; background: #f5f5f5;}.CodeMirror-scroll {overflow: hidden !important; margin-bottom: 0; margin-right: -30px; padding: 16px 30px 16px 0; outline: none; position: relative;}.CodeMirror-sizer {position: relative; border-right: 30px solid transparent;}.CodeMirror-vscrollbar, .CodeMirror-hscrollbar, .CodeMirror-scrollbar-filler, .CodeMirror-gutter-filler {position: absolute; z-index: 6; display: none;}.CodeMirror-vscrollbar {right: 0; top: 0; overflow-x: hidden; overflow-y: scroll;}.CodeMirror-hscrollbar {bottom: 0; left: 0 !important; overflow-y: hidden; overflow-x: scroll;pointer-events: auto !important;outline: none;}.CodeMirror-scrollbar-filler {right: 0; bottom: 0;}.CodeMirror-gutter-filler {left: 0; bottom: 0;}.CodeMirror-gutters {position: absolute; left: 0; top: 0; min-height: 100%; z-index: 3;}.CodeMirror-gutter {white-space: normal; height: 100%; display: inline-block; vertical-align: top; margin-bottom: -30px;}.CodeMirror-gutter-wrapper {position: absolute; z-index: 4; background: none !important; border: none !important;}.CodeMirror-gutter-background {position: absolute; top: 0; bottom: 0; z-index: 4;}.CodeMirror-gutter-elt {position: absolute; cursor: default; z-index: 4;}.CodeMirror-gutter-wrapper ::selection { background-color: transparent }.CodeMirror-gutter-wrapper ::-moz-selection { background-color: transparent }.CodeMirror-lines {cursor: text; min-height: 1px;}.CodeMirror pre.CodeMirror-line,.CodeMirror pre.CodeMirror-line-like {-moz-border-radius: 0; -webkit-border-radius: 0; border-radius: 0; border-width: 0; background: transparent; font-family: inherit; font-size: inherit; margin: 0; white-space: pre; word-wrap: normal; line-height: inherit; color: inherit; z-index: 2; position: relative; overflow: visible; -webkit-tap-highlight-color: transparent; -webkit-font-variant-ligatures: contextual; font-variant-ligatures: contextual;}.CodeMirror-wrap pre.CodeMirror-line,.CodeMirror-wrap pre.CodeMirror-line-like {word-wrap: break-word; white-space: pre-wrap; word-break: normal;}.CodeMirror-linebackground {position: absolute; left: 0; right: 0; top: 0; bottom: 0; z-index: 0;}.CodeMirror-linewidget {position: relative; z-index: 2; padding: 0.1px;}.CodeMirror-widget {}.CodeMirror-rtl pre { direction: rtl; }.CodeMirror-code {outline: none;}.CodeMirror-scroll,.CodeMirror-sizer,.CodeMirror-gutter,.CodeMirror-gutters,.CodeMirror-linenumber {-moz-box-sizing: content-box; box-sizing: content-box;}.CodeMirror-measure {position: absolute; width: 100%; height: 0; overflow: hidden; visibility: hidden;}.CodeMirror-cursor {position: absolute; pointer-events: none;}.CodeMirror-measure pre { position: static; }div.CodeMirror-cursors {visibility: hidden; position: relative; z-index: 3;}div.CodeMirror-dragcursors {visibility: visible;}.CodeMirror-focused div.CodeMirror-cursors {visibility: visible;}.CodeMirror-selected { background: #d9d9d9; }.CodeMirror-focused .CodeMirror-selected { background: #d7d4f0; }.CodeMirror-crosshair { cursor: crosshair; }.CodeMirror-line::selection, .CodeMirror-line > span::selection, .CodeMirror-line > span > span::selection { background: #d7d4f0; }.CodeMirror-line::-moz-selection, .CodeMirror-line > span::-moz-selection, .CodeMirror-line > span > span::-moz-selection { background: #d7d4f0; }.cm-searching {background: #ffa; background: rgba(255, 255, 0, .4);}.cm-force-border { padding-right: .1px; }@media print {  .CodeMirror div.CodeMirror-cursors {visibility: hidden;}}.cm-tab-wrap-hack:after { content: ""; }span.CodeMirror-selectedtext { background: none; }.CodeMirror-activeline-background, .CodeMirror-selected {transition: visibility 0ms 100ms;}.CodeMirror-blur .CodeMirror-activeline-background, .CodeMirror-blur .CodeMirror-selected {visibility:hidden;}.CodeMirror-blur .CodeMirror-matchingbracket {color:inherit !important;outline:none !important;text-decoration:none !important;}.CodeMirror-sizer {min-height:auto !important;}</style></head>

<body class="wiz-editor-body" data-wiz-document-type="common" spellcheck="false"><div><div id="header" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><h1 style="font-size:2rem;color:rgba(0, 0, 0, 0.85);"><font><font>使用TestKit测试构建逻辑</font></font></h1><div id="toc" class="toc" style="background: rgb(247, 247, 248);"><div id="toctitle"><font><font>内容</font></font></div><ul class="sectlevel1"><li><a href="#sec:testkit_usage" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>用法</font></font></a></li><li><a href="#sec:functional_testing_with_the_gradle_runner" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>使用Gradle Runner进行功能测试</font></font></a></li><li><a href="#sub:test-kit-classpath-injection" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>将被测插件放入测试版本</font></font></a></li><li><a href="#sec:controlling_the_build_environment" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>控制构建环境</font></font></a></li><li><a href="#sub:gradle-runner-gradle-version" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>用于测试的Gradle版本</font></font></a></li><li><a href="#sub:test-kit-debug" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>调试构建逻辑</font></font></a></li><li><a href="#sub:test-kit-build-cache" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>使用构建缓存进行测试</font></font></a></li></ul></div></div><div id="preamble" style="color:rgb(2, 48, 58);font-family:Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif"><div class="sectionbody"><div class="paragraph"><p style="font-size:1.0625rem;"><font><font>Gradle TestKit（又名TestKit）是一个有助于测试Gradle插件和一般构建逻辑的库。</font><font>目前，它专注于</font></font><em style="font-style:italic;"><font><font>功能</font></font></em><font><font>测试。</font><font>也就是说，通过将其作为程序执行的构建的一部分进行测试来测试构建逻辑。</font><font>随着时间的流逝，TestKit可能会扩展以方便其他类型的测试。</font></font></p></div></div></div><div class="sect1" style="color:rgb(2, 48, 58);font-family:Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif"><h2 id="sec:testkit_usage" style="font-size:1.5rem;"><a class="anchor" href="#sec:testkit_usage" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sec:testkit_usage" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>用法</font></font></a></h2><div class="sectionbody"><div class="paragraph"><p><font><font>要使用TestKit，请在插件的版本中包含以下内容：</font></font></p></div><div class="exampleblock"><div class="title" style="font-style: italic;"><font><font>示例1.声明TestKit依赖项</font></font></div><div class="content"><div class="multi-language-selector"><code data-lang="groovy" class="language-option selected" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:20px center;background-repeat:no-repeat;background-size:20px 12px;&quot;);">Groovy</code><code data-lang="kotlin" class="language-option" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; color: rgba(0, 0, 0, 0.9); background-image: url(&quot;data:image/svg+xml;background-position:30px center;background-repeat:no-repeat;background-size:11px 11px;&quot;);">Kotlin</code></div><div class="exampleblock testable-sample multi-language-sample" data-lang="groovy"><div class="content"><div class="listingblock"><div class="title" style="font-family: Inconsolata, monospace; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:16px 80%;background-repeat:no-repeat;background-size:20px 12px;&quot;);"><font><font>build.gradle</font></font></div></div></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945190244_4339" class="wiz-code-container"><textarea style="display:none;">dependencies {
    testImplementation gradleTestKit()
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945190244_4339"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 80px; min-width: 299.475px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">dependencies</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">testImplementation</span> <span class="cm-variable">gradleTestKit</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 80px;"></div><div class="CodeMirror-gutters" style="height: 110px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="paragraph"><p><font><font>其中</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">gradleTestKit()</code><font><font>包含TestKit的类以及</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>Gradle Tooling API客户端</font></font></a><font><font>。</font><font>它不包括</font></font><a href="http://junit.org" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>JUnit</font></font></a><font><font>，</font></font><a href="http://testng.org" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>TestNG</font></font></a><font><font>或任何其他测试执行框架的版本。</font><font>必须明确声明这种依赖性。</font></font></p></div><div class="exampleblock"><div class="title" style="font-style: italic;"><font><font>示例2.声明JUnit依赖项</font></font></div><div class="content"><div class="multi-language-selector"><code data-lang="groovy" class="language-option selected" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:20px center;background-repeat:no-repeat;background-size:20px 12px;&quot;);">Groovy</code><code data-lang="kotlin" class="language-option" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; color: rgba(0, 0, 0, 0.9); background-image: url(&quot;data:image/svg+xml;background-position:30px center;background-repeat:no-repeat;background-size:11px 11px;&quot;);">Kotlin</code></div><div class="exampleblock testable-sample multi-language-sample" data-lang="groovy"><div class="content"><div class="listingblock"><div class="title" style="font-family: Inconsolata, monospace; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:16px 80%;background-repeat:no-repeat;background-size:20px 12px;&quot;);"><font><font>build.gradle</font></font></div></div></div></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945190229_1644" class="wiz-code-container"><textarea style="display:none;">dependencies {
    testImplementation 'junit:junit:4.13'
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945190229_1644"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 80px; min-width: 322.2px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">dependencies</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">testImplementation</span> <span class="cm-string">'junit:junit:4.13'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 80px;"></div><div class="CodeMirror-gutters" style="height: 110px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><h2 id="sec:functional_testing_with_the_gradle_runner" style="font-size:1.5rem;"><a class="anchor" href="#sec:functional_testing_with_the_gradle_runner" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sec:functional_testing_with_the_gradle_runner" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>使用Gradle Runner进行功能测试</font></font></a></h2><div class="sectionbody"><div class="paragraph"><p><font><font>所述</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner</font></font></a><font><font>便于编程执行摇篮构建，和检查结果。</font></font></p></div><div class="paragraph"><p><font><font>可以（例如以编程方式或从模板中）创建人为构建的练习“被测逻辑”。</font><font>然后可以潜在地以各种方式（例如，任务和参数的不同组合）执行构建。</font><font>然后可以通过断言以下内容（可能组合使用）来验证逻辑的正确性：</font></font></p></div><div class="ulist"><ul><li><span><font><font>构建的输出；</font></font></span></li><li><span><font><font>构建的日志（即控制台输出）；</font></font></span></li><li><span><font><font>由构建执行的任务集及其结果（例如FAILED，UP-TO-DATE等）。</font></font></span></li></ul></div><div class="paragraph"><p><font><font>在创建并配置了</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>Runner</font></font></a><font><font>实例之后，可以</font><font>根据预期结果</font><font>通过</font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font>GradleRunner.build（）</font></a><font>或</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.buildAndFail（）</font></font></a><font><font>方法</font><font>执行构建</font><font>。</font></font></p></div><div class="paragraph"><p><font><font>以下内容演示了Java JUnit测试中Gradle Runner的用法：</font></font></p></div><div class="sect2"><h3 id="example_using_gradlerunner_with_java_and_junit" style="font-size:1.125rem;"><a class="anchor" href="#example_using_gradlerunner_with_java_and_junit" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#example_using_gradlerunner_with_java_and_junit" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>示例：将GradleRunner与Java和JUnit一起使用</font></font></a></h3><div class="listingblock"><div class="title"><font><font>BuildLogicFunctionalTest.java</font></font></div></div></div></div></div></div></div><div data-mode="Java" data-theme="default" id="wiz_cm_1605945190169_8570" class="wiz-code-container"><textarea style="display:none;">import org.gradle.testkit.runner.BuildResult;
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
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945190169_8570"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 1448px; min-width: 630.2px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">org</span>.<span class="cm-variable">gradle</span>.<span class="cm-variable">testkit</span>.<span class="cm-variable">runner</span>.<span class="cm-variable">BuildResult</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">org</span>.<span class="cm-variable">gradle</span>.<span class="cm-variable">testkit</span>.<span class="cm-variable">runner</span>.<span class="cm-variable">GradleRunner</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">org</span>.<span class="cm-variable">junit</span>.<span class="cm-variable">Before</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">org</span>.<span class="cm-variable">junit</span>.<span class="cm-variable">Rule</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">org</span>.<span class="cm-variable">junit</span>.<span class="cm-variable">Test</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">org</span>.<span class="cm-variable">junit</span>.<span class="cm-variable">rules</span>.<span class="cm-variable">TemporaryFolder</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">java</span>.<span class="cm-variable">io</span>.<span class="cm-variable">BufferedWriter</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">java</span>.<span class="cm-variable">io</span>.<span class="cm-variable">File</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">java</span>.<span class="cm-variable">io</span>.<span class="cm-variable">FileWriter</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">java</span>.<span class="cm-variable">io</span>.<span class="cm-variable">IOException</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">java</span>.<span class="cm-variable">util</span>.<span class="cm-variable">Collections</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-keyword">static</span> <span class="cm-variable">org</span>.<span class="cm-variable">junit</span>.<span class="cm-variable">Assert</span>.<span class="cm-variable">assertEquals</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-keyword">static</span> <span class="cm-variable">org</span>.<span class="cm-variable">junit</span>.<span class="cm-variable">Assert</span>.<span class="cm-variable">assertTrue</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-keyword">static</span> <span class="cm-variable">org</span>.<span class="cm-variable">gradle</span>.<span class="cm-variable">testkit</span>.<span class="cm-variable">runner</span>.<span class="cm-variable">TaskOutcome</span>.<span class="cm-operator">*</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">public</span> <span class="cm-keyword">class</span> <span class="cm-def">BuildLogicFunctionalTest</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-meta">@Rule</span> <span class="cm-keyword">public</span> <span class="cm-keyword">final</span> <span class="cm-variable">TemporaryFolder</span> <span class="cm-variable">testProjectDir</span> <span class="cm-operator">=</span> <span class="cm-keyword">new</span> <span class="cm-variable">TemporaryFolder</span>();</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">21</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-keyword">private</span> <span class="cm-variable">File</span> <span class="cm-variable">settingsFile</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">22</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-keyword">private</span> <span class="cm-variable">File</span> <span class="cm-variable">buildFile</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">23</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">24</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-meta">@Before</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">25</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-keyword">public</span> <span class="cm-type">void</span> <span class="cm-variable">setup</span>() <span class="cm-keyword">throws</span> <span class="cm-variable">IOException</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">26</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">settingsFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-variable">newFile</span>(<span class="cm-string">"settings.gradle"</span>);</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">27</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-variable">newFile</span>(<span class="cm-string">"build.gradle"</span>);</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">28</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">29</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">30</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-meta">@Test</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">31</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-keyword">public</span> <span class="cm-type">void</span> <span class="cm-variable">testHelloWorldTask</span>() <span class="cm-keyword">throws</span> <span class="cm-variable">IOException</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">32</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">writeFile</span>(<span class="cm-variable">settingsFile</span>, <span class="cm-string">"rootProject.name = 'hello-world'"</span>);</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">33</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-type">String</span> <span class="cm-variable">buildFileContent</span> <span class="cm-operator">=</span> <span class="cm-string">"task helloWorld {"</span> <span class="cm-operator">+</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">34</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                                  <span class="cm-string">"    doLast {"</span> <span class="cm-operator">+</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">35</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                                  <span class="cm-string">"        println 'Hello world!'"</span> <span class="cm-operator">+</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">36</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                                  <span class="cm-string">"    }"</span> <span class="cm-operator">+</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">37</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                                  <span class="cm-string">"}"</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">38</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">writeFile</span>(<span class="cm-variable">buildFile</span>, <span class="cm-variable">buildFileContent</span>);</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">39</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">40</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">BuildResult</span> <span class="cm-variable">result</span> <span class="cm-operator">=</span> <span class="cm-variable">GradleRunner</span>.<span class="cm-variable">create</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">41</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-variable">withProjectDir</span>(<span class="cm-variable">testProjectDir</span>.<span class="cm-variable">getRoot</span>())</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">42</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-variable">withArguments</span>(<span class="cm-string">"helloWorld"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">43</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-variable">build</span>();</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">44</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">45</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">assertTrue</span>(<span class="cm-variable">result</span>.<span class="cm-variable">getOutput</span>().<span class="cm-variable">contains</span>(<span class="cm-string">"Hello world!"</span>));</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">46</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">assertEquals</span>(<span class="cm-variable">SUCCESS</span>, <span class="cm-variable">result</span>.<span class="cm-variable">task</span>(<span class="cm-string">":helloWorld"</span>).<span class="cm-variable">getOutcome</span>());</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">47</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">48</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">49</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-keyword">private</span> <span class="cm-type">void</span> <span class="cm-variable">writeFile</span>(<span class="cm-variable">File</span> <span class="cm-variable">destination</span>, <span class="cm-type">String</span> <span class="cm-variable">content</span>) <span class="cm-keyword">throws</span> <span class="cm-variable">IOException</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">50</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">BufferedWriter</span> <span class="cm-variable">output</span> <span class="cm-operator">=</span> <span class="cm-atom">null</span>;</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">51</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-keyword">try</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">52</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">output</span> <span class="cm-operator">=</span> <span class="cm-keyword">new</span> <span class="cm-variable">BufferedWriter</span>(<span class="cm-keyword">new</span> <span class="cm-variable">FileWriter</span>(<span class="cm-variable">destination</span>));</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">53</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">output</span>.<span class="cm-variable">write</span>(<span class="cm-variable">content</span>);</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">54</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        } <span class="cm-keyword">finally</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">55</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-keyword">if</span> (<span class="cm-variable">output</span> <span class="cm-operator">!=</span> <span class="cm-atom">null</span>) {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">56</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                <span class="cm-variable">output</span>.<span class="cm-variable">close</span>();</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">57</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">58</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">59</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">60</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 1448px;"></div><div class="CodeMirror-gutters" style="height: 1478px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><div class="paragraph"><p><font><font>以下内容演示了在Kotlin JUnit测试中Gradle运行器的用法：</font></font></p></div></div><div class="sect2"><h3 id="example_using_gradlerunner_with_kotlin_and_junit" style="font-size:1.125rem;"><a class="anchor" href="#example_using_gradlerunner_with_kotlin_and_junit" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#example_using_gradlerunner_with_kotlin_and_junit" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>示例：将GradleRunner与Kotlin和JUnit一起使用</font></font></a></h3><div class="listingblock"><div class="title"><font><font>BuildLogicFunctionalTest.kt</font></font></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945190121_3385" class="wiz-code-container"><textarea style="display:none;">import org.gradle.testkit.runner.BuildResult
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
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945190121_3385"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 1160px; min-width: 607.263px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">gradle</span>.<span class="cm-property">testkit</span>.<span class="cm-property">runner</span>.<span class="cm-property">BuildResult</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">gradle</span>.<span class="cm-property">testkit</span>.<span class="cm-property">runner</span>.<span class="cm-property">GradleRunner</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">gradle</span>.<span class="cm-property">testkit</span>.<span class="cm-property">runner</span>.<span class="cm-property">TaskOutcome</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">junit</span>.<span class="cm-property">Assert</span>.<span class="cm-property">assertEquals</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">junit</span>.<span class="cm-property">Assert</span>.<span class="cm-property">assertTrue</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">junit</span>.<span class="cm-property">Before</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">junit</span>.<span class="cm-property">Rule</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">junit</span>.<span class="cm-property">Test</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">junit</span>.<span class="cm-property">rules</span>.<span class="cm-property">TemporaryFolder</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">kotlin</span>.<span class="cm-variable">jvm</span>.<span class="cm-property">JvmField</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">java</span>.<span class="cm-variable">io</span>.<span class="cm-property">File</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">class</span> <span class="cm-def">BuildLogicFunctionalTest</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-operator">@</span><span class="cm-variable">Rule</span> <span class="cm-operator">@</span><span class="cm-variable">JvmField</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-property">val</span> <span class="cm-def">testProjectDir</span>: <span class="cm-variable">TemporaryFolder</span> <span class="cm-operator">=</span> <span class="cm-variable">TemporaryFolder</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">private</span> <span class="cm-variable">lateinit</span> <span class="cm-keyword">var</span> <span class="cm-def">settingsFile</span>: <span class="cm-variable">File</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">private</span> <span class="cm-variable">lateinit</span> <span class="cm-keyword">var</span> <span class="cm-def">buildFile</span>: <span class="cm-variable">File</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-operator">@</span><span class="cm-variable">Before</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">21</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">fun</span> <span class="cm-variable">setup</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">22</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">settingsFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">"settings.gradle.kts"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">23</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">"build.gradle.kts"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">24</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">25</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">26</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-operator">@</span><span class="cm-variable">Test</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">27</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">fun</span> <span class="cm-string-2">`test helloWorld task`</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">28</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">29</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">settingsFile</span>.<span class="cm-property">writeText</span>(<span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">30</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">rootProject</span>.<span class="cm-variable">name</span> <span class="cm-operator">=</span> <span class="cm-string">"hello-world"</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">31</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-string">""".trimIndent())</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">32</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span>.<span class="cm-variable">writeText</span>(<span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">33</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">tasks</span>.<span class="cm-variable">register</span>(<span class="cm-string">"helloWorld"</span>) {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">34</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                <span class="cm-variable">doLast</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">35</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                    <span class="cm-variable">println</span>(<span class="cm-string">"Hello world!"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">36</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">37</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">38</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-string">""".trimIndent())</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">39</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">40</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">val</span> <span class="cm-variable">result</span> <span class="cm-operator">=</span> <span class="cm-variable">GradleRunner</span>.<span class="cm-property">create</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">41</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withProjectDir</span>(<span class="cm-variable">testProjectDir</span>.<span class="cm-property">root</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">42</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withArguments</span>(<span class="cm-string">"helloWorld"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">43</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">build</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">44</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">45</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">assertTrue</span>(<span class="cm-variable">result</span>.<span class="cm-property">output</span>.<span class="cm-property">contains</span>(<span class="cm-string">"Hello world!"</span>))</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">46</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">assertEquals</span>(<span class="cm-variable">TaskOutcome</span>.<span class="cm-property">SUCCESS</span>, <span class="cm-variable">result</span>.<span class="cm-property">task</span>(<span class="cm-string">":helloWorld"</span>)<span class="cm-operator">?</span>.<span class="cm-variable">outcome</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">47</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">48</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 1160px;"></div><div class="CodeMirror-gutters" style="height: 1190px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><div class="paragraph"><p><font><font>可以使用任何测试执行框架。</font></font></p></div><div class="paragraph"><p><font><font>由于Gradle构建脚本也可以用Groovy编程语言编写，因此用Groovy编写Gradle功能测试通常是一种有效的选择。</font><font>此外，建议使用（基于Groovy的）</font></font><a href="https://code.google.com/p/spock/" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>Spock测试执行框架，</font></font></a><font><font>因为它比JUnit的使用具有许多引人注目的功能。</font></font></p></div><div class="paragraph"><p><font><font>以下内容演示了Groovy Spock测试中Gradle Runner的用法：</font></font></p></div></div><div class="sect2"><h3 id="example_using_gradlerunner_with_groovy_and_spock" style="font-size:1.125rem;"><a class="anchor" href="#example_using_gradlerunner_with_groovy_and_spock" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#example_using_gradlerunner_with_groovy_and_spock" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>示例：将GradleRunner与Groovy和Spock结合使用</font></font></a></h3><div class="listingblock"><div class="title"><font><font>BuildLogicFunctionalTest.groovy</font></font></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945190079_7890" class="wiz-code-container"><textarea style="display:none;">import org.gradle.testkit.runner.GradleRunner
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
        settingsFile &lt;&lt; "rootProject.name = 'hello-world'"
        buildFile &lt;&lt; """
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
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945190079_7890"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 920px; min-width: 499.513px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">gradle</span>.<span class="cm-property">testkit</span>.<span class="cm-property">runner</span>.<span class="cm-property">GradleRunner</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">static</span> <span class="cm-variable">org</span>.<span class="cm-property">gradle</span>.<span class="cm-property">testkit</span>.<span class="cm-property">runner</span>.<span class="cm-property">TaskOutcome</span>.<span class="cm-operator">*</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">org</span>.<span class="cm-property">junit</span>.<span class="cm-property">Rule</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">junit</span>.<span class="cm-property">rules</span>.<span class="cm-property">TemporaryFolder</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">spock</span>.<span class="cm-variable">lang</span>.<span class="cm-property">Specification</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">class</span> <span class="cm-def">BuildLogicFunctionalTest</span> <span class="cm-keyword">extends</span> <span class="cm-variable">Specification</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-operator">@</span><span class="cm-variable">Rule</span> <span class="cm-property">TemporaryFolder</span> <span class="cm-def">testProjectDir</span> <span class="cm-operator">=</span> <span class="cm-keyword">new</span> <span class="cm-variable">TemporaryFolder</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">File</span> <span class="cm-variable">settingsFile</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">File</span> <span class="cm-variable">buildFile</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-variable">setup</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">settingsFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'settings.gradle'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'build.gradle'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-string">"hello world task prints hello world"</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">given</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">settingsFile</span> <span class="cm-operator">&lt;&lt;</span> <span class="cm-string">"rootProject.name = 'hello-world'"</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">&lt;&lt;</span> <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">21</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">task</span> <span class="cm-variable">helloWorld</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">22</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                <span class="cm-variable">doLast</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">23</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                    <span class="cm-variable">println</span> <span class="cm-string">'Hello world!'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">24</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">25</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">26</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">27</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">28</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">when</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">29</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">def</span> <span class="cm-variable">result</span> <span class="cm-operator">=</span> <span class="cm-variable">GradleRunner</span>.<span class="cm-property">create</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">30</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withProjectDir</span>(<span class="cm-variable">testProjectDir</span>.<span class="cm-property">root</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">31</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withArguments</span>(<span class="cm-string">'helloWorld'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">32</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">build</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">33</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">34</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">then</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">35</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">result</span>.<span class="cm-property">output</span>.<span class="cm-property">contains</span>(<span class="cm-string">'Hello world!'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">36</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">result</span>.<span class="cm-property">task</span>(<span class="cm-string">":helloWorld"</span>).<span class="cm-property">outcome</span> <span class="cm-operator">==</span> <span class="cm-variable">SUCCESS</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">37</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">38</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 920px;"></div><div class="CodeMirror-gutters" style="height: 950px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><div class="paragraph"><p><font><font>实施任何自定义构建逻辑（如插件和任务类型）通常是一种惯例，该逻辑本质上比独立项目中的外部类复杂。</font><font>这种方法背后的主要驱动力是将编译后的代码捆绑到一个JAR文件中，将其发布到二进制存储库中，并在各个项目中重复使用。</font></font></p></div></div></div></div><div class="sect1"><h2 id="sub:test-kit-classpath-injection" style="font-size:1.5rem;"><a class="anchor" href="#sub:test-kit-classpath-injection" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sub:test-kit-classpath-injection" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>将被测插件放入测试版本</font></font></a></h2><div class="sectionbody"><div class="paragraph"><p><font><font>GradleRunner使用</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>Tooling API</font></font></a><font><font>执行构建。</font><font>这意味着构建是在单独的过程中执行的（即，执行测试的过程不是同一过程）。</font><font>因此，测试版本与测试过程不会共享相同的类路径或类加载器，并且测试代码也不能隐式地用于测试版本。</font></font></p></div><div class="paragraph"><p><font><font>从2.13版开始，Gradle提供了一种常规机制，可以将被测代码注入测试版本中。</font></font></p></div><div class="sect2"><h3 id="sub:test-kit-automatic-classpath-injection" style="font-size:1.125rem;"><a class="anchor" href="#sub:test-kit-automatic-classpath-injection" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sub:test-kit-automatic-classpath-injection" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>使用Java Gradle插件开发插件自动注入</font></font></a></h3><div class="paragraph"><p><font><font>在</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>Java的摇篮插件开发的插件</font></font></a><font><font>可以用来协助摇篮插件的开发。</font><font>从Gradle 2.13版本开始，该插件提供了与TestKit的直接集成。</font><font>当应用于项目时，该插件会自动将</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">gradleTestKit()</code><font><font>依赖项</font><font>添加</font><font>到测试编译配置中。</font><font>此外，它会自动为被测试的代码生成类路径，并通过</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withPluginClasspath（）</font></font></a><font><font>将其注入</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">GradleRunner</code><font><font>用户创建的</font><font>任何</font><font>实例。</font><font>重要的是要注意，该机制当前</font></font><em style="font-style:italic;"><font><font>仅</font></font></em><font><font>在使用</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>插件DSL</font></font></a><font><font>应用被测插件</font><em style="font-style:italic;"><font>时才</font></em><font>有效</font><font>。</font><font>如果</font></font><a href="#sub:gradle-runner-gradle-version" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>目标Gradle版本</font></font></a><font><font><span>&nbsp;</span>在2.8之前的版本中，不会执行自动插件类路径注入。</font></font></p></div><div class="paragraph"><p><font><font>该插件使用以下约定来应用TestKit依赖项并注入类路径：</font></font></p></div><div class="ulist"><ul><li><span><font><font>包含正在测试的代码的源集：<span>&nbsp;</span></font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">sourceSets.main</code></span></li><li><span><font><font>用于注入插件类路径的源集：<span>&nbsp;</span></font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">sourceSets.test</code></span></li></ul></div><div class="paragraph"><p><font><font>这些约定中的任何一个都可以在</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradlePluginDevelopmentExtension</font></font></a><font><font>类的帮助下进行重新配置</font><font>。</font></font></p></div><div class="paragraph"><p><font><font>以下基于Groovy的示例演示了如何使用Java Gradle插件开发插件应用的标准约定自动注入插件类路径。</font></font></p></div><div class="exampleblock"><div class="title" style="font-style: italic;"><font><font>示例3.使用Java Gradle Development插件生成插件元数据</font></font></div><div class="content"><div class="multi-language-selector"><code data-lang="groovy" class="language-option selected" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:20px center;background-repeat:no-repeat;background-size:20px 12px;&quot;);">Groovy</code><code data-lang="kotlin" class="language-option" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; color: rgba(0, 0, 0, 0.9); background-image: url(&quot;data:image/svg+xml;background-position:30px center;background-repeat:no-repeat;background-size:11px 11px;&quot;);">Kotlin</code></div><div class="exampleblock testable-sample multi-language-sample" data-lang="groovy"><div class="content"><div class="listingblock"><div class="title" style="font-family: Inconsolata, monospace; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:16px 80%;background-repeat:no-repeat;background-size:20px 12px;&quot;);"><font><font>build.gradle</font></font></div></div></div></div></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945190058_6619" class="wiz-code-container"><textarea style="display:none;">plugins {
    id 'groovy'
    id 'java-gradle-plugin'
}

dependencies {
    testImplementation('org.spockframework:spock-core:1.3-groovy-2.4') {
        exclude module: 'groovy-all'
    }
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945190058_6619"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 248px; min-width: 561.175px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">plugins</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">id</span> <span class="cm-string">'groovy'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">id</span> <span class="cm-string">'java-gradle-plugin'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">dependencies</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">testImplementation</span>(<span class="cm-string">'org.spockframework:spock-core:1.3-groovy-2.4'</span>) {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">exclude</span> <span class="cm-variable">module</span>: <span class="cm-string">'groovy-all'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 248px;"></div><div class="CodeMirror-gutters" style="height: 278px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><h3 id="example_automatically_injecting_the_code_under_test_classes_into_test_builds" style="font-size:1.125rem;"><a class="anchor" href="#example_automatically_injecting_the_code_under_test_classes_into_test_builds" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#example_automatically_injecting_the_code_under_test_classes_into_test_builds" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>示例：自动将测试类下的代码注入到测试版本中</font></font></a></h3><div class="listingblock"><div class="title"><font><font>src / test / groovy / org / gradle / sample / BuildLogicFunctionalTest.groovy</font></font></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945190030_3" class="wiz-code-container"><textarea style="display:none;">def "hello world task prints hello world"() {
    given:
    settingsFile &lt;&lt; "rootProject.name = 'hello-world'"
    buildFile &lt;&lt; """
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
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945190030_3"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 488px; min-width: 422.613px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">def</span> <span class="cm-string">"hello world task prints hello world"</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">given</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">settingsFile</span> <span class="cm-operator">&lt;&lt;</span> <span class="cm-string">"rootProject.name = 'hello-world'"</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">buildFile</span> <span class="cm-operator">&lt;&lt;</span> <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">plugins</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">id</span> <span class="cm-string">'org.gradle.sample.helloworld'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">when</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-variable">result</span> <span class="cm-operator">=</span> <span class="cm-variable">GradleRunner</span>.<span class="cm-property">create</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">withProjectDir</span>(<span class="cm-variable">testProjectDir</span>.<span class="cm-property">root</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">withArguments</span>(<span class="cm-string">'helloWorld'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">withPluginClasspath</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">build</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">then</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">result</span>.<span class="cm-property">output</span>.<span class="cm-property">contains</span>(<span class="cm-string">'Hello world!'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">result</span>.<span class="cm-property">task</span>(<span class="cm-string">":helloWorld"</span>).<span class="cm-property">outcome</span> <span class="cm-operator">==</span> <span class="cm-variable">SUCCESS</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 488px;"></div><div class="CodeMirror-gutters" style="height: 518px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><div class="paragraph"><p><font><font>以下构建脚本演示了如何为使用自定义</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">Test</code><font><font>源集</font><font>的项目重新配置Java Gradle插件开发插件提供的约定</font><font>。</font></font></p></div><div class="exampleblock"><div class="title" style="font-style: italic;"><font><font>示例4.重新配置Java Gradle Development插件的类路径生成约定</font></font></div><div class="content"><div class="multi-language-selector"><code data-lang="groovy" class="language-option selected" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:20px center;background-repeat:no-repeat;background-size:20px 12px;&quot;);">Groovy</code><code data-lang="kotlin" class="language-option" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; color: rgba(0, 0, 0, 0.9); background-image: url(&quot;data:image/svg+xml;background-position:30px center;background-repeat:no-repeat;background-size:11px 11px;&quot;);">Kotlin</code></div><div class="exampleblock testable-sample multi-language-sample" data-lang="groovy"><div class="content"><div class="listingblock"><div class="title" style="font-family: Inconsolata, monospace; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:16px 80%;background-repeat:no-repeat;background-size:20px 12px;&quot;);"><font><font>build.gradle</font></font></div></div></div></div></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945189995_1945" class="wiz-code-container"><textarea style="display:none;">plugins {
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
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945189995_1945"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 824px; min-width: 684.225px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">plugins</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">id</span> <span class="cm-string">'groovy'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">id</span> <span class="cm-string">'java-gradle-plugin'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">sourceSets</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">functionalTest</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">groovy</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">srcDir</span> <span class="cm-variable">file</span>(<span class="cm-string">'src/functionalTest/groovy'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">resources</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">srcDir</span> <span class="cm-variable">file</span>(<span class="cm-string">'src/functionalTest/resources'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">compileClasspath</span> <span class="cm-operator">+=</span> <span class="cm-variable">sourceSets</span>.<span class="cm-property">main</span>.<span class="cm-property">output</span> <span class="cm-operator">+</span> <span class="cm-variable">configurations</span>.<span class="cm-property">testRuntimeClasspath</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">runtimeClasspath</span> <span class="cm-operator">+=</span> <span class="cm-variable">output</span> <span class="cm-operator">+</span> <span class="cm-variable">compileClasspath</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">task</span> <span class="cm-variable">functionalTest</span>(<span class="cm-variable">type</span>: <span class="cm-variable">Test</span>) {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">testClassesDirs</span> <span class="cm-operator">=</span> <span class="cm-variable">sourceSets</span>.<span class="cm-property">functionalTest</span>.<span class="cm-property">output</span>.<span class="cm-property">classesDirs</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">21</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">classpath</span> <span class="cm-operator">=</span> <span class="cm-variable">sourceSets</span>.<span class="cm-property">functionalTest</span>.<span class="cm-property">runtimeClasspath</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">22</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">23</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">24</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">check</span>.<span class="cm-property">dependsOn</span> <span class="cm-variable">functionalTest</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">25</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">26</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">gradlePlugin</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">27</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">testSourceSets</span> <span class="cm-variable">sourceSets</span>.<span class="cm-property">functionalTest</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">28</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">29</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">30</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">dependencies</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">31</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">functionalTestImplementation</span>(<span class="cm-string">'org.spockframework:spock-core:1.3-groovy-2.4'</span>) {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">32</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">exclude</span> <span class="cm-variable">module</span>: <span class="cm-string">'groovy-all'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">33</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">34</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 824px;"></div><div class="CodeMirror-gutters" style="height: 854px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><h3 id="sec:working_with_gradle_versions_prior_to_213" style="font-size:1.125rem;"><a class="anchor" href="#sec:working_with_gradle_versions_prior_to_213" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sec:working_with_gradle_versions_prior_to_213" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>使用2.13之前的Gradle版本</font></font></a></h3><div class="paragraph"><p><font><font>对于Gradle的早期版本（2.13之前的版本），可以通过一些额外的配置来手动使被测代码可用。</font><font>下面的示例演示如何让生成的文件包含被测代码的实现类路径，并使其在测试运行时可用。</font></font></p></div><div class="exampleblock"><div class="title" style="font-style: italic;"><font><font>例子5.使测试类路径下的代码可用于测试</font></font></div><div class="content"><div class="multi-language-selector"><code data-lang="groovy" class="language-option selected" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:20px center;background-repeat:no-repeat;background-size:20px 12px;&quot;);">Groovy</code><code data-lang="kotlin" class="language-option" style="font-family: Lato, Arial, sans-serif; font-size: 0.9375rem; color: rgba(0, 0, 0, 0.9); background-image: url(&quot;data:image/svg+xml;background-position:30px center;background-repeat:no-repeat;background-size:11px 11px;&quot;);">Kotlin</code></div><div class="exampleblock testable-sample multi-language-sample" data-lang="groovy"><div class="content"><div class="listingblock"><div class="title" style="font-family: Inconsolata, monospace; background-color: rgb(247, 247, 248); background-image: url(&quot;data:image/svg+xml;background-position:16px 80%;background-repeat:no-repeat;background-size:20px 12px;&quot;);"><font><font>build.gradle</font></font></div></div></div></div></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945189967_482" class="wiz-code-container"><textarea style="display:none;">// Write the plugin's classpath to a file to share with the tests
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
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945189967_482"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="right: 0px; left: 0px; height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 488px; min-width: 761.2px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-comment">// Write the plugin's classpath to a file to share with the tests</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">task</span> <span class="cm-variable">createClasspathManifest</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-variable">outputDir</span> <span class="cm-operator">=</span> <span class="cm-variable">file</span>(<span class="cm-string">"$buildDir/$name"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">inputs</span>.<span class="cm-property">files</span>(<span class="cm-variable">sourceSets</span>.<span class="cm-property">main</span>.<span class="cm-property">runtimeClasspath</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">withPropertyName</span>(<span class="cm-string">"runtimeClasspath"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">withNormalizer</span>(<span class="cm-variable">ClasspathNormalizer</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">outputs</span>.<span class="cm-property">dir</span>(<span class="cm-variable">outputDir</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">withPropertyName</span>(<span class="cm-string">"outputDir"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">doLast</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">outputDir</span>.<span class="cm-property">mkdirs</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">file</span>(<span class="cm-string">"$outputDir/plugin-classpath.txt"</span>).<span class="cm-property">text</span> <span class="cm-operator">=</span> <span class="cm-variable">sourceSets</span>.<span class="cm-property">main</span>.<span class="cm-property">runtimeClasspath</span>.<span class="cm-property">join</span>(<span class="cm-string">"\n"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-comment">// Add the classpath file to the test runtime classpath</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">dependencies</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">testRuntimeOnly</span> <span class="cm-variable">files</span>(<span class="cm-variable">createClasspathManifest</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 488px;"></div><div class="CodeMirror-gutters" style="height: 518px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><div class="paragraph"><p><font><font>然后，测试可以读取该值，并使用方法</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withPluginClasspath（java.lang.Iterable）</font></font></a><font><font>将类路径注入测试版本</font><font>。</font><font>然后可以使用该类路径通过插件DSL在测试版本中定位插件（请参阅</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>插件</font></font></a><font><font>）。</font><font>通过插件DSL应用插件需要定义插件标识符。</font><font>以下是从Spock Framework</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">setup()</code><font><font>方法</font><font>中执行此操作的示例（在Groovy中），该</font><font>方法类似于JUnit</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">@Before</code><font><font>方法。</font></font></p></div></div><div class="sect2"><h3 id="example_injecting_the_code_under_test_classes_into_test_builds" style="font-size:1.125rem;"><a class="anchor" href="#example_injecting_the_code_under_test_classes_into_test_builds" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#example_injecting_the_code_under_test_classes_into_test_builds" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>示例：将测试类下的代码注入测试版本</font></font></a></h3><div class="listingblock multi-language-sample" data-lang="groovy"><div class="title"><font><font>src / test / groovy / org / gradle / sample / BuildLogicFunctionalTest.groovy</font></font></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945189932_969" class="wiz-code-container"><textarea style="display:none;">    List&lt;File&gt; pluginClasspath

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
        buildFile &lt;&lt; """
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
    }</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945189932_969"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="display: block; right: 0px; left: 0px; height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 930px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 800px; min-width: 899.813px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">List</span><span class="cm-operator">&lt;</span><span class="cm-variable">File</span><span class="cm-operator">&gt;</span> <span class="cm-variable">pluginClasspath</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-variable">setup</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">settingsFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'settings.gradle'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'build.gradle'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">def</span> <span class="cm-variable">pluginClasspathResource</span> <span class="cm-operator">=</span> <span class="cm-variable">getClass</span>().<span class="cm-property">classLoader</span>.<span class="cm-property">findResource</span>(<span class="cm-string">"plugin-classpath.txt"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-keyword">if</span> (<span class="cm-variable">pluginClasspathResource</span> <span class="cm-operator">==</span> <span class="cm-atom">null</span>) {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-keyword">throw</span> <span class="cm-keyword">new</span> <span class="cm-variable">IllegalStateException</span>(<span class="cm-string">"Did not find plugin classpath resource, run `testClasses` build task."</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">pluginClasspath</span> <span class="cm-operator">=</span> <span class="cm-variable">pluginClasspathResource</span>.<span class="cm-property">readLines</span>().<span class="cm-property">collect</span> { <span class="cm-keyword">new</span> <span class="cm-variable">File</span>(<span class="cm-variable">it</span>) }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-string">"hello world task prints hello world"</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">given</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">&lt;&lt;</span> <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">plugins</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                <span class="cm-variable">id</span> <span class="cm-string">'org.gradle.sample.helloworld'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">21</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">22</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">23</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">when</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">24</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">def</span> <span class="cm-variable">result</span> <span class="cm-operator">=</span> <span class="cm-variable">GradleRunner</span>.<span class="cm-property">create</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">25</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withProjectDir</span>(<span class="cm-variable">testProjectDir</span>.<span class="cm-property">root</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">26</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withArguments</span>(<span class="cm-string">'helloWorld'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">27</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withPluginClasspath</span>(<span class="cm-variable">pluginClasspath</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">28</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">build</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">29</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">30</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">then</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">31</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">result</span>.<span class="cm-property">output</span>.<span class="cm-property">contains</span>(<span class="cm-string">'Hello world!'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">32</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">result</span>.<span class="cm-property">task</span>(<span class="cm-string">":helloWorld"</span>).<span class="cm-property">outcome</span> <span class="cm-operator">==</span> <span class="cm-variable">SUCCESS</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">33</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 800px;"></div><div class="CodeMirror-gutters" style="height: 830px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><div class="paragraph"><p><font><font>当作为Gradle构建的一部分执行功能测试时，此方法效果很好。</font><font>从IDE执行功能测试时，还有一些额外的注意事项。</font><font>即，类路径清单文件指向Gradle而不是IDE生成的类文件等。</font><font>这意味着在更改被测代码的源代码之后，必须由Gradle重新编译源代码。</font><font>同样，如果被测代码的有效类路径发生变化，则必须重新生成清单。</font><font>无论哪种情况，执行</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">testClasses</code><font><font>构建任务都将确保一切都是最新的。</font></font></p></div><div class="paragraph"><p><font><font>一些IDE提供了一个方便的选项，可以将“测试类路径的生成和执行”委派给构建。</font><font>在IntelliJ中，您可以在``偏好设置...''&gt;``构建，执行，部署''&gt;``构建工具''&gt;``Gradle''&gt;``Runner''&gt;``委派IDE生成/运行操作以进行gradle''下找到此选项。</font><font>请查阅IDE的文档以获取更多信息。</font></font></p></div></div><div class="sect2"><h3 id="sec:working_with_gradle_versions_prior_to_28" style="font-size:1.125rem;"><a class="anchor" href="#sec:working_with_gradle_versions_prior_to_28" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sec:working_with_gradle_versions_prior_to_28" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>使用2.8之前的Gradle版本</font></font></a></h3><div class="paragraph"><p><font><font>当使用早于2.8的Gradle版本执行构建时</font><font>，</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withPluginClasspath（java.lang.Iterable）</font></font></a><font><font>方法将不起作用（请参阅</font></font><a href="#sub:gradle-runner-gradle-version" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>用于测试的版本</font></font></a><font><font>），因为在此类Gradle版本中不支持此功能。</font></font></p></div><div class="paragraph"><p><font><font>相反，必须通过构建脚本本身注入代码。</font><font>下面的示例演示了如何完成此操作。</font></font></p></div></div><div class="sect2"><h3 id="example_injecting_the_code_under_test_classes_into_test_builds_for_gradle_versions_prior_to_2_8" style="font-size:1.125rem;"><a class="anchor" href="#example_injecting_the_code_under_test_classes_into_test_builds_for_gradle_versions_prior_to_2_8" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#example_injecting_the_code_under_test_classes_into_test_builds_for_gradle_versions_prior_to_2_8" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>示例：将测试类下的代码注入2.8之前的Gradle版本的测试版本中</font></font></a></h3><div class="listingblock multi-language-sample" data-lang="groovy"><div class="title"><font><font>src / test / groovy / org / gradle / sample / BuildLogicFunctionalTest.groovy</font></font></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945189887_793" class="wiz-code-container"><textarea style="display:none;">    List&lt;File&gt; pluginClasspath

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

        buildFile &lt;&lt; """
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
    }</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945189887_793"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="display: block; right: 0px; left: 0px; height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 930px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 992px; min-width: 899.813px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">List</span><span class="cm-operator">&lt;</span><span class="cm-variable">File</span><span class="cm-operator">&gt;</span> <span class="cm-variable">pluginClasspath</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-variable">setup</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">settingsFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'settings.gradle'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'build.gradle'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">def</span> <span class="cm-variable">pluginClasspathResource</span> <span class="cm-operator">=</span> <span class="cm-variable">getClass</span>().<span class="cm-property">classLoader</span>.<span class="cm-property">findResource</span>(<span class="cm-string">"plugin-classpath.txt"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-keyword">if</span> (<span class="cm-variable">pluginClasspathResource</span> <span class="cm-operator">==</span> <span class="cm-atom">null</span>) {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-keyword">throw</span> <span class="cm-keyword">new</span> <span class="cm-variable">IllegalStateException</span>(<span class="cm-string">"Did not find plugin classpath resource, run `testClasses` build task."</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">pluginClasspath</span> <span class="cm-operator">=</span> <span class="cm-variable">pluginClasspathResource</span>.<span class="cm-property">readLines</span>().<span class="cm-property">collect</span> { <span class="cm-keyword">new</span> <span class="cm-variable">File</span>(<span class="cm-variable">it</span>) }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-string">"hello world task prints hello world with pre Gradle 2.8"</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">given</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">def</span> <span class="cm-variable">classpathString</span> <span class="cm-operator">=</span> <span class="cm-variable">pluginClasspath</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">collect</span> { <span class="cm-variable">it</span>.<span class="cm-property">absolutePath</span>.<span class="cm-property">replace</span>(<span class="cm-string">'\\'</span>, <span class="cm-string">'\\\\'</span>) } <span class="cm-comment">// escape backslashes in Windows paths</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-variable">collect</span> { <span class="cm-string">"'$it'"</span> }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-variable">join</span>(<span class="cm-string">", "</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">21</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">22</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">&lt;&lt;</span> <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">23</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">buildscript</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">24</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                <span class="cm-variable">dependencies</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">25</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                    <span class="cm-variable">classpath</span> <span class="cm-variable">files</span>(<span class="cm-variable">$classpathString</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">26</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">27</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">28</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">apply</span> <span class="cm-variable">plugin</span>: <span class="cm-string">"org.gradle.sample.helloworld"</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">29</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">30</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">31</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">when</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">32</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">def</span> <span class="cm-variable">result</span> <span class="cm-operator">=</span> <span class="cm-variable">GradleRunner</span>.<span class="cm-property">create</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">33</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withProjectDir</span>(<span class="cm-variable">testProjectDir</span>.<span class="cm-property">root</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">34</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withArguments</span>(<span class="cm-string">'helloWorld'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">35</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withGradleVersion</span>(<span class="cm-string">"2.7"</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">36</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">build</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">37</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">38</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">then</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">39</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">result</span>.<span class="cm-property">output</span>.<span class="cm-property">contains</span>(<span class="cm-string">'Hello world!'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">40</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">result</span>.<span class="cm-property">task</span>(<span class="cm-string">":helloWorld"</span>).<span class="cm-property">outcome</span> <span class="cm-operator">==</span> <span class="cm-variable">SUCCESS</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">41</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 992px;"></div><div class="CodeMirror-gutters" style="height: 1022px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><h2 id="sec:controlling_the_build_environment" style="font-size:1.5rem;"><a class="anchor" href="#sec:controlling_the_build_environment" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sec:controlling_the_build_environment" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>控制构建环境</font></font></a></h2><div class="sectionbody"><div class="paragraph"><p><font><font>运行程序通过在JVM的temp目录（即</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">java.io.tmpdir</code><font><font>，通常</font><font>由</font><font>system属性</font><font>指定的位置）内的目录中指定专用的“工作目录”，在隔离的环境中执行测试构建</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">/tmp</code><font><font>。</font><font>默认的Gradle用户主目录（例如</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">~/.gradle/gradle.properties</code><font><font>）</font><font>中的任何配置</font><font>都不会用于测试执行。</font><font>TestKit并未公开对环境的各个方面进行细粒度控制的机制（例如JDK）。</font><font>未来版本的TestKit将提供改进的配置选项。</font></font></p></div><div class="paragraph"><p><font><font>TestKit使用专用的守护程序进程，这些进程在测试执行后会自动关闭。</font></font></p></div></div></div><div class="sect1"><h2 id="sub:gradle-runner-gradle-version" style="font-size:1.5rem;"><a class="anchor" href="#sub:gradle-runner-gradle-version" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sub:gradle-runner-gradle-version" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>用于测试的Gradle版本</font></font></a></h2><div class="sectionbody"><div class="paragraph"><p><font><font>Gradle运行器需要Gradle发行版才能执行构建。</font><font>TestKit并不依赖于Gradle的所有实现。</font></font></p></div><div class="paragraph"><p><font><font>默认情况下，运行程序将尝试根据从何处</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">GradleRunner</code><font><font>加载类</font><font>来查找Gradle发行版</font><font>。</font><font>也就是说，期望该类是从Gradle发行版加载的，就像使用</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">gradleTestKit()</code><font><font>依赖声明时一样。</font></font></p></div><div class="paragraph"><p><font><font>当将跑步者用作</font></font><em style="font-style:italic;"><font><font>Gradle执行</font></font></em><font><font>的测试的一部分</font><font>（例如执行</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">test</code><font><font>插件项目</font><font>的</font><font>任务）时，跑步者将使用与执行测试相同的发行版。</font><font>当将运行程序用作</font></font><em style="font-style:italic;"><font><font>IDE执行</font></font></em><font><font>的测试的一部分时</font><font>，将使用与导入项目时相同的Gradle发行版。</font><font>这意味着该插件将使用与其构建时相同的Gradle版本进行有效测试。</font></font></p></div><div class="paragraph"><p><font><font>另外，可以通过以下任何一种</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">GradleRunner</code><font><font>方法</font><font>指定要使用的Gradle的不同版本和特定版本</font><font>：</font></font></p></div><div class="ulist"><ul><li><span><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withGradleVersion（java.lang.String）</font></font></a></span></li><li><span><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withGradleInstallation（java.io.File）</font></font></a></span></li><li><span><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withGradleDistribution（java.net.URI）</font></font></a></span></li></ul></div><div class="paragraph"><p><font><font>它可以潜在地用于跨Gradle版本测试构建逻辑。</font><font>以下内容演示了编写为Groovy Spock测试的跨版本兼容性测试：</font></font></p></div><div class="sect2"><h3 id="example_specifying_a_gradle_version_for_test_execution" style="font-size:1.125rem;"><a class="anchor" href="#example_specifying_a_gradle_version_for_test_execution" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#example_specifying_a_gradle_version_for_test_execution" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>示例：指定用于测试执行的Gradle版本</font></font></a></h3><div class="listingblock"><div class="title"><font><font>BuildLogicFunctionalTest.groovy</font></font></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945189843_7508" class="wiz-code-container"><textarea style="display:none;">import org.gradle.testkit.runner.GradleRunner
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
        buildFile &lt;&lt; """
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
        gradleVersion &lt;&lt; ['2.6', '2.7']
    }
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945189843_7508"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 1040px; min-width: 599.663px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">gradle</span>.<span class="cm-property">testkit</span>.<span class="cm-property">runner</span>.<span class="cm-property">GradleRunner</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">static</span> <span class="cm-variable">org</span>.<span class="cm-property">gradle</span>.<span class="cm-property">testkit</span>.<span class="cm-property">runner</span>.<span class="cm-property">TaskOutcome</span>.<span class="cm-operator">*</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-variable">org</span>.<span class="cm-property">junit</span>.<span class="cm-property">Rule</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">org</span>.<span class="cm-variable">junit</span>.<span class="cm-property">rules</span>.<span class="cm-property">TemporaryFolder</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">spock</span>.<span class="cm-variable">lang</span>.<span class="cm-property">Specification</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">import</span> <span class="cm-def">spock</span>.<span class="cm-variable">lang</span>.<span class="cm-property">Unroll</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-keyword">class</span> <span class="cm-def">BuildLogicFunctionalTest</span> <span class="cm-keyword">extends</span> <span class="cm-variable">Specification</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-operator">@</span><span class="cm-variable">Rule</span> <span class="cm-property">final</span> <span class="cm-def">TemporaryFolder</span> <span class="cm-def">testProjectDir</span> <span class="cm-operator">=</span> <span class="cm-keyword">new</span> <span class="cm-variable">TemporaryFolder</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">File</span> <span class="cm-variable">settingsFile</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">File</span> <span class="cm-variable">buildFile</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-variable">setup</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">settingsFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'settings.gradle'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'build.gradle'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-operator">@</span><span class="cm-variable">Unroll</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-string">"can execute hello world task with Gradle version #gradleVersion"</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">given</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">21</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildFile</span> <span class="cm-operator">&lt;&lt;</span> <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">22</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">task</span> <span class="cm-variable">helloWorld</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">23</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                <span class="cm-variable">doLast</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">24</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                    <span class="cm-variable">logger</span>.<span class="cm-property">quiet</span> <span class="cm-string">'Hello world!'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">25</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">26</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">27</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">28</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">29</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">when</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">30</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">def</span> <span class="cm-variable">result</span> <span class="cm-operator">=</span> <span class="cm-variable">GradleRunner</span>.<span class="cm-property">create</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">31</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withGradleVersion</span>(<span class="cm-variable">gradleVersion</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">32</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withProjectDir</span>(<span class="cm-variable">testProjectDir</span>.<span class="cm-property">root</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">33</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">withArguments</span>(<span class="cm-string">'helloWorld'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">34</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            .<span class="cm-property">build</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">35</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">36</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">then</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">37</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">result</span>.<span class="cm-property">output</span>.<span class="cm-property">contains</span>(<span class="cm-string">'Hello world!'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">38</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">result</span>.<span class="cm-property">task</span>(<span class="cm-string">":helloWorld"</span>).<span class="cm-property">outcome</span> <span class="cm-operator">==</span> <span class="cm-variable">SUCCESS</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">39</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">40</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">where</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">41</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">gradleVersion</span> <span class="cm-operator">&lt;&lt;</span> [<span class="cm-string">'2.6'</span>, <span class="cm-string">'2.7'</span>]</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">42</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">43</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 1040px;"></div><div class="CodeMirror-gutters" style="height: 1070px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><h3 id="sub:test-kit-compatibility" style="font-size:1.125rem;"><a class="anchor" href="#sub:test-kit-compatibility" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sub:test-kit-compatibility" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>使用不同的Gradle版本进行测试时的功能支持</font></font></a></h3><div class="paragraph"><p><font><font>可以使用GradleRunner在Gradle 1.0及更高版本中执行构建。</font><font>但是，早期版本不支持某些运行器功能。</font><font>在这种情况下，跑步者在尝试使用功能时会抛出异常。</font></font></p></div><div class="paragraph"><p><font><font>下表列出了对使用的Gradle版本敏感的功能。</font></font></p></div><div class="wiz-table-container" style="position: relative; padding: 0px;"><div class="wiz-table-body"><table class="tableblock frame-all grid-all fit-content" style="background:white;"><caption class="title" style="font-style: italic;"><font><font>表1. Gradle版本兼容性</font></font></caption><colgroup><col><col><col></colgroup><thead style="background:rgb(247, 248, 247);"><tr><th class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><font><font>特征</font></font></th><th class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><font><font>最低版本</font></font></th><th class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><font><font>描述</font></font></th></tr></thead><tbody><tr><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>检查执行的任务</font></font></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>2.5</font></font></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>使用</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>BuildResult.getTasks（）</font></font></a><font><font>和类似方法</font><font>检查执行的任务</font><font>。</font></font></p></td></tr><tr style="background:rgb(248, 248, 247);"><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><a href="#sub:test-kit-classpath-injection" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>插件类路径注入</font></font></a></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>2.8</font></font></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>通过</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withPluginClasspath（java.lang.Iterable）</font></font></a><font><font>注入测试中的代码</font><font>。</font></font></p></td></tr><tr><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><a href="#sub:test-kit-debug" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>在调试模式下检查构建输出</font></font></a></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>2.9</font></font></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>使用</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>BuildResult.getOutput（）</font></font></a><font><font>在调试模式下运行时检查构建的文本输出</font><font>。</font></font></p></td></tr><tr style="background:rgb(248, 248, 247);"><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><a href="#sub:test-kit-automatic-classpath-injection" style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>自动插件类路径注入</font></font></a></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>2.13</font></font></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>通过应用Java Gradle插件开发插件，</font><font>通过</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withPluginClasspath（）</font></font></a><font><font>自动注入被测代码</font><font>。</font></font></p></td></tr><tr><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>设置构建要使用的环境变量。</font></font></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>3.5</font></font></p></td><td class="tableblock halign-left valign-top" style="color:rgba(0, 0, 0, 0.8);"><p class="tableblock" style="color: rgb(2, 48, 58);"><font><font>Gradle Tooling API仅支持在更高版本中设置环境变量。</font></font></p></td></tr></tbody></table></div></div></div></div></div><div class="sect1"><h2 id="sub:test-kit-debug" style="font-size:1.5rem;"><a class="anchor" href="#sub:test-kit-debug" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sub:test-kit-debug" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>调试构建逻辑</font></font></a></h2><div class="sectionbody"><div class="paragraph"><p><font><font>跑步者使用</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>Tooling API</font></font></a><font><font>执行构建。</font><font>这意味着构建是在单独的过程中执行的（即，执行测试的过程不是同一过程）。</font><font>因此，</font><font>以调试方式</font><font>执行</font></font><em style="font-style:italic;"><font><font>测试</font></font></em><font><font>不允许您调试调试逻辑。</font><font>在IDE中设置的任何断点都不会因测试版本执行的代码而跳闸。</font></font></p></div><div class="paragraph"><p><font><font>TestKit提供了两种不同的方式来启用调试模式：</font></font></p></div><div class="ulist"><ul><li><span><font><font>设置“</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">org.gradle.testkit.debug</code><font><font>系统属性”来</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">true</code><font><font>为JVM</font></font><em style="font-style:italic;"><font><font>使用</font></font></em><font><font>的</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">GradleRunner</code><font><font>（与所述流道执行即不生成）;</font></font></span></li><li><span><font><font>调用</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withDebug（boolean）</font></font></a><font><font>方法。</font></font></span></li></ul></div><div class="paragraph"><p><font><font>当需要启用调试支持而不对流道配置进行临时更改时，可以使用系统属性方法。</font><font>大多数IDE提供了设置JVM系统属性以执行测试的功能，并且可以使用此功能来设置此系统属性。</font></font></p></div></div></div><div class="sect1"><h2 id="sub:test-kit-build-cache" style="font-size:1.5rem;"><a class="anchor" href="#sub:test-kit-build-cache" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#sub:test-kit-build-cache" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>使用构建缓存进行测试</font></font></a></h2><div class="sectionbody"><div class="paragraph"><p><font><font>要</font><font>在测试中</font><font>启用</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>构建缓存</font></font></a><font><font>，可以将</font></font><code style="font-family:Inconsolata, monospace;font-size:0.9375rem;color:rgba(0, 0, 0, 0.9);background-color:rgb(247, 247, 248);">--build-cache</code><font><font>参数</font><font>传递</font><font>给</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner</font></font></a><font><font>或使用</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;"><font><font>启用构建缓存中</font></font></a><font><font>描述的其他方法之一</font><font>。</font><font>然后，可以</font><font>在缓存插件的自定义任务时</font><font>检查任务结果</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>TaskOutcome.FROM_CACHE</font></font></a><font><font>。</font><font>此结果仅对Gradle 3.5及更高版本有效。</font></font></p></div><div class="sect2"><h3 id="example_testing_cacheable_tasks" style="font-size:1.125rem;"><a class="anchor" href="#example_testing_cacheable_tasks" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#example_testing_cacheable_tasks" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>示例：测试可缓存任务</font></font></a></h3><div class="listingblock"><div class="title"><font><font>BuildLogicFunctionalTest.groovy</font></font></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945189815_698" class="wiz-code-container"><textarea style="display:none;">def "cacheableTask is loaded from cache"() {
    given:
    buildFile &lt;&lt; """
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
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945189815_698"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 608px; min-width: 445.7px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">def</span> <span class="cm-string">"cacheableTask is loaded from cache"</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">given</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">buildFile</span> <span class="cm-operator">&lt;&lt;</span> <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">plugins</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">id</span> <span class="cm-string">'org.gradle.sample.helloworld'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">when</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">def</span> <span class="cm-variable">result</span> <span class="cm-operator">=</span> <span class="cm-variable">runner</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">withArguments</span>( <span class="cm-string">'--build-cache'</span>, <span class="cm-string">'cacheableTask'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">build</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">then</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">result</span>.<span class="cm-property">task</span>(<span class="cm-string">":cacheableTask"</span>).<span class="cm-property">outcome</span> <span class="cm-operator">==</span> <span class="cm-variable">SUCCESS</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">16</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">17</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">when</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">18</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-keyword">new</span> <span class="cm-variable">File</span>(<span class="cm-variable">testProjectDir</span>.<span class="cm-property">root</span>, <span class="cm-string">'build'</span>).<span class="cm-property">deleteDir</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">19</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">result</span> <span class="cm-operator">=</span> <span class="cm-variable">runner</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">20</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">withArguments</span>( <span class="cm-string">'--build-cache'</span>, <span class="cm-string">'cacheableTask'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">21</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        .<span class="cm-property">build</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">22</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">23</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">then</span>:</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">24</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">result</span>.<span class="cm-property">task</span>(<span class="cm-string">":cacheableTask"</span>).<span class="cm-property">outcome</span> <span class="cm-operator">==</span> <span class="cm-variable">FROM_CACHE</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">25</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 608px;"></div><div class="CodeMirror-gutters" style="height: 638px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><div id="content" style="color: rgb(2, 48, 58); font-family: Lato, &quot;Helvetica Neue&quot;, Arial, sans-serif;"><div class="sect1"><div class="sectionbody"><div class="sect2"><div class="paragraph"><p><font><font>请注意，TestKit在测试之间重用了Gradle用户主目录（请参阅</font></font><a href style="color:rgb(29, 162, 189);text-decoration:none;font-family:Inconsolata, monospace;"><font><font>GradleRunner.withTestKitDir（java.io.File）</font></font></a><font><font>），其中包含本地构建缓存的默认位置。</font><font>为了使用构建缓存进行测试，应该在测试之间清理构建缓存目录。</font><font>完成此操作的最简单方法是将本地构建缓存配置为使用临时目录。</font></font></p></div></div><div class="sect2"><h3 id="example_clean_build_cache_between_tests" style="font-size:1.125rem;"><a class="anchor" href="#example_clean_build_cache_between_tests" style="color: rgb(29, 162, 189); text-decoration: none;"></a><a class="link" href="#example_clean_build_cache_between_tests" style="color:rgb(2, 48, 58);text-decoration:none;"><font><font>示例：在测试之间清理构建缓存</font></font></a></h3><div class="listingblock"><div class="title"><font><font>BuildLogicFunctionalTest.groovy</font></font></div></div></div></div></div></div></div><div data-mode="JavaScript" data-theme="default" id="wiz_cm_1605945189776_120" class="wiz-code-container"><textarea style="display:none;">@Rule final TemporaryFolder testProjectDir = new TemporaryFolder()
File buildFile
File localBuildCacheDirectory

def setup() {
    localBuildCacheDirectory = testProjectDir.newFolder('local-cache')
    testProjectDir.newFile('settings.gradle') &lt;&lt; """
        buildCache {
            local {
                directory '${localBuildCacheDirectory.toURI()}'
            }
        }
    """
    buildFile = testProjectDir.newFile('build.gradle')
}</textarea><wiz_code_mirror><div class="CodeMirror cm-s-default" data-id="wiz_cm_1605945189776_120"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 20px; left: 34px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" tabindex="0" style="padding:0px; width:1000px; height:1em;"></textarea></div><div class="wiz-hide wiz_CodeMirror-vscrollbar" style="width: 18px; pointer-events: none;"><div style="min-width: 1px; height: 0px;"></div></div><div class="wiz-hide wiz_CodeMirror-hscrollbar" style="height: 18px; pointer-events: none;"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler"></div><div class="CodeMirror-gutter-filler"></div><div class="CodeMirror-scroll"><div class="CodeMirror-sizer" style="margin-left: 30px; margin-bottom: 0px; border-right-width: 30px; min-height: 368px; min-width: 545.725px; padding-right: 0px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre class="CodeMirror-line-like"></pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="wiz-hide wiz_CodeMirror-cursors"><div class="CodeMirror-cursor" style="left: 4px; top: 0px; height: 24px;">&nbsp;</div></div><div class="CodeMirror-code"><div class="CodeMirror-activeline" style="position: relative;"><div class="wiz-hide wiz_CodeMirror-activeline-background CodeMirror-linebackground"></div><div class="CodeMirror-gutter-background CodeMirror-activeline-gutter" style="left: -30px; width: 30px;"></div><div class="CodeMirror-gutter-wrapper CodeMirror-activeline-gutter" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">1</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-operator">@</span><span class="cm-variable">Rule</span> <span class="cm-variable">final</span> <span class="cm-variable">TemporaryFolder</span> <span class="cm-variable">testProjectDir</span> <span class="cm-operator">=</span> <span class="cm-keyword">new</span> <span class="cm-variable">TemporaryFolder</span>()</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">2</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">File</span> <span class="cm-variable">buildFile</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">3</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">File</span> <span class="cm-variable">localBuildCacheDirectory</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">4</div></div><pre class="CodeMirror-line"><span style="padding-right:0.1px"></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">5</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;"><span class="cm-variable">def</span> <span class="cm-variable">setup</span>() {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">6</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">localBuildCacheDirectory</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFolder</span>(<span class="cm-string">'local-cache'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">7</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'settings.gradle'</span>) <span class="cm-operator">&lt;&lt;</span> <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">8</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        <span class="cm-variable">buildCache</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">9</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            <span class="cm-variable">local</span> {</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">10</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">                <span class="cm-variable">directory</span> <span class="cm-string">'${localBuildCacheDirectory.toURI()}'</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">11</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">            }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">12</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">        }</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">13</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-string">"""</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">14</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">    <span class="cm-variable">buildFile</span> <span class="cm-operator">=</span> <span class="cm-variable">testProjectDir</span>.<span class="cm-property">newFile</span>(<span class="cm-string">'build.gradle'</span>)</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -30px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 21px;">15</div></div><pre class="CodeMirror-line"><span style="padding-right: 0.1px;">}</span></pre></div></div></div></div></div></div><div style="position: absolute; height: 13px; width: 1px; border-bottom: 0px solid transparent; top: 368px;"></div><div class="CodeMirror-gutters" style="height: 398px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 29px;"></div></div></div></div></wiz_code_mirror></div><div><br></div></body></html>