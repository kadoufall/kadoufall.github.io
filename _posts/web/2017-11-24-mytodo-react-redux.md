---
title: 基于React+React-router+Redux的Todo+留言Demo
date: 2017-11-24 11:02:39
tags:
    - JavaScript
    - React
    - Redux
---

<!-- more -->

## 对React的一些理解
- 我理解的`React`是一个UI框架，也就是仅仅是`MVVM`框架的`V`。但是不同于`MVVM`中`View`和`ViewModel`的双向绑定，React这里是单向响应的数据流。React只负责根据拿到的数据各种组件如何呈现、如何组合以显示。而不需要像在`MVVM`中一样，因为双向绑定，`View`和`ViewModel`的联系很紧密，任何一边的改动意味着另一边也需要进行变动，这样代码的维护可能就比较困难
- 而既然是单向的数据流，那么每一次数据的变更都得重新刷新整个界面，如果是直接刷新DOM，那么肯定会很慢，所以React引入了**虚拟DOM**。虚拟DOM的大概实现我的理解是，第一次接受到数据的时候，React会根据数据用JS对象模拟DOM树，然后比照这个对象生成实际的DOM。而第二次数据来的时候，就构造一个新的对象，然后用diffing算法比较两者的区别，这个算法有点复杂。。然后根据这个区别去更新实际DOM中需要更新的部分。这样就最大限度减少了对DOM的实际操作。
- React的基本语法还是比较简单的，声明式设计
- React的组件有**属性**和**状态**。属性是不可改变的，而状态是可以改变的，状态的改变会引起重新渲染
- React使用到了**JSX**，这是一种以类XML句法创建JS对象，这样代码变得十分易读。在ES6中有实现模版字面量与JSX有相同之处，但是前者的代码并不是那么易读。JSX需要使用Babel等转换器以转为正常的JS。这个转变大概是，转换器在简单地扫描JSX，用JS函数替换HTML标签
- React没有设计代码结构和组件之间的通信，所以得和其他框架或者库一起用

## React Router
- React Router是一个路由库，单页面应用都得需要路由吧。这个库通过管理 URL，实现组件的切换和状态的变化

## Flux
- 针对单向数据流有一个`Flux`架构，这个架构是针对数据单向流动设计的
- 这个架构中，一个应用分为4个部分 
  - `View`： 视图层
  - `Action`（动作）：视图层发出的消息（比如`mouseClick`）
  - `Dispatcher`（派发器）：用来接收`Actions`、执行回调函数
  - `Store`（数据层）：用来存放应用的状态，一旦发生变动，就提醒`Views`要更新页面
- 这里用户访问`View`，通过`View`的用户接口触发`Action`，`Action`被发送到`Dispatcher`进行处理,处理后将更新通知发送给`Store`，`Store`收到后发出一个`"change"`事件给`View`，`View`即刷新界面

## Redux
- Redux可以说是`Flux`架构的一种实现
- 特点在于 - 单一数据源：整个Web 应用是一个状态机，视图与状态是一一对应的。所有的状态，保存在一个对象树里面。所有的对象树只存在于唯一一个`Store`中 
  - `state`是只读的：惟一改变 state 的方法就是触发 action。视图和网络请求都不能直接修改 `state`，相反它们只能表达想要修改的意图，都只能触发`action` 
  - 没有 `Dispatcher`，使用`reducers`。`reducer`是纯函数，表示为`(previousState, action) => newState`。每个`reducer`维护状态树的某一部分，多个组成了根`reducer`，根`reducer`就负责维护整个视图。
- 执行流程 - 定义所有可能的`action`和`reducer` - 无论是后台数据发来还是`View`的用户接口事件被触发，都会发出一个`action`，`store`调用某个特定的`reducer`去处理该`action`。`reducer`在处理后返回一个新的`state`，然后`store`将这个新的`state`返回给`view`
- 和`React`结合：只需要使用指定的 `React Redux` 组件` <Provider>`，这样所有的组件都可以访问 `Redux store`。结合后，`react`的组件分为了展示组件和容器组件，展示组件只负责UI呈现，容器组件则要负责数据获取和状态更新等
- 问题：这里我个人感觉比较麻烦的是，对于几乎所有的展示组件，都得再写一个容器组件来处理数据。这很繁琐。而且一些如表单这种耦合度很高的组件，就得混用展示和容器组件了。这就导致在定义新组件的时候很困惑。如果遇到TodoList包着Todo，我对一个Todo进行了操作，action得一层一层向上冒泡，先到TodoList展示组件，再到VisibleTodoList容器组件，很麻烦