# GradleUserGuideZh

## 这是什么？
本仓库提供Gradle文档的翻译，主要内容为常见文章翻译。原文见[Gradle](https://docs.gradle.org/current/userguide/userguide.html) 

## 如何保证时效性
考虑到Gradle的更新速度，会以月为周期及时更新文档

## 翻译效果
因笔者个人能力及精力原因，大部分翻译还是机器翻译。后续会陆续优化，欢迎提交issue优化

## 目录
- [首页](/md/首页.md)
- [发行说明](/md/Gradle发行说明.md)
- 用户手册
  - [什么是Gradle](/md/什么是Gradle.md)
  - [入门](/md/入门.md)
  - [安装Gradle](/md/安装Gradle.md)
  - 升级Gradle
    - [将您的构建从Gradle 4.x升级到5.0](/md/将您的构建从Gradle%204.md)
    - [将您的构建从Gradle 5.x升级到6.0](/md/将您的构建从Gradle%205.md)
    - [将您的构建从Gradle 6.x升级到最新](/md/将您的构建从Gradle%206.md)
    - [功能生命周期](/md/功能生命周期.md)
  - 迁移至Gradle
    - [从Apache Maven迁移](/md/从Apache%20Maven迁移.md)
    - [从Apache Ant迁移](/md/从Apache%20Ant迁移.md)
  - [兼容性说明](/md/兼容性说明.md)
  - [对构建进行故障排除](/md/对构建进行故障排除.md)
- 运行Gradle构建
  - 自定义执行
    - [Gradle环境搭建](/md/Gradle环境搭建.md)
    - [Gradle守护程序](/md/Gradle守护程序.md)
    - [初始化脚本](/md/初始化脚本.md)
  - [执行多项目构建](/md/执行多项目构建.md)
  - [检查Gradle构建](https://scans.gradle.com/)
  - 优化构建时间
    - [Gradle构建性能优化](/md/改善Gradle构建的性能.md)
    - [启用和配置构建缓存](/md/构建缓存.md)
    - [使用配置缓存](/md/配置缓存.md)
- 编写Gradle构建
  - 基础学习
    - [构建脚本基础](/md/构建脚本基础.md)
    - [处理任务](/md/处理任务.md)
    - [了解构建脚本更多信息](/md/编写构建脚本.md)
    - [处理文件](/md/处理文件.md)
    - [使用Gradle插件](/md/使用Gradle插件.md)
    - [构建生命周期](/md/构建生命周期.md)
    - [使用记录](/md/使用记录.md)
    - [问题避免](/md/避免陷阱.md)
  - 创建多项目构建
    - [创建多项目构建](/md/Gradle中的多项目构建.md)
    - [声明子项目之间的依赖关系](/md/声明子项目之间的依赖关系.md)
    - [在子项目之间共享构建逻辑](/md/在子项目之间共享构建逻辑.md)
    - [微调项目布局](/md/微调项目布局.md)
    - [了解配置执行](/md/配置时间和执行时间.md)
  - 创建可维护的构建
    - [组织Gradle项目](/md/组织Gradle项目.md)
    - [最佳实践](/md/创作可维护版本的最佳实践.md)
  - 高级设置
    - [开发并行任务](/md/使用Worker%20API开发并行任务.md)
    - [使用TestKit测试构建](/md/使用TestKit测试构建逻辑.md)
    - [从Gradle使用Ant](/md/从Gradle使用Ant.md)
- 编写JVM构建
  - [构建Java和JVM项目](/md/构建Java和JVM项目.md)
  - [测试Java和JVM项目](/md/在Java和JVM项目中进行测试.md)
  - [Java项目的工具链](/md/JVM项目的工具链.md)
  - [管理依赖关系](/md/管理JVM项目的依赖关系.md)
  - JVM插件
    - [Java库插件](/md/Java库插件.md)
    - [Java应用插件](/md/Java应用插件.md)
    - [Java平台插件](/md/Java平台插件.md)
    - [Groovy插件](/md/Groovy插件.md)
    - [Scala插件](/md/Scala插件.md)
- 编写C ++ / Swift构建
  - [构建C++项目](/md/构建C++项目.md)
  - [测试C++项目](/md/在C++项目中进行测试.md)
  - [构建Swift项目](/md/建立Swift项目.md)
  - [测试Swift项目](/md/在Swift项目中进行测试.md)
- 使用依赖项
  - 学习基础
      - [什么是依赖管理](/md/Gradle中的依赖管理.md)
      - [声明存储库](/md/声明存储库.md)
      - [声明依赖](/md/声明依赖.md)        
      - [了解库和应用程序之间的区别](/md/了解库和应用程序之间的区别.md)
      - [查看和调试依赖项](/md/查看和调试依赖项.md)
      - [了解依赖解析](/md/了解依赖性解析.md)
      - [验证依赖关系](/md/验证依赖关系.md)
  - 声明版本
      - [声明版本和范围](/md/声明版本和范围.md)
      - [声明丰富版本](/md/声明丰富版本.md)
      - [处理版本变更](/md/处理随时间变化的版本.md)        
      - [锁定版本](/md/锁定依赖版本.md)
  - 转变控制      
      - [升级版本](/md/升级传递依赖的版本.md)
      - [降级和排查排除依赖项](/md/降级版本并排除依赖项.md)
      - [共享版本](/md/在项目之间共享依赖版本.md)
      - [对齐依赖](/md/对齐依赖版本.md)
      - [处理互斥依赖](/md/处理互斥依赖性.md)
      - [修复元数据](/md/使用组件元数据规则修复元数据.md)
      - [自定义依赖解析](/md/直接自定义依赖项的解析.md)
      - [防止意外升级](/md/防止意外的依赖升级.md)
  - Library的生产和消费形式
      - [声明Library的能力](/md/声明Library的能力.md)
      - [建模功能变体和可选依赖项](/md/建模功能变体和可选依赖项.md)
      - [了解变体选择](/md/了解变体选择.md)
      - [了解变体属性](/md/使用变体属性.md)
      - [在项目之间共享输出](/md/在项目之间共享输出.md)
      - [Artifact转换](/md/转换解决方案上的依赖工件.md)
  - 在多仓库环境中工作
      - [复合构建](/md/复合构建.md)
  - 发布Library
      - [设置发布](/md/将项目发布为模块.md)
      - [了解Gradle模块元数据](/md/了解Gradle模块元数据.md)
      - [签名作品](/md/签名作品.md)
      - [定制发布](/md/定制发布.md)
      - [Maven发布插件](/md/Maven发布插件.md)
      - [Ivy发布插件](/md/Ivy发布插件.md)
  - [术语](/md/依赖管理术语.md)
- Gradle扩展
  - [插件开发教程](https://gradle.org/guides/?q=Plugin%20Development)
  - [编写Gradle任务类型](/md/开发自定义Gradle任务类型.md)
  - [编写Gradle插件](/md/开发自定义Gradle插件.md)
  - [编写自定义Gradle类型](/md/开发自定义Gradle类型.md)
  - [延迟配置任务](/md/延迟配置.md)
  - [使用避免任务配置](https://docs.gradle.org/6.7.1/userguide/task_configuration_avoidance.html)
- 参考
  - [核心插件](/md/Gradle插件参考.md)
  - [命令行界面](/md/命令行界面.md)
  - [Gradle和第三方工具](/md/Gradle和第三方工具.md)
  - [gradle wrapper](/md/gradle_wrapper.md)
  - [Gradle管理的目录](/md/Gradle目录和文件的使用.md)

## 致谢
* 感谢[Gradle](https://docs.gradle.org/) 提供优秀的工具与文档
* 感谢我的好友郑PP提供翻译咨询
* 还要感谢我实际上不存在的女朋友，感谢她体谅我创作不易，所以至今没出现在我生命里，让我有如此多的精力折腾