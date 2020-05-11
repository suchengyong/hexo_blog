---
title: 13个npm快速使用开发技巧
date: 2020-05-10 10:03:58
tags: webpack
categories: 前端开发 
thumbnail: http://attach.bbs.miui.com/forum/201501/24/124634p9g93m9o1ecoy4cz.jpg
---

## 1.学习基本快捷方式
我们从最基本的开始,学习最常见的npm快捷方式从长远来将会节省很多时间。

* 安装  —  常规：npm install，简写：npm i。
* 测试  —  常规：npm test，简写：npm t。
* 帮助  —  常规：npm --help，简写：npm -h。
* 全局标志 —  常规： --global，简写：-g。
* 保存为开发依赖 - 常规： - save-dev，简写：-D。
* npm init 默认值 - 常规：npm init --yes 或 npm init --force，简写：npm init -y 或 npm init -f

我们知道使用-save或-S来保存包，但现在这是个已经是默认值。要安装一个包而不保存它，可以使用 ——no-save标志。
#### 不太常见的快捷键
还有一些不常见的快捷方式，如下：
* 安装包信息将加入到optionalDependencies（可选阶段的依赖）- 常规：--save-optional， 简写：-O。
* 精确安装指定模块版本 - 常规：--save-optional， 简写：-O。
如果需要在本地保存一个npm包，或者通过单个文件下载选择一组可用的包，可以使用--save-bundle或-B将它们捆绑在一起，并使用npm pack获得捆绑包

#### 根的快捷方式
* 符号通常用于表示应用程序的根目录，npm术语中的应用程序入口点，即package.json中指定为“main”的值
```
{
  "main": "index.js"
}
```
这个快捷方式也可以用于像npx create-react-app . 这样的命令。因此，可以运行npx create-react-app .，而不是使用 npx create-react-app my-app 创建一个新的my-app目录

### 2. 设置默认npm init属性

当运行npm init开始一个新项目时，你可能会发现自己一次又一次地输入配置细节。假如，你可能是项目的主要负责人。有时为了节省时间，可以为这些字段设置默认值，如下所示：
```
npm config set init.author.name "Joe Bloggs"
npm config set init.author.email "JoebLoggs@gmail.com"
npm config set init.author.url "Joebloggs.com"
npm config set init.license "MIT"
```
要检查是否正确添加了这些属性，在终端输入 npm config edit查看配置文件信息。当然也j可以通过直接在打开的配置文件编辑信息。 如果要编辑全局npm设置，使用npm config edit -g。
要重新初始化默认设置，可以使用以下脚本。第一行用空字符串替换配置文件，第二行用默认设置重新填充配置文件。
```
echo "" > $(npm config get userconfig)
npm config edit
```
上面的脚本将重置用户默认值，下面的脚本将重置全局默认值
```
echo "" > $(npm config get globalconfig)
npm config --global edit
```
### 3. 让脚本跨平台兼容
任何在命令行上运行的代码都有兼容性问题的风险，特别是在Windows和基于unix的系统(包括Mac和Linux)之间。如果你只处理特定的项目，那么这不是问题，但是在许多情况下，跨平台兼容性很有必要的:任何开放源码或协作项目，以及示例和教程项目，都应该能够工作，而不管操作系统是什么。
谢天谢地，解决方案很简单。有几个选项可供选择，但效果最好的是cross-env。使用npm i -D cross-env将其作为开发依赖项安装。然后在任何环境变量之前包括关键字cross-env，就像这样：
```
{
  "scripts": {
    "build": "cross-env NODE_ENV=production webpack --config build/wepack.config.js"
  }
}
```
cross-env是 实现跨平台兼容性的最无缝的方法，但还有其他两个流行的工具，它们可以帮助实现跨平台兼容性：
* rimraf 可以安装在全球运行跨平台脚本
* ShellJS 是Unix shell命令在Node.js API上的可移植实现。
### 4. 并行运行脚本
可以使用&&来依次运行两个或多个进程。但是并行运行脚本呢?为此，我们可以从各种npm包中进行选择。concurrent 和 npm-run-all 是最流行的解决方案。
首先通过 npm i -D concurrently 安装开发依赖。然后按照以下格式将其添加到脚本中：
```
{
  "start": "concurrently \"command1 arg\" \"command2 arg\""
}
```
### 5. 在不同的目录中运行脚本
有时，在不同的文件夹中拥有一个包含多个package.json文件的应用程序。 从根目录访问这些脚本会很方便，而不是每次要运行脚本时导航到不同的文件夹，有两种方法可以执行此操作。
第一种是手动 cd 并运行对应的命令：
```
cd folder && npm start && cd ..
```
但还有一个更优雅的解决方案，即使用--prefix标志指定路径
```
npm start --prefix path/to/your/folder
```
下面是一个工作应用程序中此解决方案的示例，我们希望在前端(在客户机目录中)和后端(在服务器目录中)运行 npm start。

```
"start": "concurrently \"(npm start --prefix client)\" \"(npm start --prefix server)\"",
```
### 6. 延迟运行脚本直到端口准备就绪
通常，在开发全堆栈应用程序期间，咱们可能希望同时启动服务器和客户端。wait-on 节点模块提供了一种方便的方法来确保进程只在某些进程就绪时发生:在我们的例子中，我们有一个特定的端口。
例如，这是我在使用React前端的Electron项目中使用的dev脚本。 同时使用，脚本并行加载表示层和Electron窗口。 但是，使用wait-on，只有在 http://localhost:3000 启动好，才会打开Electron窗口。
```
"dev": "concurrently \"cross-env BROWSER=none npm run start\" \"wait-on http://localhost:3000 && electron .\"",
```
此外，React默认情况下会打开一个浏览器窗口，但对于 Electron 开发来说，这是不必要的。咱们可以通过传递环境变量BROWSER=none来禁用此行为
### 7. 列出并选择可用脚本
列出package.json文件中可用的脚本很简单：只需转到项目的根目录并在终端中输入npm run。
但是有一种更方便的方法可以获得脚本列表，可以立即运行该列表:为此，全局安装 NTL (npm任务列表)模块:
```
npm i -g ntl
```
然后在项目文件夹中运行ntl命令，可以获得一个可用脚本列表，并可以选择其中一个运行。

### 8. 运行前后脚本
你可能熟悉prebuild和postbuild这样的脚本，它们允许你定义在构建脚本之前或之后运行的代码。但事实上，pre和post可以在任何脚本之前添加，包括自定义脚本。
这不仅使你的代码更干净，而且还允许你单独运行pre和post脚本
### 9. 控制应用程序版本
与手动更改应用程序的版本相比，npm 提供了一些有用的快捷方式来完成这一点。 要增加版本，请在运行 npm version加上major，minor或patch`

```
// 1.0.0
npm version patch
// 1.0.1
npm version minor
// 1.1.0
npm version major
// 2.0.0
```
根据更新应用程序的频率，可以通过在每次部署时增加版本号来节省时间，使用以下脚本:

```
{
  "predeploy": "npm version patch"
}
```
### 10. 从命令行编辑 package.json

package.json是一个常规的json文件，因此可以使用工具库json从命令行进行编辑。 这在修改package.json提供另外一种新的方式，允许w你q创建超出默认值的快捷方式。 全局安装：

```
npm install -g json
```
然后，可以使用它来使用-I进行就地编辑。 例如，要添加值为“bar”的新脚本“foo”，这样写
```
json -I -f package.json -e 'this.scripts.foo="bar"'
```
### 11. 自动设置和打开你的github库

如果package.json文件中有“repository”，则可以通过输入 npm repo在默认浏览器中打开它。
如果你的项目已经连接到远程存储库，并且已经在命令行上安装了git，那您可以使用这个命令找到你的连接存储库
```
git config --get remote.origin.url
```
更好的是，如果你按照上面的提示并安装了json模块，可以使用下面的脚本自动将正确的存储库添加到 package.json
```
json -I -f package.json -e "this.repository=\"$(git config --get remote.origin.url)\""
```
### 13. 使用自定义npm init脚本将你的第一个 Commit 提交到 GitHub

为了将git命令合并到.npm-init.js文件中，需要一种方法来控制命令行。为此，我们可以使用child_process 模块。在文件的顶部引入它，因为我们只需要execSync函数，所以可以使用析构赋值语法自己获取它：
```
const { execSync } = require('child_process');
```
我还创建了一个helper函数，它将函数的结果打印到控制台：
```
function run(func) {
  console.log(execSync(func).toString())
}
```
最后，我们将提示输入GitHub存储库URL，如果提供，我们将生README.md文件，并启动我们的第一次提交。
```
repository: prompt('github repository url', '', function (url) {
  if (url) {
    run('touch README.md');
    run('git init');
    run('git add README.md');
    run('git commit -m "first commit"');
    run(`git remote add origin ${url}`);
    run('git push -u origin master');
  }
  return url;
})
```
总的来说，.npm-init.js文件大概如下：
```
const { execSync } = require('child_process');

function run(func) {
  console.log(execSync(func).toString())
}

module.exports = {
  name: prompt('package name', basename || package.name),
  version: prompt('version', '0.0.0'),
  decription: prompt('description', ''),
  main: prompt('entry point', 'index.js'),
  keywords: prompt(function (s) { return s.split(/\s+/) }),
  author: prompt('author', 'Joe Bloggs <joe.bloggs@gmail.com> (joebloggs.com)'),
  license: prompt('license', 'ISC'),
  repository: prompt('github repository url', '', function (url) {
    if (url) {
      run('touch README.md');
      run('git init');
      run('git add README.md');
      run('git commit -m "first commit"');
      run(`git remote add origin ${url}`);
      run('git push -u origin master');
    }
    return url;
  }),
}
```
package.json文件：
```
{
  "name": "Custom npm init",
  "version": "0.0.0",
  "decription": "A test project, to demonstrate a custom npm init script.",
  "main": "index.js",
  "keywords": [],
  "author": "Joe Bloggs <joe.bloggs@gmail.com> (joebloggs.com)",
  "license": "ISC",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/JoeBloggs/custom.git"
  },
  "bugs": {
    "url": "https://github.com/JoeBloggs/custom/issues"
  },
  "homepage": "https://github.com/JoeBloggs/custom#readme"
}
```
你也可以通过合并GitHub API进一步实现这一点，这样就不需要创建一个新的存储库，这部分留给你们自己完成。
总的来说，希望这篇文章能够让你了解npm可以实现的目标，并展示了一些提高工作效率的方法 - 无论是你知道常见的快捷方式，还是通过充分利用脚本 package.json，或编写自定义版本的npm init。
