---
layout: post
title: VUE3-29-渲染函数
date: 2021-08-02 21:01:55 +0800
categories: [VUE]
tags: [vue, vue3, vue-learn, vue3-learn, sh]
published: true
---

# 渲染函数

Vue 推荐在绝大多数情况下使用模板来创建你的 HTML。

然而在一些场景中，你真的需要 JavaScript 的完全编程的能力。这时你可以用渲染函数，它比模板更接近编译器。

让我们深入一个简单的例子，这个例子里 render 函数很实用。

假设我们要生成一些带锚点的标题：

```html
<h1>
  <a name="hello-world" href="#hello-world">
    Hello world!
  </a>
</h1>
```

锚点标题的使用非常频繁，我们应该创建一个组件：

```xml
<anchored-heading :level="1">Hello world!</anchored-heading>
```

当开始写一个只能通过 level prop 动态生成标题 (heading) 的组件时，我们很快就可以得出这样的结论：

```js
const app = Vue.createApp({})

app.component('anchored-heading', {
  template: `
    <h1 v-if="level === 1">
      <slot></slot>
    </h1>
    <h2 v-else-if="level === 2">
      <slot></slot>
    </h2>
    <h3 v-else-if="level === 3">
      <slot></slot>
    </h3>
    <h4 v-else-if="level === 4">
      <slot></slot>
    </h4>
    <h5 v-else-if="level === 5">
      <slot></slot>
    </h5>
    <h6 v-else-if="level === 6">
      <slot></slot>
    </h6>
  `,
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

这个模板感觉不太好。它不仅冗长，而且我们为每个级别标题重复书写了 `<slot></slot>`。

当我们添加锚元素时，我们必须在每个 v-if/v-else-if 分支中再次重复它。

虽然模板在大多数组件中都非常好用，但是显然在这里它就不合适了。

那么，我们来尝试使用 render 函数重写上面的例子：

```js
const app = Vue.createApp({})

app.component('anchored-heading', {
  render() {
    const { h } = Vue

    return h(
      'h' + this.level, // tag name
      {}, // props/attributes
      this.$slots.default() // array of children
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

render() 函数的实现要精简得多，但是需要非常熟悉组件的实例 property。

在这个例子中，你需要知道，向组件中传递不带 v-slot 指令的子节点时，比如 anchored-heading 中的 Hello world! ，这些子节点被存储在组件实例中的 $slots.default 中。

如果你还不了解，在深入渲染函数之前推荐阅读实例 property API。

# DOM 树

在深入渲染函数之前，了解一些浏览器的工作原理是很重要的。

以下面这段 HTML 为例：

```xml
<div>
  <h1>My title</h1>
  Some text content
  <!-- TODO: Add tagline -->
</div>
```

当浏览器读到这些代码时，它会建立一个 ”DOM 节点“ 树 来保持追踪所有内容，如同你会画一张家谱树来追踪家庭成员的发展一样。

每个元素都是一个节点。每段文字也是一个节点。甚至注释也都是节点。一个节点就是页面的一个部分。就像家谱树一样，每个节点都可以有孩子节点 (也就是说每个部分可以包含其它的一些部分)。

高效地更新所有这些节点会是比较困难的，不过所幸你不必手动完成这个工作。

你只需要告诉 Vue 你希望页面上的 HTML 是什么，这可以是在一个模板里：

```xml
<h1>${ blogTitle }</h1>
```

或者一个渲染函数里：

```js
render() {
  return Vue.h('h1', {}, this.blogTitle)
}
```

在这两种情况下，Vue 都会自动保持页面的更新，即便 blogTitle 发生了改变。

# 虚拟 DOM 树

Vue 通过建立一个虚拟 DOM 来追踪自己要如何改变真实 DOM。

请仔细看这行代码：

```js
return Vue.h('h1', {}, this.blogTitle)
```

h() 到底会返回什么呢？

其实不是一个实际的 DOM 元素。

它更准确的名字可能是 createNodeDescription，因为它所包含的信息会告诉 Vue 页面上需要渲染什么样的节点，包括及其子节点的描述信息。

我们把这样的节点描述为“虚拟节点 (virtual node)”，也常简写它为 VNode。“虚拟 DOM”是我们对由 Vue 组件树建立起来的整个 VNode 树的称呼。

# h() 参数

h() 函数是一个用于创建 vnode 的实用程序。也许可以更准确地将其命名为 createVNode()，但由于频繁使用和简洁，它被称为 h() 。

它接受三个参数：

```js
// @returns {VNode}
h(
  // {String | Object | Function | null} tag
  // 一个 HTML 标签名、一个组件、一个异步组件，或者 null。
  // 使用 null 将会渲染一个注释。
  //
  // 必需的。
  'div',

  // {Object} props
  // 与 attribute、prop 和事件相对应的对象。
  // 我们会在模板中使用。
  //
  // 可选的。
  {},

  // {String | Array | Object} children
  // 子 VNodes, 使用 `h()` 构建,
  // 或使用字符串获取 "文本 Vnode" 或者
  // 有 slot 的对象。
  //
  // 可选的。
  [
    'Some text comes first.',
    h('h1', 'A headline'),
    h(MyComponent, {
      someProp: 'foobar'
    })
  ]
)
```

# 完整实例

有了这些知识，我们现在可以完成我们最开始想实现的组件：

```js
const app = Vue.createApp({})

/** Recursively get text from children nodes */
function getChildrenTextContent(children) {
  return children
    .map(node => {
      return typeof node.children === 'string'
        ? node.children
        : Array.isArray(node.children)
        ? getChildrenTextContent(node.children)
        : ''
    })
    .join('')
}

app.component('anchored-heading', {
  render() {
    // create kebab-case id from the text contents of the children
    const headingId = getChildrenTextContent(this.$slots.default())
      .toLowerCase()
      .replace(/\W+/g, '-') // replace non-word characters with dash
      .replace(/(^-|-$)/g, '') // remove leading and trailing dashes

    return Vue.h('h' + this.level, [
      Vue.h(
        'a',
        {
          name: headingId,
          href: '#' + headingId
        },
        this.$slots.default()
      )
    ])
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

# 约束

## VNodes 必须唯一

组件树中的所有 VNode 必须是唯一的。这意味着，下面的渲染函数是不合法的：

```js
render() {
  const myParagraphVNode = Vue.h('p', 'hi')
  return Vue.h('div', [
    // 错误 - 重复的Vnode!
    myParagraphVNode, myParagraphVNode
  ])
}
```

如果你真的需要重复很多次的元素/组件，你可以使用工厂函数来实现。例如，下面这渲染函数用完全合法的方式渲染了 20 个相同的段落：

```js
render() {
  return Vue.h('div',
    Array.apply(null, { length: 20 }).map(() => {
      return Vue.h('p', 'hi')
    })
  )
}
```

# 使用 JavaScript 代替模板功能

## v-if 和 v-for

只要在原生的 JavaScript 中可以轻松完成的操作，Vue 的渲染函数就不会提供专有的替代方法。比如，在模板中使用的 v-if 和 v-for：

```xml
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>No items found.</p>
```

这些都可以在渲染函数中用 JavaScript 的 if/else 和 map() 来重写：

```js
props: ['items'],
render() {
  if (this.items.length) {
    return Vue.h('ul', this.items.map((item) => {
      return Vue.h('li', item.name)
    }))
  } else {
    return Vue.h('p', 'No items found.')
  }
}
```

## v-model

v-model 指令扩展为 modelValue 和 onUpdate:modelValue 在模板编译过程中，我们必须自己提供这些prop：

```js
props: ['modelValue'],
render() {
  return Vue.h(SomeComponent, {
    modelValue: this.modelValue,
    'onUpdate:modelValue': value => this.$emit('update:modelValue', value)
  })
}
```

## v-on

我们必须为事件处理程序提供一个正确的prop名称，例如，要处理 click 事件，prop名称应该是 onClick。

```js
render() {
  return Vue.h('div', {
    onClick: $event => console.log('clicked', $event.target)
  })
}
```

## 事件修饰符

对于 .passive 、 .capture和 .once 事件修饰符，Vue提供了处理程序的对象语法：

实例:

```js
render() {
  return Vue.h('input', {
    onClick: {
      handler: this.doThisInCapturingMode,
      capture: true
    },
    onKeyUp: {
      handler: this.doThisOnce,
      once: true
    },
    onMouseOver: {
      handler: this.doThisOnceInCapturingMode,
      once: true,
      capture: true
    },
  })
}
```

对于所有其它的修饰符，私有前缀都不是必须的，因为你可以在事件处理函数中使用事件方法：

| 修饰符	|  处理函数中的等价操作 | 
|:---|:---|
| .stop	       |         event.stopPropagation() |
| .prevent	    |        event.preventDefault() |
| .self	        |        if (event.target !== event.currentTarget) return |
| 按键：.enter, .13	|    if (event.keyCode !== 13) return (对于别的按键修饰符来说，可将 13 改为另一个按键码 |
| 修饰键：.ctrl, .alt, .shift, .meta	| if (!event.ctrlKey) return (将 ctrlKey 分别修改为 altKey, shiftKey, 或 metaKey) |

这里是一个使用所有修饰符的例子：

```js
render() {
  return Vue.h('input', {
    onKeyUp: event => {
      // 如果触发事件的元素不是事件绑定的元素
      // 则返回
      if (event.target !== event.currentTarget) return
      // 如果向上键不是回车键，则中止
      // 没有同时按下按键 (13) 和 shift 键
      if (!event.shiftKey || event.keyCode !== 13) return
      // 停止事件传播
      event.stopPropagation()
      // 阻止该元素默认的 keyup 事件
      event.preventDefault()
      // ...
    }
  })
}
```

## 插槽

你可以通过 this.$slots 访问静态插槽的内容，每个插槽都是一个 VNode 数组：

```js
render() {
  // `<div><slot></slot></div>`
  return Vue.h('div', {}, this.$slots.default())
}
```

```js
props: ['message'],
render() {
  // `<div><slot :text="message"></slot></div>`
  return Vue.h('div', {}, this.$slots.default({
    text: this.message
  }))
}
```

要使用渲染函数将插槽传递给子组件，请执行以下操作：

```js
render() {
  // `<div><child v-slot="props"><span>{{ props.text }}</span></child></div>`
  return Vue.h('div', [
    Vue.h('child', {}, {
      // pass `slots` as the children object
      // in the form of { name: props => VNode | Array<VNode> }
      default: (props) => Vue.h('span', props.text)
    })
  ])
}
```

# JSX

如果你写了很多渲染函数，可能会觉得下面这样的代码写起来很痛苦：

```js
Vue.h(
  'anchored-heading',
  {
    level: 1
  },
  [Vue.h('span', 'Hello'), ' world!']
)
```

特别是对应的模板如此简单的情况下：

```xml
<anchored-heading :level="1"> <span>Hello</span> world! </anchored-heading>
```

这就是为什么会有一个 Babel 插件，用于在 Vue 中使用 JSX 语法，它可以让我们回到更接近于模板的语法上。

```js
import AnchoredHeading from './AnchoredHeading.vue'

new Vue({
  el: '#demo',
  render() {
    return (
      <AnchoredHeading level={1}>
        <span>Hello</span> world!
      </AnchoredHeading>
    )
  }
})
```

有关 JSX 如何映射到 JavaScript 的更多信息，请参阅使用文档 

# 模板编译

你可能会有兴趣知道，Vue 的模板实际上被编译成了渲染函数。

这是一个实现细节，通常不需要关心。

但如果你想看看模板的功能具体是怎样被编译的，可能会发现会非常有意思。

下面是一个使用 Vue.compile 来实时编译模板字符串的简单示例：

# 参考资料

https://vue3js.cn/docs/zh/guide/render-function.html

* any list
{:toc}