---
layout: post
title: Programming language theory 编程语言理论-05-curring 柯里化
date:  2020-6-5 17:42:59 +0800
categories: [Theory]
tags: [programming-language-theory, sh]
published: true
---

# 编程语言理论系列

[Programming language theory 编程语言理论-01-overview 概览](https://houbb.github.io/2020/06/05/programming-language-theory-01-overivew)

[Programming language theory 编程语言理论-02-求值策略 Evaluation strategy](https://houbb.github.io/2020/06/05/programming-language-theory-02-evaluation-strategy)

[Programming language theory 编程语言理论-03-及早求值（英语：Eager evaluation）又译热切求值，也被称为贪婪求值（Greedy evaluation）](https://houbb.github.io/2020/06/05/programming-language-theory-03-eager-evaluation)

[Programming language theory 编程语言理论-03-惰性求值 Lazy Evaluation](https://houbb.github.io/2020/06/05/programming-language-theory-03-lazy-evaluation)

[Programming language theory 编程语言理论-03-短路求值（Short-circuit evaluation; minimal evaluation; McCarthy evaluation; 又称最小化求值）](https://houbb.github.io/2020/06/05/programming-language-theory-03-min-evaluation)

[Programming language theory 编程语言理论-04-组合子逻辑](https://houbb.github.io/2020/06/05/programming-language-theory-04-combine)

[Programming language theory 编程语言理论-05-curring 柯里化](https://houbb.github.io/2020/06/05/programming-language-theory-05-curring)

[Programming language theory 编程语言理论-06-λ演算（英语：lambda calculus，λ-calculus）](https://houbb.github.io/2020/06/05/programming-language-theory-06-lambda-calculus)

# Currying

在计算机科学中，柯里化（英语：Currying），又译为卡瑞化或加里化，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。这个技术由克里斯托弗·斯特雷奇以逻辑学家哈斯凯尔·加里命名的，尽管它是Moses Schönfinkel和戈特洛布·弗雷格发明的。

在直觉上，柯里化声称“如果你固定某些参数，你将得到接受余下参数的一个函数”。所以对于有两个变量的函数 y^x，如果固定了y=2，则得到有一个变量的函数2^x。

在理论计算机科学中，柯里化提供了在简单的理论模型中，比如：只接受一个单一参数的lambda演算中，研究带有多个参数的函数的方式。

函数柯里化的对偶是Uncurrying，一种使用匿名单参数函数来实现多参数函数的方法。

例如：

```js
var foo = function(a) {
  return function(b) {
    return a * a + b * b;
  }
}
```

这样调用上述函数：(foo(3))(4)，或直接foo(3)(4)。

# 动机

柯里化是一种处理函数中附有多个参数的方法，并在只允许单一参数的框架中使用这些函数。

例如，一些分析技术只能用于具有单一参数的函数。现实中的函数往往有更多的参数。

弗雷格表明，**为单一参数情况提供解决方案已经足够了，因为可以将具有多个参数的函数转换为一个单参数的函数链。这种转变是现在被称为“柯里化”的过程**。

在数学分析或计算机编程中，所有可能遇到的“普通”函数都可以被使用。

但是，有些类别不可能使用柯里化；确实允许柯里化的最普通的类别是闭合的monoidal类别。一些编程语言几乎总是使用curried函数来实现多个参数；值得注意的例子是 ML 和 Haskell，在这两种情况下，所有函数都只有一个参数。这个属性是从lambda演算继承而来的，其中多参数的函数通常以柯里形式表示。

柯里化与部分求值是相关的，但不完全相同。在实作中，闭包的编程技术可以用来执行部分求值和一种卷曲，通过将参数隐藏在使用柯里化函数的环境中。


# 参考资料

https://zh.wikipedia.org/wiki/%E6%9F%AF%E9%87%8C%E5%8C%96

* any list
{:toc}