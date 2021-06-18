---
title: Verdaccio + Docker搭建npm私有仓库
date: 2021-06-18 14:02:26
tags: 前端工程化
---
为托管公司内部组件，实现私有化，并且易于管理和更新，使用Verdaccio + Docker搭建npm私有仓库
## Docker 安装Verdacico
一、拉取 verdaccio 的 docker 镜像
```
docker pull verdaccio/verdaccio
```
二、使用docker运行verdaccio
```
docker run -it -d --rm --name verdaccio -p 4873:4873 verdaccio/verdaccio
```
三、使用命令
```
// 添加用户 username passward email
npm adduser --registry localst:4873

// 登录
npm login --registry localst:4873

// 发布私有包
npm publish --registry localst:4873

// 设置npm注册表信息
npm set registry http://localhost:4873/

// 增加配置在.npmrc
registry=http://localhost:4873

// 增加配置在package.json
{
  "publishConfig": {
    "registry": "http://localhost:4873"
  }
}
```
