# Node  基本命令

### 镜像源

1、查看当前 npm 配置信息。

```shell
npm config list
```

2、查看当前 npm 镜像源地址。

```shell
npm config get registry
```

3、设置 npm 镜像源为指定的地址。其中，`<registry_url>` 为镜像源地址。

```shell
npm config set registry <registry_url>
```

4、设置 npm 镜像源为淘宝镜像源。

```shell
npm config set registry https://registry.npm.taobao.org/
```

5、设置 npm 镜像源为官方源（默认源）。

```shell
npm config set registry https://registry.npmjs.org/
```

6、恢复 npm 镜像源为默认地址。

```shell
npm config delete registry
```

### Node.js

1、查看当前安装的 Node.js 版本号。

```shell
node -v
```

2、运行名为 "app.js" 的 JavaScript 文件（如果文件名不同，则将其替换为相应的文件名）。

```shell
node app.js
```

3、创建一个新的 Node.js 项目。

```shell
npm init
```

4、安装指定名称的软件包。

```shell
npm install <package_name>
```

5、卸载指定名称的软件包。


```shell
npm uninstall <package_name>
```
6、更新所有已安装的软件包。

```shell
npm update
```

7、检查哪些软件包需要更新。

```shell
npm outdated
```

8、列出所有已安装的软件包，包括其版本号和依赖项。

```shell
npm list
```

9、启动应用程序。

```shell
npm start
```

10、运行测试，该测试通常包含在 package.json 文件中。

```shell
npm test
```

### Vue.js

1、查看当前安装的 Vue.js 版本号。

```shell
vue --version
```

2、创建新的 Vue.js 项目。

```shell
vue create <project_name>
```

3、添加指定插件到 Vue.js 项目中。

```shell
vue add <plugin_name>
```

4、使用图形用户界面管理 Vue.js 项目。

```shell
vue ui
```

5、在开发环境中启动 Vue.js 应用程序并实时重新加载更改。

```shell
vue serve
```

6、生成生产版本的 Vue.js 应用程序。

```shell
vue build
```

7、检查 Vue.js 项目的 webpack 配置。

```shell
vue inspect
```

8、执行 "serve" 命令并启动开发服务器。

```shell
npm run serve
```

9、执行 "build" 命令并生成生产版本的应用程序。

```shell
npm run build
```

10、执行静态代码分析工具 Eslint 进行代码校验。

```shell
npm run lint
```

11、将 Vue CLI 的全局包更新到最新版本。

```shell
vue upgrade
```

12、显示有关本地环境和全局包的详细信息。

```shell
vue info
```

13、使用模板初始化新的 Vue.js 项目。

```shell
vue init
```

14、在文件更改时持续构建应用程序。

```shell
vue build --watch
```

15、启动开发服务器并自动打开浏览器。

```shell
vue serve --open
```

