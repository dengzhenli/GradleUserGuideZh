# 声明Library的能力


内容

  * [能力是首要概念](#%E8%83%BD%E5%8A%9B%E6%98%AF%E9%A6%96%E8%A6%81%E6%A6%82%E5%BF%B5)
  * [声明外部模块的功能](#%E5%A3%B0%E6%98%8E%E5%A4%96%E9%83%A8%E6%A8%A1%E5%9D%97%E7%9A%84%E5%8A%9F%E8%83%BD)
  * [声明本地组件的其他功能](#%E5%A3%B0%E6%98%8E%E6%9C%AC%E5%9C%B0%E7%BB%84%E4%BB%B6%E7%9A%84%E5%85%B6%E4%BB%96%E5%8A%9F%E8%83%BD)

## [能力是首要概念](#%E8%83%BD%E5%8A%9B%E6%98%AF%E9%A6%96%E8%A6%81%E6%A6%82%E5%BF%B5)

组件提供了许多功能，这些功能通常与用于提供那些功能的软件体系结构正交。例如，一个库可能在单个工件中包含多个功能。但是，这样的库将以单个GAV（组，工件和版本）坐标发布。这意味着，在单个坐标处，可能会共存组件的不同“特征”。

使用Gradle，显式声明组件提供的功能变得很有趣。为此，Gradle提供了[功能](/md/升级传递依赖的版本.md#在传递依赖项上添加约束)的概念。

通常通过组合不同的 _功能_ 来构建 _功能_ 。

在理想的情况下，组件不应声明对显式GAV的依赖，而应在功能方面表达其要求：

  * “给我一个提供日志记录的组件”

  * “给我一个脚本引擎”

  * “给我一个支持Groovy的脚本引擎”

通过对 _功能进行_ 建模，依赖关系管理引擎可以更智能，并在依赖关系图中的 _不兼容功能_ 告诉您，或者让您选择何时图中的不同模块提供相同 _功能_ 。

## [声明外部模块的功能](#%E5%A3%B0%E6%98%8E%E5%A4%96%E9%83%A8%E6%A8%A1%E5%9D%97%E7%9A%84%E5%8A%9F%E8%83%BD)

值得注意的是，Gradle支持声明的功能可用于构建的组件，也可以声明外部组件的功能（如果没有）。

例如，如果您的构建文件包含以下依赖项：

例子1.一个带有日志框架隐式冲突的构建文件

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        // This dependency will bring log4:log4j transitively
        implementation 'org.apache.zookeeper:zookeeper:3.4.9'
    
        // We use log4j over slf4j
        implementation 'org.slf4j:log4j-over-slf4j:1.7.10'
    }

build.gradle.kts

    
    
    dependencies {
        // This dependency will bring log4:log4j transitively
        implementation("org.apache.zookeeper:zookeeper:3.4.9")
    
        // We use log4j over slf4j
        implementation("org.slf4j:log4j-over-slf4j:1.7.10")
    }

照原样，很难确定在类路径上最终会有两个日志记录框架。实际上，`zookeeper`将引入`log4j`我们想要使用的位置`log4j-over-
slf4j`。我们可以通过添加一条规则来抢先检测冲突，该规则将声明两个日志记录框架提供相同的功能：

例子2.一个带有日志框架隐式冲突的构建文件

`Groovy``Kotlin`

build.gradle

    
    
    dependencies {
        // Activate the "LoggingCapability" rule
        components.all(LoggingCapability)
    }
    
    @CompileStatic
    class LoggingCapability implements ComponentMetadataRule {
        final static Set<String> LOGGING_MODULES = ["log4j", "log4j-over-slf4j"] as Set<String>
    
        void execute(ComponentMetadataContext context) {
            context.details.with {
                if (LOGGING_MODULES.contains(id.name)) {
                    allVariants {
                        it.withCapabilities {
                            // Declare that both log4j and log4j-over-slf4j provide the same capability
                            it.addCapability("log4j", "log4j", id.version)
                        }
                    }
                }
            }
        }
    }

build.gradle.kts

    
    
    dependencies {
        // Activate the "LoggingCapability" rule
        components.all(LoggingCapability::class.java)
    }
    
    class LoggingCapability : ComponentMetadataRule {
        val loggingModules = setOf("log4j", "log4j-over-slf4j")
    
        override
        fun execute(context: ComponentMetadataContext) = context.details.run {
            if (loggingModules.contains(id.name)) {
                allVariants {
                    withCapabilities {
                        // Declare that both log4j and log4j-over-slf4j provide the same capability
                        addCapability("log4j", "log4j", id.version)
                    }
                }
            }
        }
    }

通过添加此规则，我们将确保Gradle _将_ 检测到冲突并适当地失败：

    
    
    > Could not resolve all files for configuration ':compileClasspath'.
       > Could not resolve org.slf4j:log4j-over-slf4j:1.7.10.
         Required by:
             project :
          > Module 'org.slf4j:log4j-over-slf4j' has been rejected:
               Cannot select module with conflict on capability 'log4j:log4j:1.7.10' also provided by [log4j:log4j:1.2.16(compile)]
       > Could not resolve log4j:log4j:1.2.16.
         Required by:
             project : > org.apache.zookeeper:zookeeper:3.4.9
          > Module 'log4j:log4j' has been rejected:
               Cannot select module with conflict on capability 'log4j:log4j:1.2.16' also provided by [org.slf4j:log4j-over-slf4j:1.7.10(compile)]
               
请参阅[文档](/md/处理互斥依赖性.md#在候选人之间进行选择)的[功能部分，](/md/处理互斥依赖性.md#在候选人之间进行选择)以了解如何解决功能冲突。

## [声明本地组件的其他功能](#%E5%A3%B0%E6%98%8E%E6%9C%AC%E5%9C%B0%E7%BB%84%E4%BB%B6%E7%9A%84%E5%85%B6%E4%BB%96%E5%8A%9F%E8%83%BD)

所有组件都具有与该组件相同的GAV坐标对应的 _隐式功能_ 。只要在不同GAV坐标处发布的库是同一API的 _替代实现_
，就很方便。但是，也可以为组件声明其他 _显式功能_ ：

示例3.声明组件的功能

`Groovy``Kotlin`

build.gradle

    
    
    configurations {
        apiElements {
            outgoing {
                capability("com.acme:my-library:1.0")
                capability("com.other:module:1.1")
            }
        }
        runtimeElements {
            outgoing {
                capability("com.acme:my-library:1.0")
                capability("com.other:module:1.1")
            }
        }
    }

build.gradle.kts

    
    
    configurations {
        apiElements {
            outgoing {
                capability("com.acme:my-library:1.0")
                capability("com.other:module:1.1")
            }
        }
        runtimeElements {
            outgoing {
                capability("com.acme:my-library:1.0")
                capability("com.other:module:1.1")
            }
        }
    }

功能必须附加到 _传出配置_ ， _传出配置_
是组件的[消耗性配置](/md/声明依赖.md#可解析和消耗性配置)。

此示例显示我们声明了两个功能：

  1. `com.acme:my-library:1.0`，它对应于库的 _隐式功能_

  2. `com.other:module:1.1`，它对应于此库的另一功能

值得注意的是，我们需要执行1.，因为一旦您开始声 _明显式_ 功能，就需要声明 _所有_ 功能，包括 _隐式功能_ 。

第二功能可以特定于此库，也可以对应于外部组件提供的功能。在这种情况下，如果`com.other:module`出现在相同的依赖图中，则构建将失败，并且使用者[将不得不选择要使用的模块](/md/处理互斥依赖性.md#在候选人之间进行选择)。

功能已发布到Gradle模块元数据。但是，它们在POM或Ivy元数据文件中 _没有等效项_
。因此，发布此类组件时，Gradle会警告您该功能仅适用于Gradle使用者：

    
    
    Maven publication 'maven' contains dependencies that cannot be represented in a published pom file.
     - Declares capability com.acme:my-library:1.0
     - Declares capability com.other:module:1.1

