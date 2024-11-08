# 设计模式

## state的DRY（不要自我重复）

- 尽量使state精简，减少state的数量，比如：如果页面需要渲染一个数组列表和列表的长度，只需要声明一个数组state，读取他的数组长度渲染到页面上，而不是重新定义一个数组长度state

**一些规则**

1. 随着时间推移**保持不变**的，不是state
2. 通过props**从父组件传递**的，不是state
3. 可以基于已有state或者props**计算**的，不是state

## state的存放位置

- react使用单向数据流，数据只从父组件传递到子组件，需要清楚state到底属于哪一个组件（该组件依赖这个state实现相应式）

**对于每一个state**

1. 找到依赖其更新的组件
1. 找出这些组件最近的父组件
3. 选择state的存放位置
   - 一般可以存放在共同父组件中
   - 如果没有有意义的父组件可以存放，可以创建一个组件专门用来存放state

## 纯粹的组件

- 在 React 中，你可以在渲染时读取三种输入：props，state和 context，应该始终将这些输入视为只读

- 当你想根据用户输入 *更改* 某些内容时，你应该 设置状态，而不是直接写入变量。当你的组件正在渲染时，你永远不应该改变预先存在的变量或对象

React 提供了 “严格模式”，在严格模式下开发时，它将会**调用每个组件函数两次**，**通过重复调用组件函数，严格模式有助于找到违反这些规则的组件**。

- 函数不纯粹，调用它两次就会出现问题。但对于纯函数，即使调用该函数两次也能得到正确结果。**纯函数仅仅执行计算，因此调用它们两次不会改变任何东西** 两次求解 y = 2x 不会改变 y 的值一样。相同的输入，总是返回相同的输出。

- 严格模式**在生产环境下不生效**，因此它不会降低应用程序的速度。如需引入严格模式，你可以用 `<React.StrictMode>` 包裹根组件。一些框架会默认这样做

## 如何理解副作用

- 在react的设计模式中，组件渲染函数应该是一个纯函数，即给定相同的props，一定返回相同的JSX，同时不会改变已经定义过的变量
- 所谓副作用，就是执行函数时改变了已经定义过的变量

## 理解Hook

- hook只对组件的渲染起作用，即他只能在组件的顶层作用域中调用
- hook是函数，但是应该把它理解为组件需求的无条件声明

## state如同一张快照

- state虽然在代码上来看是在组件函数里定义的，但实际上**state被react保存在组件之外**的

- 当state发生变化时，react会根据新的state重新执行对应的组件函数

![image-20240930163310596](https://p.ipic.vip/q0nvyg.png)









# 技巧

## 在列表渲染中返回多个dom节点

- 如果你想让每个列表项都输出多个 DOM 节点而非一个的话，该怎么做呢？

- Fragment 语法的简写形式 `<> </>` **无法接受 key 值**，所以你只能要么把生成的节点用一个 `<div>` 标签包裹起来，要么使用长一点但更明确的 `<Fragment>` 写法：

```react
import { Fragment } from 'react';

// ...

const listItems = people.map(person =>

  <Fragment key={person.id}>

    <h1>{person.name}</h1>

    <p>{person.bio}</p>

  </Fragment>

);
```

这里的 Fragment 标签本身并不会出现在 DOM 上，这串代码最终会转换成 `<h1>`、`<p>`、`<h1>`、`<p>`…… 的列表

## 触发捕获事件

- 如果想在时间的捕获阶段就出发对应的响应函数，除了原生的addEventLisenter可以在第三个参数中传入true，在react中也可以在响应函数后面加上**capture**

例如：

``````react
<div onClickCapture={()=>{alert('正在捕获阶段，我先被触发')}}>
	<button onClick={()=>{alert('正在触发阶段')}}>click me</button>
</div>
``````









# 注意事项

1. 为组件指定他的id时不要使用key传入，而应该单独设置一个props，如：id



# 积累

- useDebounceFn
  - ahooks库的一个钩子，用于为一个函数添加防抖