---
layout: post
title: jdk 变更日志-21-jdk21
date:  2017-06-28 23:15:43 +0800
categories: [Java]
tags: [jdk, java]
published: true
---

# 拓展阅读

[Java Functional java 函数式编程](https://houbb.github.io/2017/06/29/java-functional)

[Java Lambda](https://houbb.github.io/2017/06/28/java-lambda)

# jdk21 有哪些新特性

JDK 21 引入了一系列新特性和改进，其中包括：

1. **序列集合（Sequenced Collections）**：JEP 431 提出了一个新的接口族，用于表示集合中元素的预定义序列或顺序。

2. **分代 ZGC（Generational ZGC）**：JEP 439 扩展了 Z 垃圾收集器（ZGC），为年轻对象和老对象维护独立的代，以提高应用程序性能。

3. **记录模式（Record Patterns）**：JEP 440 增强了 Java 编程语言，允许解构记录值，可以嵌套记录模式和类型模式。

4. **`switch` 表达式的模式匹配**：JEP 441 通过 `switch` 表达式和语句的模式匹配增强了 Java 编程语言。

5. **虚拟线程（Virtual Threads）**：JEP 444 引入了轻量级线程，旨在减少编写、维护和观察高吞吐量并发应用程序的工作量。

6. **弃用 Windows 32 位 x86 移植**：JEP 449 计划在未来版本中删除对 Windows 32 位 x86 的支持。

7. **准备禁止动态加载代理**：JEP 451 要求在代理动态加载到正在运行的 JVM 中时发出警告，以提高默认情况下的完整性。

8. **密钥封装机制 API**：JEP 452 介绍了一种用于密钥封装机制（KEM）的 API。

9. **字符串模板（String Templates）**：JEP 430 作为预览功能，提供了一种新的方式来动态构建字符串。

10. **外部函数和内存 API（第三次预览）**：JEP 442 允许 Java 程序与 Java 运行时之外的代码和数据进行互操作。

11. **未命名模式和变量（预览）**：JEP 443 允许在模式匹配中忽略某些不需要的值。

12. **未命名类和实例 `main` 方法（预览）**：JEP 445 允许在不定义类的情况下编写 `main` 方法。

13. **作用域值（预览）**：JEP 446 支持在线程内和线程间共享不可变数据。

14. **结构化并发（预览）**：JEP 453 通过结构化并发 API 简化了并发编程。

15. **向量 API（第六次孵化）**：JEP 448 允许以一种在运行时可靠地编译为支持的 CPU 架构上的向量指令的方式来表达向量计算。

这些新特性和改进展示了 Java 对现代编程需求的关注，预计在未来的版本中，Java 将进一步强化其并发编程能力，并为开发者提供更多高效、安全和可靠的工具。


# 详细介绍 jdk20 JDK 21 序列集合（Sequenced Collections）

JDK 21 引入了序列集合（Sequenced Collections），这是 Java 集合框架的一个重要补充。序列集合是一种特殊的集合，其中的元素具有一个明确的顺序，这个顺序是集合的一个结构属性。这个新特性由 JEP 431 提出，并作为正式特性被纳入 JDK 21。

以下是序列集合的一些关键点：

1. **定义顺序**：序列集合中的元素按照特定的顺序排列，这个顺序在集合创建时定义，并在集合的生命周期内保持不变。

2. **结构属性**：序列集合的顺序是其结构的一部分，这意味着顺序是集合类型的固有属性，而不是由外部因素（如迭代器的使用）决定的。

3. **统一操作集**：序列集合提供了统一的操作集，这些操作考虑了集合中元素的顺序，从而允许开发者以一种一致且可预测的方式来处理这些集合。

4. **核心接口**：序列集合引入了新的接口，如 `SequencedCollection`，这些接口定义了操作序列集合的标准方式。

5. **实际应用**：序列集合可以用于多种场景，包括但不限于数据流处理、事件序列处理以及任何需要保持元素顺序的集合使用场景。

6. **与现有集合的关系**：序列集合与 Java 中已有的集合类型（如 `List`）的关系是互补的。开发者可以根据需要选择使用传统的集合类型或序列集合。

7. **增强的表达能力**：序列集合的引入增强了 Java 集合框架的表达能力，允许开发者更精确地定义和使用有序集合。

8. **示例代码**：
   ```java
   SequencedCollection seqCol = ...;
   // 序列集合的具体使用将取决于其实现和提供的操作
   ```

9. **JDK 21 中的位置**：序列集合作为 JDK 21 的一部分，位于 `java.util` 包中，允许开发者直接在标准 Java 应用程序中使用。

序列集合的引入是 Java 集合框架在表达有序集合方面的一个重要进步。它为 Java 开发者提供了一种新的工具来处理需要保持元素顺序的场景，同时简化了代码的编写和维护。随着 JDK 的发展，序列集合有望成为 Java 标准库中不可或缺的一部分，从而帮助 Java 程序员更高效地创建和维护有序集合。

# 详细介绍 jdk20 JDK 21 分代 ZGC（Generational ZGC）

JDK 21 引入了分代 ZGC（Generational Z Garbage Collector），这是对现有的 Z Garbage Collector（ZGC）的一个扩展。分代 ZGC 旨在通过区分年轻代和老年代对象来进一步提升垃圾收集的性能和效率。

以下是分代 ZGC 的一些关键特性：

1. **年轻代和老年代的区分**：分代 ZGC 维护了年轻对象和老年代对象的独立代，使得垃圾收集器可以更频繁地收集那些生命周期短的年轻对象。

2. **性能提升**：通过更精确地收集年轻代对象，分代 ZGC 可以减少垃圾收集的暂停时间，从而提高应用程序的整体性能。

3. **低延迟**：ZGC 以其低延迟特性而闻名，分代 ZGC 进一步增强了这一特性，使得应用程序可以更加平滑地运行，尤其是在需要实时处理的场合。

4. **减少内存占用**：分代策略有助于减少内存中的碎片，并更有效地利用内存资源。

5. **与现有 ZGC 的兼容性**：分代 ZGC 保持了与现有 ZGC 的兼容性，允许开发者在现有的 ZGC 应用程序中无缝过渡到分代 ZGC。

6. **垃圾收集器的可配置性**：分代 ZGC 提供了多种配置选项，允许开发者根据应用程序的具体需求调整垃圾收集的行为。

7. **示例代码**：
   ```java
   // 启用分代 ZGC 可以通过 JVM 启动参数指定
   --XX:+UseZGC --XX:+UseGenerationalZGC
   ```

8. **JDK 21 中的位置**：分代 ZGC 作为 JDK 21 的一部分，可以通过 JVM 选项启用，成为垃圾收集策略的一部分。

9. **长期支持**：由于 JDK 21 是一个长期支持（LTS）版本，分代 ZGC 也预计将得到长期的支持和维护。

分代 ZGC 的引入是 Java 垃圾收集器在提高性能和资源利用率方面的一个重要步骤。

它为 Java 开发者提供了一种新的工具来优化内存管理，特别是在处理大规模数据和高并发应用程序时。

# 详细介绍 jdk20 JDK 21 记录模式（Record Patterns）

在 JDK 21 中，记录模式（Record Patterns）作为一个正式特性被引入，它是对 Java 编程语言的扩展，允许开发者使用模式匹配来解构记录（record）类型的值。这个特性在 Java 14 中作为预览特性首次提出，并在随后的版本中不断演化和改进。

以下是记录模式的一些关键点：

1. **解构记录**：记录模式允许开发者在访问记录时直接解构其组成部分，而不需要先将其分配给临时变量。

2. **增强可读性**：通过直接在 `instanceof` 或 `switch` 语句中使用记录模式，代码的可读性和意图表达更加清晰。

3. **与模式匹配结合**：记录模式与 Java 16 中引入的模式匹配特性（`instanceof` 模式匹配）相结合，提供了一种新的方式来处理对象实例。

4. **类型安全**：由于记录模式在编译时进行检查，因此它提供了类型安全，减少了运行时错误的可能性。

5. **示例代码**：
   ```java
   record Point(int x, int y) {}
   
   Object obj = ...;
   if (obj instanceof Point(int x, int y)) {
       System.out.println("Point coordinates: x = " + x + ", y = " + y);
   }
   ```
   在这个例子中，通过 `instanceof` 与记录模式结合，我们可以直接访问 `Point` 记录的 `x` 和 `y` 成员。

6. **与 `switch` 表达式结合**：
   ```java
   record Shape(String type, double area) {}
   
   String describe(Object obj) {
       return switch (obj) {
           case Shape("Circle", double area) -> "A circle with area " + area;
           case Shape("Rectangle", double area) -> "A rectangle with area " + area;
           default -> "An unknown shape";
       };
   }
   ```
   在这个例子中，`switch` 表达式使用了记录模式来匹配 `Shape` 记录的类型和面积。

7. **简化的语法**：记录模式的引入简化了处理记录类型时的语法，使得相关代码更加简洁。

8. **JDK 21 中的位置**：记录模式作为 JDK 21 的一部分，可以通过 Java 的 `instanceof` 表达式直接使用。

记录模式是 Java 语言在处理数据结构方面的一项重要补充，它使得处理记录类型更加直观和方便。

# 详细介绍 jdk20 JDK 21 `switch` 表达式的模式匹配

JDK 21 中的 `switch` 表达式模式匹配是一个强大的特性，它扩展了 `switch` 语句的能力，允许在 `switch` 表达式和语句中使用模式匹配。这个特性在 Java 14 首次作为预览特性引入，并在 JDK 17、JDK 18、JDK 19 和 JDK 20 中不断演进，最终在 JDK 21 中成为正式特性。

以下是 `switch` 表达式模式匹配的一些关键点：

1. **类型安全**：模式匹配在编译时进行类型检查，确保每个模式匹配的类型正确性，减少了运行时错误的可能性。

2. **灵活性增强**：除了支持传统的常量匹配，`switch` 模式匹配还支持类型匹配和更复杂的模式匹配，如解构赋值等。

3. **可读性提升**：新的语法结构更加直观和易读，使得代码更加清晰易懂。

4. **使用场景**：`switch` 模式匹配适用于多种场景，包括类型判断、条件分支和复杂逻辑处理等。它可以替代传统的 `if-else` 语句，使代码更加简洁和易于维护。

5. **语法结构**：在 JDK 21 中，`switch` 模式匹配采用了新的语法结构，允许开发者在 `switch` 语句中使用类型检查和模式匹配。使用箭头 (`->`) 分隔匹配模式和要执行的代码块。

6. **示例代码**：
   ```java
   Object value = "Hello";
   String result = switch (value) {
       case String s -> s.length() > 10 ? "Long" : "Short";
       case Integer i -> "Integer: " + i;
       default -> "Unknown";
   };
   ```

7. **与传统 `switch` 语句的区别**：传统的 `switch` 语句只能基于常量值进行匹配，而新模式匹配允许匹配更复杂的类型和模式，包括对象的属性。

8. **错误处理**：引入了 `MatchException`，在模式匹配中实现更统一的错误处理。

9. **泛型记录模式**：Java 20 增加了对 `switch` 语句/表达式中泛型记录模式类型参数推断的支持，这在 JDK 21 中也得到了应用。

10. **JDK 21 中的位置**：`switch` 表达式模式匹配作为 JDK 21 的一部分，可以通过 Java 的 `switch` 表达式直接使用。

`switch` 表达式模式匹配是 Java 语言在控制流结构方面的一个重要补充，它为开发者提供了一种新的工具来处理条件逻辑，同时简化了代码的编写和维护。

# 详细介绍 jdk20 JDK 21 虚拟线程（Virtual Threads）

JDK 21 引入了虚拟线程（Virtual Threads），这是 Project Loom 的一部分，旨在简化并发编程并提高资源利用率。

虚拟线程是 JDK 而不是 OS 实现的轻量级线程（Lightweight Process，LWP），由 JVM 调度。

以下是虚拟线程的一些关键特性：

1. **轻量级**：虚拟线程的创建、销毁和调度开销极低，可以轻松创建数以万计的虚拟线程，而不会像传统线程那样耗尽系统资源。

2. **资源利用率**：虚拟线程共享操作系统线程资源，避免了传统线程模型中线程资源的浪费，表现出色，尤其是在资源受限的环境中。

3. **简化并发编程**：开发者无需关注线程的创建、销毁和调度等细节，可以更专注于业务逻辑的实现。

4. **高可扩展性**：虚拟线程可以创建数百万甚至数十亿个线程，因此Java应用程序可以更好地适应大规模并发场景。

5. **使用示例**：
   ```java
   Thread.startVirtualThread(() -> {
       System.out.println(Thread.currentThread());
   });
   Thread.ofVirtual().start(() -> {
       System.out.println(Thread.currentThread());
   });
   ```

6. **性能对比**：在密集 I/O 的场景下，虚拟线程可以大幅提高线程的执行效率，减少线程资源的创建以及上下文切换。

7. **缺点**：虚拟线程不适用于计算密集型任务，因为密集型计算始终需要 CPU 资源作为支持。此外，虚拟线程是一个相对较新的功能，可能会存在一些未知的问题或者不稳定性。

8. **创建方法**：Java 21 提供了多种创建虚拟线程的方法，包括 `Thread.startVirtualThread()`、`Thread.ofVirtual()`、`ThreadFactory` 创建以及 `Executors.newVirtualThreadPerTaskExecutor()`。

9. **调度和固定**：虚拟线程可以被调度，并且可以被固定（pinned）到特定的操作系统线程上。

10. **JDK 21 中的位置**：虚拟线程作为 JDK 21 的一部分，位于 `java.lang` 和 `java.util.concurrent` 包中，允许开发者直接在标准 Java 应用程序中使用。

虚拟线程是 Java 并发模型的一个重大补充，它为开发者提供了一种新的工具来处理高并发场景，同时简化了并发编程的复杂性。

# 详细介绍 jdk20 JDK 21 弃用 Windows 32 位 x86 移植

在 JDK 21 中，OpenJDK 社区决定弃用对 Windows 32 位 x86 端口的支持，并计划在未来的版本中将其完全移除。这一决策反映在 JEP 449 中，其主要动机是为了使社区能够更快地开发新特性和增强功能，推动平台向前发展。

以下是 JDK 21 中关于弃用 Windows 32 位 x86 移植的一些关键点：

1. **技术限制**：在 Windows 32 位 x86 平台上，某些新特性，如虚拟线程（Project Loom），由于技术限制，无法提供预期的性能优势，因为它们会回退到使用传统的内核线程。

2. **未来展望**：随着 Windows 10（最后一个支持 32 位操作的 Windows 操作系统）的官方支持在 2025 年 10 月结束，继续维护 32 位端口的支持变得不再合理。

3. **构建过程变更**：在 JDK 21 的构建过程中，默认情况下不再允许配置 32 位 Windows 构建。如果尝试配置，构建系统将产生错误，但可以通过添加 `--enable-deprecated-ports=yes` 配置选项来覆盖。

4. **向后兼容性**：尽管 32 位 Windows 端口在 JDK 21 中被弃用，但现有的 32 位 JVM 仍然可以运行，以支持旧系统和应用程序。

5. **性能考虑**：在 64 位 Windows 上通过 WOW64 模拟层运行 32 位 JVM 可能会导致性能显著下降，这是 OpenJDK 团队决定弃用的另一个原因。

6. **过渡期**：虽然 JDK 21 弃用了 32 位 Windows 端口，但开发者和用户仍有时间迁移到 64 位系统或寻找替代方案。OpenJDK 团队提供了清晰的路径和指导，以帮助社区成员进行过渡。

7. **安全性和完整性**：禁止动态加载代理也是 JDK 21 的一个变化，这是为了提高 JVM 的安全性和完整性。动态加载代理在 JDK 21 中仍然被允许，但会发出警告，以帮助用户为将来的版本做好准备。

# 详细介绍 jdk20 JDK 21 准备禁止动态加载代理

在 JDK 21 中，引入了一个重要的变更，即 JEP 451: 准备禁止动态加载代理（Prepare to Disallow the Dynamic Loading of Agents）。

这个提案的目的是提高 Java 应用程序的安全性，通过在运行时禁止动态加载代理，减少潜在的安全风险。

以下是关于准备禁止动态加载代理的一些关键信息：

1. **安全性提升**：动态加载代理允许在运行时修改和监视 Java 应用程序的行为，虽然这在调试和性能分析方面非常有用，但也存在安全风险。恶意代码可能利用此功能执行恶意操作，如窃取敏感信息或篡改数据。

2. **实现原理**：为了实现这一变更，JEP 451 建议修改 Java 虚拟机的类加载器（ClassLoader），在 `java.lang.ClassLoader` 类中添加一个新的方法 `disallowDynamicAgentLoading()`，默认返回 `false`。当该方法返回 `true` 时，表示禁止动态加载代理。

3. **Instrumentation API 的修改**：为了支持 `ClassLoader` 的修改，还需要对 Java 虚拟机的 Instrumentation API 进行更改。在 `java.lang.instrument.Instrumentation` 接口中添加了一个新的方法 `isDynamicAgentLoadingAllowed()`，默认返回 `true`。当该方法返回 `false` 时，表示禁止动态加载代理。

4. **安全管理器的更新**：此外，提案还建议更新 Java 虚拟机的安全管理器（SecurityManager），以允许检查是否允许动态加载代理。这样可以通过对安全策略的配置来控制哪些代码可以使用动态加载代理功能。

5. **过渡期**：在 JDK 21 中，动态加载代理仍然是被允许的，但 JVM 会在发生动态加载代理时发出警告。这些警告旨在帮助用户为将来的版本做好准备，届时默认情况下将不允许动态加载代理。

6. **对现有代码的影响**：如果现有代码依赖于动态加载代理的功能，那么禁用它可能会导致这些代码无法正常工作。因此，在应用该增强提案之前，需要仔细评估现有代码的依赖关系。

7. **使用示例**：在 `premain` 方法中，通过调用 `isDynamicAgentLoadingAllowed()` 方法，可以检查是否允许动态加载代理。如果不允许，则抛出安全异常。

8. **注意事项**：在使用动态加载代理禁用准备之前，需要仔细评估现有代码是否依赖于动态加载代理的功能。需要更新相关的类加载器和安全管理器来支持禁止动态加载代理的功能。使用时，需要确保应用程序的安全策略能够正确地控制动态加载代理的使用权限。


# 详细介绍 jdk20 JDK 21 密钥封装机制 API

JDK 21 引入了密钥封装机制（Key Encapsulation Mechanism，简称 KEM）API，作为 JEP 452 的一部分，这是一个重要的安全特性，旨在提高数据传输过程中的安全性。

以下是 JDK 21 中密钥封装机制 API 的一些关键特性：

1. **密钥封装机制**：KEM 是一种加密技术，它使用非对称加密来保护对称密钥。这种方法允许在不直接暴露私钥的情况下安全地传输密钥。

2. **安全性增强**：KEM API 通过提供一种安全的方式来生成、封装和解封装密钥，增强了应用程序的安全性。它避免了传统密钥交换过程中的安全风险，如密钥泄露和中间人攻击。

3. **算法支持**：JDK 21 的 KEM API 支持多种 KEM 算法，包括 RSA-KEM 和椭圆曲线集成加密方案（ECIES），以及美国国家标准与技术研究所（NIST）后量子密码学标准化过程的候选算法。

4. **协议集成**：KEM 可以与传输层安全性（TLS）等协议集成，提供数据传输过程中的安全保障。

5. **灵活性和扩展性**：安全提供商可以在 Java 代码或本机代码中实现 KEM 算法，增加了算法的灵活性和可扩展性。

6. **使用示例**：
   ```java
   // 假设已经生成了密钥对 keyPair
   Cipher cipher = Cipher.getInstance("AES");
   cipher.init(Cipher.WRAP_MODE, keyPair.getPublic());
   byte[] wrappedKey = cipher.wrap(sharedSecret);
   // 解封装会话密钥
   cipher.init(Cipher.UNWRAP_MODE, keyPair.getPrivate());
   byte[] unwrappedKey = cipher.unwrap(wrappedKey, "AES", Cipher.SECRET_KEY);
   // 输出结果
   System.out.println("Shared Secret: " + new String(sharedSecret));
   System.out.println("Unwrapped Key: " + new String(unwrappedKey));
   ```
   这个示例展示了如何使用 JDK 21 的 KEM API 进行密钥的封装和解封装。

7. **注意事项**：使用 KEM API 时，开发者需要注意保护私钥的安全，并确保密钥封装过程中生成的密文通过安全信道传输。

8. **JDK 21 的位置**：KEM API 作为 JDK 21 的一部分，位于 `java.security` 包中，允许开发者直接在标准 Java 应用程序中使用。

密钥封装机制 API 是 JDK 21 在提升数据安全性方面的一个重要进步。

它为开发者提供了一种新的工具来保护数据传输过程中的密钥安全，同时简化了密钥管理的复杂性。

# 详细介绍 jdk20 JDK 21 字符串模板（String Templates）

# 详细介绍 jdk20 JDK 21 字符串模板（String Templates）

JDK 21 引入了字符串模板（String Templates）作为预览特性，这是 Java 语言的一个新特性，旨在提供一种更简洁和高效的方式来构建字符串。字符串模板允许开发者使用类似于格式化字符串字面量的方式来创建字符串，同时保持了字符串的不可变性。

以下是 JDK 21 中字符串模板的一些关键特性：

1. **预览特性**：字符串模板在 JDK 21 中作为预览特性引入，这意味着它还在开发中，并且可能会根据用户反馈进行改进。

2. **简洁的语法**：字符串模板提供了一种新的字符串字面量语法，允许开发者在字符串中直接嵌入表达式，从而减少了字符串拼接的复杂性。

3. **类型安全**：字符串模板在编译时进行类型检查，确保嵌入的表达式与预期的字符串格式兼容，提高了代码的安全性。

4. **性能优化**：字符串模板的设计考虑了性能，它通过优化字符串的构建过程，减少了内存分配和字符串连接操作，提高了运行效率。

5. **格式化能力**：字符串模板支持格式化操作，允许开发者指定数字、日期等的格式，提供了与 `String.format` 类似的功能。

6. **使用示例**：
   ```java
   String name = "World";
   String greeting = `Hello, ${name}!`; // 使用字符串模板构建字符串
   ```

7. **与现有字符串处理的兼容性**：字符串模板设计为与现有的字符串处理方式兼容，使得开发者可以逐步采用这一新特性，而不是完全重写现有代码。

8. **未来发展**：作为预览特性，字符串模板预计在未来的 JDK 版本中会根据开发者的反馈进行调整，并可能在将来成为正式特性。

9. **JDK 21 中的位置**：字符串模板作为 JDK 21 的一部分，可以通过 Java 的字符串字面量直接使用。

字符串模板是 Java 语言在字符串处理方面的一个重要补充，它为开发者提供了一种新的工具来简化字符串的构建和格式化。

# 详细介绍 jdk20 JDK 21 外部函数和内存 API（第三次预览）

# 详细介绍 jdk20 JDK 21 外部函数和内存 API（第三次预览）

JDK 21 包含了外部函数和内存 API（通常称为 Project Panama 的一部分）的第三次预览。这个 API 允许 Java 程序安全且高效地与 Java 运行时环境之外的代码和数据进行互操作，它在 JDK 21 中继续作为预览特性进行开发。

以下是 JDK 21 中外部函数和内存 API 的一些关键特性：

1. **安全互操作**：API 旨在提供一种比 Java Native Interface (JNI) 更安全的方法来调用本地库和操作非 JVM 管理的内存。

2. **简化的 FFI**：外部函数和内存 API 旨在简化 Java 与本地代码之间的互操作，减少 JNI 带来的复杂性和风险。

3. **内存操作**：API 提供了对非 JVM 管理内存的访问，允许 Java 代码分配、访问和操作本地内存。

4. **外部函数调用**：开发者可以使用这个 API 调用用 C 或其他语言编写的本地函数，直接从 Java 代码中。

5. **动态链接**：API 支持动态链接本地库，无需在 JVM 启动时指定所有本地库。

6. **资源类型**：引入了资源类型的概念，这些资源在作用域结束时自动被清理，减少了内存泄漏和其他资源管理问题。

7. **方法句柄**：API 使用方法句柄来表示和调用外部函数，这使得调用过程更加自然和高效。

8. **预览特性**：作为预览特性，外部函数和内存 API 允许开发者尝试这个 API 并提供反馈，以便进一步的开发和改进。

9. **示例代码**：
   ```java
   // 假设已经加载了某个本地库，并且库中有一个名为 "compute" 的函数
   MethodHandle computeHandle = lookup().find("compute");
   int result = (int) computeHandle.invokeExact(arg1, arg2);
   ```

10. **与 JNI 的关系**：FFM API 旨在最终取代 JNI，提供一种更安全和高效的方式来处理 Java 与本地代码的互操作。

11. **未来发展**：预计外部函数和内存 API 将继续发展，并在 JDK 的未来版本中成为正式特性。

外部函数和内存 API 是 Java 在处理与本地代码互操作方面的一个重要进展，它为开发者提供了一种现代、类型安全且性能优异的方法来访问外部函数和内存。

# 详细介绍 jdk20 JDK 21 未命名模式和变量（预览）

在 JDK 21 中，未命名模式和变量作为预览特性被引入，旨在简化 Java 语言中的模式匹配和变量声明。

这个特性通过引入新的语法结构，允许开发者在模式匹配和变量声明中忽略某些不需要的值，从而提高代码的可读性和简洁性。

以下是 JDK 21 中未命名模式和变量的一些关键特性：

1. **简化代码**：通过使用下划线 `_` 作为未命名模式或变量，开发者可以忽略在模式匹配中不需要的值，减少代码的冗余。

2. **提高可读性**：未命名模式和变量的使用使得代码更加直观，开发者可以更清晰地表达他们的意图，即某些值在当前上下文中是不必要的。

3. **减少模板代码**：在处理包含多个元素的数据结构时，如果某些元素不需要被单独处理，未命名模式和变量可以减少模板代码的编写。

4. **预览特性**：作为预览特性，未命名模式和变量在 JDK 21 中引入，这意味着它还在开发中，并且可能会根据用户反馈进行改进。

5. **示例代码**：
   ```java
   Object obj = ...;
   if (obj instanceof String _ || obj instanceof Integer _) {
       // 在这里，我们不关心具体的 String 或 Integer 值
   }
   ```

   在这个例子中，模式匹配使用了下划线 `_` 来表示开发者不关心 `String` 或 `Integer` 的具体值。

6. **与现有代码的兼容性**：尽管未命名模式和变量是一个新特性，但它设计为与现有的 Java 代码兼容，这意味着现有的代码可以在不做修改的情况下继续工作。

7. **未来发展**：随着 JDK 的发展，未命名模式和变量预计将在 JDK 的未来版本中成为正式特性，从而成为 Java 程序员在处理数据结构时的一个有用工具。

未命名模式和变量是 Java 语言在模式匹配方面的一个重要补充，它为开发者提供了一种新的工具来处理不需要的值，同时简化了代码的编写。

JDK 21 引入了未命名类和实例 `main` 方法作为预览特性，这是 Java 语言的一个创新，旨在简化 Java 程序的编写，特别是在创建小型工具或快速原型时。这个特性允许开发者在不定义类的情况下编写 `main` 方法，从而减少样板代码并使程序入口点更加直观。

### 关键特性：

1. **简化的程序入口**：开发者可以直接编写 `main` 方法，而不需要创建一个单独的类来封装它。

2. **减少样板代码**：未命名类的特性避免了编写重复的类定义代码，这在处理小型程序或脚本时非常有用。

3. **易于理解和使用**：对于初学者和有经验的开发者来说，这种新方法提供了一种更自然的方式来编写和理解 Java 程序。

4. **预览特性**：作为预览特性，未命名类和实例 `main` 方法可能在未来的 JDK 版本中根据用户反馈进行调整。

5. **示例代码**：
   ```java
   public static void main(String[] args) {
       // 程序逻辑
   }
   ```
   在这个示例中，`main` 方法直接出现在源文件中，没有被包裹在一个类定义中。

6. **与现有代码的兼容性**：尽管这是一个新特性，但它与现有的 Java 程序结构兼容，不会影响已有的类和 `main` 方法的使用。

7. **未来发展**：作为预览特性，未命名类和实例 `main` 方法预计将在 JDK 的未来版本中根据开发者的反馈进行改进，并可能最终成为正式特性。

8. **JDK 21 中的位置**：这个特性作为 JDK 21 的一部分，可以通过 Java 的编译器和运行时环境使用。

9. **简化的调试和测试**：未命名类的特性可能简化了单个 `main` 方法的调试和测试过程，因为开发者可以直接关注程序的入口点。

10. **教育和实验**：在教育和实验环境中，未命名类的特性可以让学生和研究人员快速尝试 Java 编程，而不必一开始就处理类的概念。

未命名类和实例 `main` 方法的引入，体现了 Java 语言对简化开发流程的持续关注，尤其是在编写小型应用程序或脚本时。

# 详细介绍 jdk20 JDK 21 作用域值（预览）

JDK 21 引入了作用域值（Scoped Values）作为预览特性，这是 Java 并发编程领域的一个重要补充。

作用域值提供了一种新的方式来管理线程内和线程间的数据共享，特别是在大量使用虚拟线程时。

以下是 JDK 21 中作用域值的一些关键特性：

1. **不可变共享数据**：作用域值允许在不同线程或虚拟线程之间安全地共享不可变数据，减少了并发编程的复杂性。

2. **自动生命周期管理**：作用域值的生命周期与创建它的线程或虚拟线程绑定，当线程结束时，作用域值会自动被清理，减少了内存泄漏的风险。

3. **简化的并发控制**：通过使用作用域值，开发者可以减少对同步机制（如锁）的依赖，从而简化并发程序的编写。

4. **预览特性**：作为预览特性，作用域值在 JDK 21 中引入，这意味着它还在开发中，并且可能会根据用户反馈进行改进。

5. **示例代码**：
   ```java
   ScopedValue<String> message = new ScopedValue<>();
   try (ScopedValueScope scope = message.open()) {
       // 在这里可以访问或修改 message 的值
       String value = message.get();
       // ...
   }
   ```
   在这个例子中，`ScopedValue` 用于在作用域内共享数据。

6. **与虚拟线程的协同**：作用域值与 JDK 21 中的虚拟线程特性紧密结合，使得在大量并发执行时，资源管理和错误处理更加高效。

7. **灵活性和扩展性**：作用域值的设计允许安全提供商在 Java 代码或本机代码中实现细节，增加了算法的灵活性和可扩展性。

8. **JDK 21 中的位置**：作用域值作为 JDK 21 的一部分，位于 `java.util.concurrent` 包中，允许开发者直接在标准 Java 应用程序中使用。

9. **未来发展**：随着 JDK 的发展，作用域值预计将在 JDK 的未来版本中成为正式特性，从而成为 Java 程序员在处理并发问题时的一个有用工具。

作用域值是 Java 并发模型的一个重大补充，它为开发者提供了一种新的工具来处理高并发场景，同时简化了并发编程的复杂性。

# 详细介绍 jdk20 JDK 21 结构化并发（Structured Concurrency）（预览）

在 JDK 21 中，结构化并发作为预览特性被引入，旨在简化和改进多线程程序的编写和管理。

结构化并发是一种编程范式，它通过将并发任务组织为一个工作单元，从而简化错误处理、取消操作，并提高程序的可靠性和可观测性。

以下是 JDK 21 中结构化并发的一些关键特性：

1. **工作单元**：结构化并发将相关的并发任务（如线程或异步任务）组织为一个工作单元，这些任务作为一个整体被管理，便于集中处理取消和异常。

2. **错误传播**：如果工作单元中的一个任务失败，结构化并发可以自动取消同一工作单元中的其他任务，并将异常传播到父任务，简化了错误处理。

3. **作用域**：结构化并发通常与一个特定的代码块或作用域关联，任务的生命周期被限制在这个作用域内。

4. **取消操作**：结构化并发提供了一种机制，允许在不需要时取消整个工作单元中的所有任务。

5. **资源管理**：它与作用域结合，可以确保在作用域结束时自动释放资源，减少了资源泄露的风险。

6. **安全性**：结构化并发通过限制任务的执行范围，减少了并发编程中的许多常见错误，如死锁和竞态条件。

7. **与虚拟线程的协同**：结构化并发与虚拟线程紧密结合，使得在大量并发执行时，资源管理和错误处理更加高效。

8. **异步编程**：结构化并发也适用于异步编程模式，可以与 CompletableFuture 或其他异步 API 结合使用。

9. **预览特性**：在 JDK 21 中，结构化并发作为一个预览特性，允许开发者尝试这个 API 并提供反馈。

10. **示例代码**：
    ```java
    StructuredTaskScope scope = StructuredTaskScope.open();
    try {
        scope.spawn(() -> {
            // 这里是并发任务的代码
        });
        // 可以继续执行其他工作单元外的代码
    } finally {
        scope.close();
    }
    ```
    使用 `StructuredTaskScope` 可以定义一个结构化并发的作用域，并使用 `spawn` 方法来启动并发任务。

11. **资源清理**：在作用域结束时，所有启动的任务都将被取消，并且相关的资源将被清理。

12. **未来发展**：结构化并发预计将继续发展，并在 JDK 的未来版本中提供更稳定的 API。

结构化并发是 Java 并发编程领域的一个重要进展，它通过提供一种新的编程模型来帮助开发者更安全、更高效地编写并发程序。

随着 JDK 的发展，结构化并发有望成为 Java 标准库中的标准特性，从而成为 Java 程序员处理并发问题的首选方法。

# 详细介绍 jdk20 JDK 21 向量 API（第六次孵化）

向量 API 是 JDK 21 中的一个孵化特性，它允许开发者表达可以在运行时编译为特定 CPU 架构上最佳向量指令的向量计算。

# 详细介绍 jdk20 JDK 21 向量 API（第六次孵化）

JDK 21 继续孵化向量 API，这是 Java 语言的一个特性，旨在提供一种表达方式，让开发者能够利用现代 CPU 架构的 SIMD（单指令多数据）指令集进行高效的向量计算。

这个特性通过提供一种与平台无关的方式来表达向量计算，使得 Java 程序能够更充分地利用硬件能力，从而提高性能。

以下是 JDK 21 中向量 API 的一些关键特性：

1. **平台无关性**：向量 API 提供了一种与平台无关的方式来表达向量计算，使得开发者可以编写一次代码，然后在支持的 CPU 架构上运行，以利用硬件加速。

2. **性能提升**：通过使用向量 API，开发者可以潜在地提高应用程序的性能，尤其是在需要大规模并行处理的数值计算任务中。

3. **安全性**：与直接使用 SIMD 指令相比，向量 API 提供了更高级别的抽象，减少了因错误使用硬件指令而带来的风险。

4. **孵化阶段**：向量 API 在 JDK 21 中处于第六次孵化阶段，这意味着它还在开发中，并且可能会根据用户反馈进行改进。

5. **内存段操作**：向量 API 增强了对 `MemorySegment` 的操作，允许从 `MemorySegment` 加载和存储向量，这与外部函数和内存 API 的预览定义相一致。

6. **表达能力**：向量 API 提供了丰富的表达能力，允许开发者以声明式的方式编写复杂的向量运算。

7. **未来展望**：随着 JDK 的发展，向量 API 预计将逐渐趋于稳定，并在未来版本中成为正式特性，从而为 Java 程序提供更广泛的硬件加速能力。

8. **示例代码**：
   ```java
   VectorSpecies species = ...; // 定义向量种类
   Vector vector = ...; // 创建向量实例
   // 执行向量运算
   ```

9. **JDK 21 中的位置**：向量 API 作为 JDK 21 的一部分，允许开发者直接在标准 Java 应用程序中使用。

向量 API 是 Java 在性能优化方面的一个重要进展，它为开发者提供了一种新的工具来利用现代 CPU 的并行处理能力，同时简化了并行计算的复杂性。

* any list
{:toc}