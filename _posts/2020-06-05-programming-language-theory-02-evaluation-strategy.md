---
layout: post
title: Programming language theory 编程语言理论-02-求值策略 Evaluation strategy
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

# 求值策略

在计算机科学中，求值策略（英语：Evaluation strategy）是确定编程语言中表达式的求值的一组（通常确定性的）规则。

重点典型的位于函数或算子上——求值策略定义何时和以何种次序求值给函数的实际参数，什么时候把它们代换入函数，和代换以何种形式发生。

经常使用用来研究函数的形式系统λ演算来建模求值策略，这里它们通常叫做归约策略。

求值策略分为两大基本类，严格的和非严格的，基于如何处理给函数的实际参数。一个语言可以组合多种求值策略；例如C++组合了传值调用和传引用调用。多数语言对布尔表达式和if语句使用某种形式的非严格求值。

# 严格求值

在“严格求值”（Strict evaluation）中，给函数的实际参数总是在应用这个函数之前求值。

在邱奇编码下，算子的热情求值映射到了函数的严格求值；为此严格求值有时叫做“热情求值”。

多数现存编程语言对函数使用严格求值。

## 应用次序

“应用次序”（Applicative order）（或“最左最内”）求值称呼函数的实际参数按可归约表达式的后序遍历从左至右的求值的策略。

不像传值调用，应用次序求值尽可能的在应用函数之前归约函数体内的项。

## 传值调用

“传值调用”（Call by value）求值是最常见的求值策略，C和Scheme这样差异巨大的语言都在使用。

在传值调用中实际参数被求值，其值被绑定到函数中对应的变量上（通常是把值复制到新内存区域）。如果函数或过程能把值赋给它的形式参数，则被赋值的只是局部拷贝——就是说，在函数返回后调用者作用域里的曾传给函数的任何东西都不会变。

传值调用不是一个单一的求值策略，而是指一类函数的实参在被传给函数之前就被求值的求值策略。尽管很多使用传值调用的编程语言（如Common Lisp、Eiffel、Java）从左至右的求值函数的实际参数，某些语言（比如OCaml）从右至左的求值函数和它们的实际参数，而另一些语言（比如Scheme和C）未指定这种次序（尽管它们保证顺序一致性）。

## 传引用调用

在“传引用调用”（Call by reference）求值中，传递给函数的是它的实际参数的隐式引用而不是实参的拷贝。通常函数能够修改这些参数（比如赋值），而且改变对于调用者是可见的。因此传引用调用提供了一种调用者和函数交换数据的方法。传引用调用的语言中追踪函数调用的副作用比较难，易产生不易察觉的bug。

很多语言支持某种形式的传引用调用，但是很少有语言默认使用它。FORTRAN II 是一种早期的传引用调用语言。一些语言如C++、PHP、Visual Basic .NET、C#和REALbasic默认使用传值调用，但是提供一种传引用的特别语法。

在那些使用传值调用又不支持传引用调用的语言里，可以用引用（引用其他对象的对象），比如指针（表示其他对象的内存地址的对象）来模拟。C和ML就用了这种方法。这不是一种不同的求值策略（语言本身还是传值调用）。它有时被叫做“传地址调用”（call by address）。这可能让人不易理解。在C之类不安全的语言里会引发解引用空指针之类的错误。但ML的引用是类型安全和内存安全的。

类似的效果可由传共享对象调用（传递一个可变对象）实现。比如Python、Ruby。

例：C用指针模拟的传引用调用

```c
void modify(int p, int* q, int* r) {
    p = 27; // passed by value: only the local parameter is modified
    *q = 27; // passed by value or reference, check call site to determine which
    *r = 27; // passed by value or reference, check call site to determine which
}

int main() {
    int a = 1;
    int b = 1;
    int x = 1;
    int* c = &x;
    modify(a, &b, c); // a is passed by value, b is passed by reference by creating a pointer,
                    // c is a pointer passed by value
                    // b and x are changed
    return 0;
}
```

## 传共享对象调用

传共享对象调用（Call by sharing）的方式由Barbara Liskov命名[1]，并被Python、Java（对象类型）、JavaScript、Scheme、OCaml等语言使用。

与传引用调用不同，对于调用者而言在被调用函数里修改参数是没有影响的。如果要达成传引用调用的效果就需要传一个共享对象，一旦被调用者修改了对象，调用者就可以看到变化（因为对象是共享的，没有拷贝）。

比如这段Python代码：

```python
def f(l):
    l.append(1)
    l = [2]
m = []
f(m)
print(m)
```

会输出[1]而不是[2]。因为列表是可变的，append方法改变了m。而赋值局部变量l的行为对外面作用域没有影响（在这类语言中赋值是给变量绑定一个新对象，而不是改变对象）。

使用C/C++语言的程序员可能因不能用指针等使函数返回多个值而感到不便，但是像Python这样的语言提供了替代方案：函数能方便的返回多个值，比C++11的std::tie更加简单。

## 传复件-恢复调用

“传复件-恢复调用”（Call by copy-restore）、“传值-结果调用”或“传值-返回调用”（在Fortran社区中的术语）是传引用调用的特殊情况，即在传引用调用时，向被叫进程所传递的引用并非调用进程原有的引用，而是一个原有引用的复制，即被传递的引用与调用进程没有关系。传复件-恢复调用在这种情况下很重要：如果函数调用的一个形式参数，是可能被其他执行线程同时访问的引用。那么就把这个引用的内容复制到一个新创建的引用中，再将这个新创建的、与调用进程无关的引用传递给被叫进程。当被叫进程执行结束、调用返回的时候，再把这个新引用中更新过的内容复制回调叫进程原来的引用中（“恢复”）。

传值-返回调用的语义在两个或更多实际参数相互是别名的时候也不同于传引用调用，就是说它们都指向了在调用者环境中的同一个变量。在传引用调用下，写其中一个会影响另一个；传值-返回调用通过给函数以独自的复件来避免了这种情况，但没有规定在调用者环境中的结果(依赖于哪个别名实际参数首先被复制回去)。

当引用未初始化就传递给被调用者的时候，这种求值策略可以叫“传结果调用”。

## 部分求值

更多信息：柯里化

在“部分求值”（Partial evaluation）中，求值可以延续到仍未被应用的函数体之内。求值不包含未绑定变量的任何子表达式，并且归约其实际参数值是已知的函数应用。

在有副作用存在的时候，完全部分求值可能产生未预期的结果，支持部分求值的系统趋向只把它用于函数内“纯”表达式(没有副作用的表达式)。


# 非严格求值

在“非严格求值”（Non-strict evaluation）中，不求值给函数的实际参数，除非它们在函数体内实际上被用到了。

在邱奇编码下，算子的惰性求值映射到了函数的非严格求值；为此，非严格求值有时也叫做“惰性求值”。布尔表达式在很多语言中使用惰性求值；在这种上下文中它通常叫做短路求值。条件表达式也通常使用惰性求值，但出于不同的原因。

## 正常次序

“正常次序”（Normal order）（或“最左最外”）求值是总是归约的最外可归约式，在求值函数的实际参数之前应用函数的求值策略。它不同于传名调用，传名调用不进入未应用的函数体内求值。

## 传名调用

在“传名调用”（Call by name）求值中，根本就不求值给函数的实际参数——而是使用避免捕获代换把函数的实际参数直接代换入函数体内。如果实际参数在函数的求值中未被用到，则它永不被求值；如果这个实际参数使用多次，则它每次都被重新求值。（参见Jensen设备。）

传名调用求值超过传值调用求值的优点是传名调用求值在一个值存在的时候总是生成这个值，而传名调用可能不终止如果这个函数的实际参数是求值这个函数所不需要的不终止计算。反过来说，在函数的实际参数会用到的时候传名调用就非常慢了，这是因为实践中几乎总是要使用如thunk这样的机制。

传名调用求值很少直接实现，但是经常用于程序和编程语言的理论性质的思考中。带有传名调用语义的现实世界中的语言趋向使用传需求调用求值。传名调用是ALGOL 60中的缺省求值。

## 传需求调用

“传需求调用”（Call by need）是传名调用的记忆化版本，如果“函数的实际参数被求值了”，这个值被存储起来已备后续使用。在“纯”(无副作用)设置下，这产生同传名调用一样的结果；当函数实际参数被使用两次或更多次的时候，传需求调用总是更快。

因为表达式的求值可能出现在计算内任意远的地方，使用传需求调用的语言一般不支持计算效果（比如mutation）除非通过使用单子。这消除了其值变更先于它们的延迟求值的变量的任何未预期行为。

Haskell是最周知的使用传需求调用求值的语言。

## 传宏展开调用

“传宏展开调用”（Call by macro expansion）类似于传名调用，但是使用了文本代换而不是避免捕获代换。如果不小心的使用，宏代换可能导致变量捕获并导致不希望的行为。卫生宏通过检查并替换不是形式参数的阴影变量避免了这个问题。

# 非确定性策略

非确定性策略（Nondeterministic strategies）包括：

## 完全β归约

在“完全β归约”（Full β-reduction）下，任何函数应用都可以在任何时候归约（是避免捕获代换把函数的实际参数代换如函数内）。这甚至可以在未应用的函数体内进行。

## 传预期调用

“传预期调用”（Call by future）（或“并行传名调用”）类似于传名调用，除了这个函数的实际参数的求值可能并行于函数体的求值（而非只在用到的时候）。两个执行线程在函数体的求值中需要这个实际参数的时候同步；如果这个实际参数永不用到，实际参数的线程可以杀死。

## 最优求值

“最优求值”（Optimistic evaluation）是传需求调用的另一个变体，在其中函数的实际参数部分的求值一段时间（这可在运行时间调整），此后求值退出使用传需求调用应用函数。这种方式避免了传需求调用的某些运行时间代价，而仍保持了想要的终止特征。


# 参考资料

wiki

* any list
{:toc}