---
title: My-Todo(React&GraphQL&Apollo)
date: 2017-03-26 20:16:30
tags:
    - JavaScript
    - React
    - GraphQL
---

## **Goal：**
- 实现一个Todo
- 将Todo设置为一个类似于堆栈(FIFO)的结构，新的Todo被推到堆栈的顶部
- 需要能看到当前所有待办Todo
- 假如只会完成栈顶的Todo
- 封装GraphQL-API
- 做一些数据可视化

<!-- more -->

---

## **框架**
- 前端：源码：[mytodo-client](https://github.com/kadoufall/mytodo-client)
  - Todo demo的前台，基于[create-react-app](https://github.com/facebookincubator/create-react-app)
  - 使用[Apollo-client](https://github.com/apollographql/apollo-client)管理数据
  - 使用[d3act](https://github.com/AnSavvides/d3act)建图
 
- 后端----源码： [todo-server](https://github.com/kadoufall/todo-server)
  - Todo demo的后台，基于[apollo-tutorial-kit](https://github.com/apollographql/apollo-tutorial-kit)
  - 服务器为[graphql-server-express](http://dev.apollodata.com/tools/graphql-server/index.html)
  - 使用[graphql-tools](https://github.com/apollographql/graphql-tools)构建GraphQL模式和解析器
  - 数据库使用[sqlite](https://www.sqlite.org/)


## 前端
### React










