# full-stack-guide

> 一句话概括：这是一个面向全栈工程师的系统性学习与面试准备笔记库，按主题沉淀「原理 + 源码 + 工程取舍 + 面试表达」四层内容。

预计阅读时间：大约 5 分钟

仓库不是速查手册的堆砌，而是希望每篇文档都能回答三个问题：**它是什么、它的边界在哪、在真实项目里该怎么用**。因此文档普遍采用「一句话结论 → 机制展开 → 边界与代价 → 实践与追问」的组织方式，并配有表格、示意图和可直接运行的示例。

---

## 内容导航

| 主题 | 文档 | 预计阅读 | 内容概要 | 适合读者 |
| --- | --- | --- | --- | --- |
| Redis | [Redis 常见面试题总结](redis/summary.md) | 大约 60 分钟 | 按真实面试顺序覆盖基础认知、数据类型与底层编码、持久化、过期与淘汰、主从/哨兵/Cluster、缓存三大问题与双写一致性、分布式锁、事务/Lua/Pipeline、性能排障、场景设计，附 40 题快问快答与答题模板 | 初中级后端、准备面试的全栈工程师 |
| Redis | [跳表与 Redis 有序集合深度笔记](redis/skiplist-and-sorted-set.md) | 大约 60 分钟 | 从有序链表的瓶颈讲到多级索引与随机层高，结合 Redis 7.x 源码剖析 `zset` 的 `dict + skiplist` 双结构、`span` 设计与排名计算，并对比红黑树、B+ 树等替代方案 | 想深入数据结构与源码的开发者 |
| Spring | [Spring 常见面试题总结](backend/java/spring/summary.md) | 大约 70 分钟 | 按真实面试顺序覆盖 IoC/DI、Bean 生命周期与三级缓存、AOP 代理边界、事务传播与失效场景、MVC 请求流程、Boot 自动配置与 Starter、生态选型与工程踩坑，附 40 题快问快答与答题模板 | 初中级后端、准备面试的全栈工程师 |
| Spring | [IoC & AOP 详解（快速搞懂）](backend/java/spring/ioc-and-aop.md) | 大约 55 分钟 | 从 new 依赖的耦合问题讲清 IoC/DI 思想与注入方式，JDK/CGLIB 代理源码对比、通知顺序、自调用失效与 Spring AOP 边界 | 初中级后端、想搞懂代理机制的开发者 |
| Spring | [Bean 生命周期与循环依赖源码详解](backend/java/spring/bean-lifecycle-and-circular-dependency.md) | 大约 75 分钟 | doCreateBean/refresh() 源码走读、Full/Lite 配置类、三级缓存与早期代理一致性推演、无解循环依赖与 @Lazy 原理 | 准备中高级面试、想读容器源码的开发者 |
| Spring | [Spring 事务详解](backend/java/spring/transaction.md) | 大约 65 分钟 | TransactionInterceptor 与 ThreadLocal 连接绑定、七种传播行为源码、失效场景全清单、回滚规则、大事务治理与分布式选型 | 初中级后端、被事务坑过的开发者 |
| Spring | [Spring 中的设计模式详解](backend/java/spring/design-patterns.md) | 大约 50 分钟 | 工厂/单例/代理/模板方法/观察者/适配器/策略/责任链等模式的角色映射、源码锚点与面试答法 | 想系统答好模式题的开发者 |
| Spring | [Spring Boot 自动装配原理详解](backend/java/spring/boot-autoconfiguration.md) | 大约 65 分钟 | AutoConfigurationImportSelector 主链路、条件注解与用户 Bean 优先时机、自定义 Starter 完整示例、Boot 4 模块化演进 | 初中级后端、要写 Starter 的开发者 |
| Spring | [Spring Boot 的 JAR 为什么可以直接运行](backend/java/spring/boot-executable-jar.md) | 大约 65 分钟 | fat jar 布局与 MANIFEST、启动器家族与嵌套 jar 类加载、分层 jar 与 CDS/AOT 启动加速取舍 | 想搞懂类加载与部署形态的开发者 |
| Spring | [@Async 注解原理分析](backend/java/spring/async-annotation-principle.md) | 大约 55 分钟 | 代理注册链与执行器选择顺序、默认线程池坑、异常与 ThreadLocal 上下文边界、异步事件组合语义 | 初中级后端、排查过异步坑的开发者 |
| Spring | [Spring MVC 请求处理流程详解](backend/java/spring/mvc-request-flow.md) | 大约 75 分钟 | doDispatch 源码走读、参数/返回值 SPI 与自定义注解示例、Filter/Interceptor/AOP 对比、统一异常处理与异步请求 | 初中级后端、想扩展 MVC 的开发者 |
| Java 并发 | [Java 并发编程常见面试题总结](backend/java/concurrency/summary.md) | 大约 85 分钟 | 按真实面试顺序覆盖线程基础、JMM/volatile、synchronized/锁升级、CAS/原子类、AQS/Lock、线程池、并发容器、协调工具、异步编排、ThreadLocal、死锁排查、虚拟线程与版本演进，附 40 题快问快答与答题模板 | 初中级后端、准备面试的全栈工程师 |
| Java 并发 | [线程基础与生命周期](backend/java/concurrency/thread-basics-and-lifecycle.md) | 大约 90 分钟 | 四种创建方式、六状态与 BLOCKED/WAITING 区分、sleep/wait/yield/join、中断三件套与安全停止、LockSupport permit、顺序执行/交替打印四版手写 | 初中级后端、想搞懂线程 API 精确语义的开发者 |
| Java 并发 | [Java 内存模型与 volatile](backend/java/concurrency/jmm-and-volatile.md) | 大约 70 分钟 | 三大特性、JMM 抽象与硬件背景、指令重排与 as-if-serial、happens-before 规则、四类内存屏障、volatile 语义与 DCL 推演、final 与安全发布、VarHandle | 想深入 JMM 与内存语义的开发者 |
| Java 并发 | [synchronized 与锁升级](backend/java/concurrency/synchronized-and-lock-escalation.md) | 大约 80 分钟 | monitor/ObjectMonitor 源码、Mark Word 位布局、锁升级全链路与偏向锁版本归属（JEP 374/JDK 18 移除）、wait/notify、锁粗化/消除、与 Lock 选型 | 初中级后端、想答准锁升级版本题的开发者 |
| Java 并发 | [CAS 与原子类](backend/java/concurrency/cas-and-atomic-classes.md) | 大约 85 分钟 | CAS 与 lock cmpxchg、ABA 与 AtomicStampedReference、原子类家族与字段更新器、LongAdder/Striped64 分散计数源码、VarHandle 演进与回退 | 想深入无锁机制与原子类的开发者 |
| Java 并发 | [AQS 与 Lock 家族](backend/java/concurrency/aqs-and-locks.md) | 大约 90 分钟 | AQS state+CLH 变体队列独占/共享源码、ReentrantLock 公平/非公平、Condition 队列迁移、ReadWriteLock 位拆分与降级、StampedLock 乐观读、手写同步器 | 准备中高级面试、想读 j.u.c 源码的开发者 |
| Java 并发 | [线程池原理与工程实践](backend/java/concurrency/thread-pool.md) | 大约 90 分钟 | 七参数与 execute 三步源码、ctl 五状态、拒绝策略、Executors OOM 坑、submit 吞异常、调优与优雅关闭、监控与动态线程池、ScheduledThreadPoolExecutor | 初中级后端、被线程池坑过的开发者 |
| Java 并发 | [并发容器与阻塞队列](backend/java/concurrency/concurrent-collections.md) | 大约 90 分钟 | HashMap 并发问题、ConcurrentHashMap JDK 7/8 源码（putVal/sizeCtl/协助扩容/计数）、CopyOnWriteArrayList、BlockingQueue 家族与生产者-消费者、CLQ/SkipListMap | 初中级后端、想搞懂 CHM 与队列选型的开发者 |
| Java 并发 | [并发协调工具](backend/java/concurrency/concurrency-tools.md) | 大约 85 分钟 | CountDownLatch/CyclicBarrier/Semaphore/Exchanger/Phaser 源码与选型、Latch vs Barrier 必考对比、线程池+Barrier 死锁反例 | 初中级后端、需要多线程协作编排的开发者 |
| Java 并发 | [Fork/Join 与 CompletableFuture 异步编排](backend/java/concurrency/forkjoin-and-completable-future.md) | 大约 80 分钟 | FutureTask 状态机、work-stealing 双端队列、commonPool 与 parallelStream 坑、CompletableFuture 组合/异常/执行线程规则、超时 API | 初中级后端、要做异步聚合编排的开发者 |
| Java 并发 | [ThreadLocal 与上下文传递](backend/java/concurrency/threadlocal.md) | 大约 90 分钟 | ThreadLocalMap 与弱引用、内存泄漏两模式、线程池串号、InheritableThreadLocal 继承时机、TTL capture/replay/restore、ScopedValue（JDK 25） | 初中级后端、排查过上下文泄漏/串号的开发者 |
| Java 并发 | [死锁与并发问题排查](backend/java/concurrency/deadlock-and-troubleshooting.md) | 大约 65 分钟 | 死锁四条件与破解、jstack/jcmd/Arthas/JFR 工具链、CPU 飙高排查全流程、锁竞争优化、伪共享与 @Contended、JMH 陷阱 | 中高级后端、负责线上排障的开发者 |
| Java 并发 | [虚拟线程与结构化并发（JDK 21+）](backend/java/concurrency/virtual-threads.md) | 大约 85 分钟 | Loom M:N 调度与堆上栈、pinning 与 JEP 491（JDK 24）、不要池化虚拟线程、结构化并发（JDK 25 仍 preview）、ScopedValue、迁移清单 | 想落地 JDK 21+ 并发新特性的开发者 |

后续会按「语言与运行时 → 数据库 → 缓存与消息 → 前端与 BFF → 系统设计与工程实践」逐步补充，新文档会同步登记到上表。

---

## 目录结构

```text
full-stack-guide/
├── README.md          # 项目说明与文档索引
├── redis/             # 技术主题目录（如 redis/）
│   ├── summary.md     # 该主题的总结/入口文档（固定文件名）
│   └── ...            # 该主题的专题文档（底层结构、源码、场景实践）
└── backend/           # 领域大类目录，可按 语言/主题 分层
    └── java/
        ├── spring/    # 技术主题目录
        │   ├── summary.md
        │   └── ...    # 该主题的专题文档（代理、生命周期、事务、自动装配等）
        └── concurrency/  # 技术主题目录（Java 并发）
            ├── summary.md
            └── ...    # 该主题的专题文档（JMM、锁、AQS、线程池、并发容器、虚拟线程等）
```

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
8. **标注阅读时间**：开篇引用块后加一行「预计阅读时间：大约 xx 分钟」，按全文精读约 500 字/分钟估算、取 5 的整数倍，并在「内容导航」表同步登记。

---

## 参与维护

本项目为个人知识库，欢迎通过 Issue 指出错误或补充内容。提交前请确认：

- 文档可正常渲染，内部链接有效；
- 新增文档已登记到上方「内容导航」；
- 一次提交聚焦一个主题，提交信息说明新增或修改的文档。
