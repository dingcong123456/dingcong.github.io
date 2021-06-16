---
title: ESLint+Prettier代码规范实践
date: 2020-03-16 16:37:55
tags: 前端工程化
---
## ESLint&Prettier
#### ESLint
ESlint 主要对代码格式和代码质量进行检验。早前ESlint只提供warning报告，现在 ESLint 提供了 $ ESLint filename --fix 的命令自动修复部分不符合规范的代码
#### Prettier
prettier 自动对代码格式进行格式化，prettier 已经逐渐成为业界主流的代码风格格式化工具

代码格式问题通常指的是：单行代码长度、tab长度、空格、逗号表达式等问题。而代码质量问题指的是：未使用变量、三等号、全局变量声明等问题。

#### ESLint 和 Prettier的配合使用
**Q:ESlint 有了fix命令 为什么还要配合使用Prettier？**
A: ESLint 的规则并不能完全包含 Prettier 的规则,ESlint 主要负责代码规则校验，Prettier 负责格式化代码风格，代码样式，两者分工不同，需要配合使用。

ESLint 和 Prettier 相互合作的时候有一些问题，对于他们交集的部分规则，ESLint 和 Prettier 格式化后的代码格式不一致。导致的问题是：当你用 Prettier 格式化代码后再用 ESLint 去检测，会出现一些因为格式化导致的 warning。

**Q: 怎么解决？**
A: 在配置 ESLint 的校验规则时候把和 Prettier 冲突的规则 disable 掉，然后再使用 Prettier 的规则作为校验规则。那么使用 Prettier 格式化后，使用 ESLint 校验就不会出现对前者的 warning。

**Q: 怎么设置disable掉和Prettier冲突的ESLint规则？**

一、 安装 **eslint-config-prettier** 插件
```
npm install --save-dev eslint-config-prettier
```
二、 配置.eslintrc.* 文件
```
{
    "extends": [
        ...,
        "已经配置的规则",
    +   "prettier"
    ]
}
 ```
   三、完成上述两步可以实现的是运行 eslint 命令会按照 prettier 的规则做相关校验，但是还是需要分别运行 prettier 和 eslint 命令。社区有一个方案整合了上述两步，在使用 eslint --fix 时候，实际使用 prettier 来替代 eslint 的格式化功能。 安装：

 ```
  npm install --save-dev eslint-plugin-prettier
```
配置.eslintrc.* 文件

```
{
    "extends": [
        ...,
        "已经配置的规则",
        "plugin:prettier/recommended"
    ]
}
```
这个时候你运行 eslint --fix 实际使用的是 Prettier 去格式化文件

#### 使用husky lint-staged 强制校验和格式化
我们可以用 husky lint-staged 来在 git commit 前强制代码格式化和代码校验。
我们可以在执行 git commit 时 使用husky 和 lint-staged 对本地缓存区内的代码进行 lint校验

 ```
 npm install --save-dev husky lint-staged
 ```
 配置package.json
 ```
 {
  "name": "project-name",
  ...,
  + "husky": {
  +     "hooks": {
  +         "pre-commit": "lint-staged"
  +     }
  + },
  + "lint-staged": {
  +     "src/**/*.{js,jsx,ts,tsx,json,css,scss,md}": [
  +     "eslint --fix",
  +     "git add"
  +     ]
  + },
}
```

