# full-stack-guide

> 一句话概括：这是一个面向全栈工程师的系统性学习与面试准备笔记库，按主题沉淀「原理 + 源码 + 工程取舍 + 面试表达」四层内容。

预计阅读时间：大约 5 分钟

仓库不是速查手册的堆砌，而是希望每篇文档都能回答三个问题：**它是什么、它的边界在哪、在真实项目里该怎么用**。因此文档普遍采用「一句话结论 → 机制展开 → 边界与代价 → 实践与追问」的组织方式，并配有表格、示意图和可直接运行的示例。

---

## 内容导航

- [项目说明](README.md)
- [`redis/`](redis/)
  - [Redis 常见面试题总结](redis/summary.md)
  - [跳表（Skip List）与 Redis 有序集合深度笔记](redis/skiplist-and-sorted-set.md)
- [`backend/java/`](backend/java/)
  - [`spring/`](backend/java/spring/)
    - [Spring 常见面试题总结](backend/java/spring/summary.md)
    - [IoC & AOP 详解（快速搞懂）](backend/java/spring/ioc-and-aop.md)
    - [Bean 生命周期与循环依赖（三级缓存）源码详解](backend/java/spring/bean-lifecycle-and-circular-dependency.md)
    - [Spring 事务详解](backend/java/spring/transaction.md)
    - [Spring 中的设计模式详解](backend/java/spring/design-patterns.md)
    - [Spring Boot 自动装配原理详解](backend/java/spring/boot-autoconfiguration.md)
    - [Spring Boot 的 JAR 为什么可以直接运行？](backend/java/spring/boot-executable-jar.md)
    - [@Async 注解原理分析](backend/java/spring/async-annotation-principle.md)
    - [Spring MVC 请求处理流程详解](backend/java/spring/mvc-request-flow.md)
  - [`concurrency/`](backend/java/concurrency/)
    - [Java 并发编程常见面试题总结](backend/java/concurrency/summary.md)
    - [线程基础与生命周期](backend/java/concurrency/thread-basics-and-lifecycle.md)
    - [Java 内存模型与 volatile](backend/java/concurrency/jmm-and-volatile.md)
    - [synchronized 与锁升级](backend/java/concurrency/synchronized-and-lock-escalation.md)
    - [CAS 与原子类](backend/java/concurrency/cas-and-atomic-classes.md)
    - [AQS 与 Lock 家族](backend/java/concurrency/aqs-and-locks.md)
    - [线程池原理与工程实践](backend/java/concurrency/thread-pool.md)
    - [并发容器与阻塞队列](backend/java/concurrency/concurrent-collections.md)
    - [并发协调工具：CountDownLatch、CyclicBarrier、Semaphore 与 Phaser](backend/java/concurrency/concurrency-tools.md)
    - [Fork/Join 与 CompletableFuture 异步编排](backend/java/concurrency/forkjoin-and-completable-future.md)
    - [ThreadLocal 与上下文传递](backend/java/concurrency/threadlocal.md)
    - [死锁与并发问题排查](backend/java/concurrency/deadlock-and-troubleshooting.md)
    - [虚拟线程与结构化并发（JDK 21+）](backend/java/concurrency/virtual-threads.md)

每个主题目录下的 `summary.md`（总结文档）为入口，其余为专题文档。后续会按「语言与运行时 → 数据库 → 缓存与消息 → 前端与 BFF → 系统设计与工程实践」逐步补充，新文档会同步补进上面的文件树。

---

## 目录约定

每个主题目录对应一个技术主题，可直接位于根目录（如 `redis/`），也可按领域分层（如 `backend/java/spring/`）；目录内按「一份总结 + 若干专题」组织。**总结文档在每个主题目录中固定命名为 `summary.md`**，是该主题的入口；专题文档使用英文 kebab-case 描述性命名。标题与正文为简体中文，具体文档见上方「内容导航」。

---

## 如何使用

- **先总后分**：先读某个主题下的总结类文档，建立整体认知；再按需读该主题目录下的专题文档，深挖底层实现与工程细节。
- **按问题查**：总结类文档的目录与文末速记可直接定位考点，适合临场复习。
- **对照源码读**：专题文档中的关键结构、函数通常标注了源码出处，建议边读边对照实现。
- 文档中的 mermaid 图需要支持 mermaid 的 Markdown 阅读器（GitHub、Typora、VS Code 插件等）才能正常渲染。

---

## 写作约定

新增文档时遵循以下约定，保持全库风格一致：

1. **入口固定**：每个主题目录的总结/入口文档统一命名为 `summary.md`，不因主题不同而改名；专题文档用英文 kebab-case 描述性命名。`summary.md` 在「怎么用」一节罗列本主题的专题文档及链接，新增专题时同步更新；专题文档开头用一行「所属主题」链接回 `summary.md`。
2. **开篇一句话概括**：用一段引用块说清这篇文档解决什么问题。
3. **给出阅读路线**：用表格区分初级 / 中级 / 高级 / 选型读者的关注章节，再列目录。
4. **表格优先**：对比、速查、复杂度、命令清单尽量用表格表达。
5. **讲清边界**：不仅写「怎么用」，还要写「什么时候不成立、代价是什么」。
6. **理论与实践结合**：关键结论配示意图、源码片段或可运行示例，面试类内容附追问方向。
7. **文末速记**：用几句话或一页内容帮助读者快速回忆。
8. **标注阅读时间**：开篇引用块后加一行「预计阅读时间：大约 xx 分钟」，按全文精读约 500 字/分钟估算、取 5 的整数倍。

---

## 参与维护

本项目为个人知识库，欢迎通过 Issue 指出错误或补充内容。提交前请确认：

- 文档可正常渲染，内部链接有效；
- 新增文档已登记到上方「内容导航」；
- 一次提交聚焦一个主题，提交信息说明新增或修改的文档。
