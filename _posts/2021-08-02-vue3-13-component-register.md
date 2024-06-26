---
layout: post
title: VUE3-13-组件注册
date: 2021-08-02 21:01:55 +0800
categories: [VUE]
tags: [vue, hello-world, vue-learn, sh]
published: true
---

# 组件名

在注册一个组件的时候，我们始终需要给它一个名字。

比如在全局注册的时候我们已经看到了：

```js
const app = Vue.createApp({...})

app.component('my-component-name', {
  /* ... */
})
```

该组件名就是 app.component 的第一个参数，在上面的例子中，组件的名称是“my-component-name”。

你给予组件的名字可能依赖于你打算拿它来做什么。当直接在 DOM 中使用一个组件 (而不是在字符串模板或单文件组件) 的时候，我们强烈推荐遵循 W3C 规范中的自定义组件名 (字母全小写且必须包含一个连字符)。这会帮助你避免和当前以及未来的 HTML 元素相冲突。

- 全部小写

- 包含连字符 (及：即有多个单词与连字符符号连接)

这样会帮助你避免与当前以及未来的 HTML 元素发生冲突。

你可以在风格指南中查阅到关于组件名的其它建议。

## 组件名大小写

在字符串模板或单个文件组件中定义组件时，定义组件名的方式有两种：

## 使用 kebab-case

```js
app.component('my-component-name', {
  /* ... */
})
```

当使用 kebab-case (短横线分隔命名) 定义一个组件时，你也必须在引用这个自定义元素时使用 kebab-case，例如 `<my-component-name>`。

## 使用 PascalCase

```js
app.component('MyComponentName', {
  /* ... */
})
```

当使用 PascalCase (首字母大写命名) 定义一个组件时，你在引用这个自定义元素时两种命名法都可以使用。

也就是说 `<my-component-name>` 和 `<MyComponentName>` 都是可接受的。

注意，尽管如此，直接在 DOM (即非字符串的模板) 中使用时只有 kebab-case 是有效的。

# 全局注册

到目前为止，我们只用过 app.component 来创建组件：

```js
Vue.createApp({...}).component('my-component-name', {
  // ... 选项 ...
})
```

这些组件是全局注册的。也就是说它们在注册之后可以用在任何新创建的组件实例的模板中。

比如：

```js
const app = Vue.createApp({})

app.component('component-a', {
  /* ... */
})
app.component('component-b', {
  /* ... */
})
app.component('component-c', {
  /* ... */
})

app.mount('#app')
```

```xml
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

在所有子组件中也是如此，也就是说这三个组件在各自内部也都可以相互使用。

# 局部注册

全局注册往往是不够理想的。

比如，如果你使用一个像 webpack 这样的构建系统，全局注册所有的组件意味着即便你已经不再使用一个组件了，它仍然会被包含在你最终的构建结果中。

这造成了用户下载的 JavaScript 的无谓的增加。

在这些情况下，你可以通过一个普通的 JavaScript 对象来定义组件：

```js
const ComponentA = {
  /* ... */
}
const ComponentB = {
  /* ... */
}
const ComponentC = {
  /* ... */
}
```

然后在 components 选项中定义你想要使用的组件：

```js
const app = Vue.createApp({
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

对于 components 对象中的每个 property 来说，其 property 名就是自定义元素的名字，其 property 值就是这个组件的选项对象。

注意局部注册的组件在其子组件中不可用。

例如，如果你希望 ComponentA 在 ComponentB 中可用，则你需要这样写：

```js
const ComponentA = {
  /* ... */
}

const ComponentB = {
  components: {
    'component-a': ComponentA
  }
  // ...
}
```

或者如果你通过 Babel 和 webpack 使用 ES2015 模块，那么代码看起来更像：

```js
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  }
  // ...
}
```

注意在 ES2015+ 中，在对象中放一个类似 ComponentA 的变量名其实是 ComponentA：ComponentA 的缩写，即这个变量名同时是：

- 用在模板中的自定义元素的名称

- 包含了这个组件选项的变量名

# 模块系统

如果你没有通过 import/require 使用一个模块系统，也许可以暂且跳过这个章节。

如果你使用了，那么我们会为你提供一些特殊的使用说明和注意事项。

## 在模块系统中局部注册

如果你还在阅读，说明你使用了诸如 Babel 和 webpack 的模块系统。

在这些情况下，我们推荐创建一个 components 目录，并将每个组件放置在其各自的文件中。

然后你需要在局部注册之前导入每个你想使用的组件。

例如，在一个假设的 ComponentB.js 或 ComponentB.vue 文件中：

```js
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  }
  // ...
}
```

现在 ComponentA 和 ComponentC 都可以在 ComponentB 的模板中使用了。


# 参考资料

https://vue3js.cn/docs/zh/guide/component-registration.html

* any list
{:toc}