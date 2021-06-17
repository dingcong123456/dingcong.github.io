---
title: Commit提交规范校验配置
date: 2021-06-17 11:22:05
tags: 前端工程化
---
## Commit message 提交规范（Angular）
### 一、Commit message 的作用
格式化的Commit message，有几个好处。
1. 提供更多的历史信息，方便快速浏览。
2. 可以过滤某些commit（比如文档改动），便于快速查找信息。
3. 可以直接从commit生成Change log。

### 二、Commit message 的格式
每次提交，Commit message 都包括三个部分：Header，Body 和 Footer。
```
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>
```
其中，Header 是必需的，Body 和 Footer 可以省略。

不管是哪一个部分，任何一行都不得超过72个字符（或100个字符）。这是为了避免自动换行影响美观。
#### 2.1 Header
Header部分只有一行，包括三个字段：type（必需）、scope（可选）和subject（必需）。

**（1）type**
>* feat：新功能（feature）
>* fix：修补bug
>* docs：文档（documentation）
>* style： 格式（不影响代码运行的变动）
>* refactor：重构（即不是新增功能，也不是修改bug的代码变动）
>* test：增加测试
>* chore：构建过程或辅助工具的变动

如果type为feat和fix，则该 commit 将肯定出现在 Change log 之中。其他情况（docs、chore、style、refactor、test）由你决定，要不要放入 Change log，建议是不要。

**（2）scope**

scope用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

**（3）subject**

subject是 commit 目的的简短描述，不超过50个字符。
>* 以动词开头，使用第一人称现在时，比如change，而不是changed或changes
>* 第一个字母小写
>* 结尾不加句号（.）

## 配置 Commitizen 通过命令行交互生成规范的Commit message
### 一、安装工具和适配器(规范)
**commitizen/cz-cli**, 我们需要借助它提供的 **git cz** 命令替代我们的 git commit 命令, 帮助我们生成符合规范的 commit message.

除此之外, 我们还需要为 commitizen 指定一个 Adapter（适配器） 比如: **cz-conventional-changelog** (一个符合 Angular团队规范的 preset). 使得 commitizen 按照我们指定的规范帮助我们生成 commit message.

```
 npm install -g commitizen
 npm install -g cz-conventional-changelog
```
### 二、项目中配置使用
执行命令
```
commitizen init cz-conventional-changelog --save --save-exact
```

执行完毕，项目中的package.json文件，会多出一部分配置信息
```
{ 
  ... 
  "config": {
    "commitizen": {
      "path": "./node_modules/cz-conventional-changelog"
    }
}
```
### 三、使用git cz替代git commit
后面使用到git commit命令时，一律改为使用git cz。这时，就会出现选项，用来生成符合格式的 Commit message。

## 配置 commitlint&husky 在commit-msg钩子中进行message校验
### 一、安装配置commitlint
commitlint: 可以帮助我们 lint commit messages, 如果提交的不符合指向的规范, 拒绝提交
需要一份校验的配置, 这里推荐 @commitlint/config-conventional (符合 Angular团队规范).

```
npm install  @commitlint/config-conventional @commitlint/cli --save
```
接着在项目的根目录下新建commitlint.config.js文件，然后写入：
```
module.exports = {
  extends: ['@commitlint/config-conventional']
}
```
### 二、安装配置husky
commitlint配置完毕后需要配置 husky 在**commit-msg** 钩子中执行commitlint校验

安装husky
```
npm install husky@4.0.0 --save
```
建议安装4.x版本 6.x版本后配置规则改变，安装6.x版本请自行查找配置方案

在在package.json中配置
```
"husky": {
    "hooks": {
      "commit-msg": "commitlint -e $GIT_PARAMS"
    }
  }
```
这段配置的意思是：在’commit-msg’这个钩子内获取commit里的内容进行commitlint检测，如果果该钩子脚本以非零值退出，Git 将放弃提交

### 三、编写script脚本实现commit失败后提示
添加提示脚本再commit lint失败后进行提示

编写脚本
在当前根目录创建scripts文件夹，里面创建pre-commit.js文件
```
const chalk = require('chalk');
console.log(
  chalk.red(`commit格式错误，正确示例：git commit -m 'fix: 修复bug'`),
  chalk.green(` 
  type （只允许下列7个标识）：
  feat：新功能（feature）
  fix：修补bug
  docs：文档（documentation）
  style： 格式（不影响代码运行的变动）
  refactor：重构（即不是新增功能，也不是修改bug的代码变动）
  test：增加测试
  chore：构建过程或辅助工具的变动
  `)
)
```

修改husky的配置
```
  "husky": {
    "hooks": {
      "commit-msg": "commitlint -e $GIT_PARAMS || (node scripts/pre-commit.js&&exit 8)"
    }
  }
```
在commitlint -e $GIT_PARAMS 执行失败后 执行 node scripts/pre-commit.js&&exit 8 并退出
