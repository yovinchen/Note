# Maven 生命周期

|   阶段   |   处理   |                           描述                           |
| :------: | :------: | :------------------------------------------------------: |
|  clean   |   清除   |                         清理项目                         |
| validate | 验证项目 |          验证项目是否正确且所有必须信息是可用的          |
| compile  | 执行编译 |                  源代码编译在此阶段完成                  |
|   Test   |   测试   |      使用适当的单元测试框架（例如JUnit）运行测试。       |
| package  |   打包   |         创建JAR/WAR包如在 pom.xml 中定义提及的包         |
|  verify  |   检查   |         对集成测试的结果进行检查，以保证质量达标         |
| install  |   安装   |        安装打包的项目到本地仓库，以供其他项目使用        |
|   site   |   建立   |                     生成项目站点文档                     |
|  deploy  |   部署   | 拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程 |

# 详解

- maven包含三套**`相互独立`**的生命周期。
- `clean`生命周期：用于清理项目。
- `default`生命周期：用于构建项目。
- `site`生命周期：用于建立项目站点。

## Clean 生命周期

clean生命周期的目的是清理项目，它包含3个阶段：

| clean生命周期阶段 | 说明                          |
| ----------------- | ----------------------------- |
| pre-clean         | 执行一些clean前需要完成的工作 |
| `clean`           | 清理上一次构建生成的文件      |
| post-clean        | 执行一些clean后需要完成的工作 |

## default生命周期

default生命周期的目的是构建项目，它定义了真正构建时所需要完成的所有步骤，是所有生命周期中最核心的部分。
包含23个阶段：[详细介绍](https://link.segmentfault.com/?enc=adC1EeCKc3GrSxn7%2BTgI1A%3D%3D.Ju%2FVbyKNoLIC%2B5PcetbZtDSB6B0AyYmo3u4p50LueCAc32gWxjgPlEDdIRH8IGAw8rkW%2F9ghBqAd5LxK8%2FbGYfDBBQQ9Jx9lABkYckE7ThE%3D)

   | default生命周期阶段     | 说明                                                       |
   | ----------------------- | ---------------------------------------------------------- |
   | validate                | 验证项目是否正确并且所有必要信息都可用                     |
   | initialize              | 初始化构建状态，比如设置属性值、创建目录                   |
   | generate-sources        | 生成包含在编译阶段中的任何源代码                           |
   | process-sources         | 处理源代码，比如说，过滤任意值                             |
   | generate-resources      | 生成将会包含在项目包中的资源文件                           |
   | process-resources       | 复制和处理资源到目标目录，为打包阶段最好准备               |
   | `compile`               | 编译项目的源代码                                           |
   | process-classes         | 处理编译生成的文件，比如说对Java class文件做字节码改善优化 |
   | generate-test-sources   | 生成包含在编译阶段中的任何测试源代码                       |
   | process-test-sources    | 处理测试源代码，比如说，过滤任意值                         |
   | generate-test-resources | 为测试创建资源文件                                         |
   | process-test-resources  | 复制和处理测试资源到目标目录                               |
   | test-compile            | 编译测试源代码到测试目标目录                               |
   | process-test-classes    | 处理测试源码编译生成的文件                                 |
   | `test`                  | 使用合适的单元测试框架运行测试 , 测试代码不会被打包或部署  |
   | prepare-package         | 在实际打包之前，执行任何的必要的操作为打包做准备           |
   | `package`               | 将编译后的代码打包成可分发的格式，比如JAR                  |
   | pre-integration-test    | 在执行集成测试前进行必要的动作。比如说，搭建需要的环境     |
   | integration-test        | 如有必要，将程序包处理并部署到可以运行集成测试的环境中     |
   | post-integration-test   | 执行集成测试完成后进行必要的动作。比如说，清理集成测试环境 |
   | verify                  | 运行任何检查以验证包是否有效并符合质量标准                 |
   | `install`               | 安装项目包到maven本地仓库，供本地其他maven项目使用         |
   | `deploy`                | 将最终包复制到远程仓库，供其他开发人员和maven项目使用      |

## site生命周期

site生命周期的目的是建立和发布项目站点。Maven能够基于pom.xml所包含的信息，自动生成一个友好的站点，方便团队交流和发布项目信息。
包含以下4个阶段：

| site生命周期阶段 | 说明                                     |
| ---------------- | ---------------------------------------- |
| pre-site         | 执行一些在生成项目站点之前需要完成的工作 |
| site             | 生成项目站点文档                         |
| post-site        | 执行一些在生成项目站点之后需要完成的工作 |
| site-deploy      | 将生成的项目站点发布到服务器上           |