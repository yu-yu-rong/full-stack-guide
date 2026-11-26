# 设计模式常见面试题总结

> 一句话概括：设计模式面试考的不是背 23 种，而是「你能不能说清每个模式解决什么问题、角色是谁、代价是什么、什么时候不该用」。

预计阅读时间：大约 65 分钟

本文按面试真实提问顺序组织：基础认知 → 设计原则 → 创建型 → 结构型 → 行为型 → 高频辨析 → JDK 与框架应用 → 现代演进 → 工程取舍 → 快问快答。每题给出**一句话结论 + 展开要点 + 追问方向**；对 JDK 21 基线的语言特性均标注版本归属（Lambda 8、record 16、sealed 17、模式匹配 21）。Spring 视角的设计模式不在此重复，见 [Spring 中的设计模式详解](../backend/java/spring/design-patterns.md)。

---

## 0. 怎么用这篇文档

### 0.1 答题四段式

面试官问「装饰器和代理什么区别」，答「都是包一层」只能拿到 30 分。用四段式：

| 层次 | 内容 | 作用 |
| --- | --- | --- |
| ① 结论 | 一句话给出判断/定义 | 让面试官知道你有整体认知 |
| ② 机制 | 关键结构、角色分工，2~4 个点 | 证明不是背的 |
| ③ 边界 | 这个模式不解决什么、代价是什么 | **区分中级和高级的分水岭** |
| ④ 实践 | 项目里在哪用过、为什么用、踩过什么坑 | 把知识变成经验 |

完整示例（问「说说策略模式」）：

> 策略模式的本质是把「同一件事的多种做法」各自封装成独立的算法族，运行时选择其一。比如折扣计算：定义 `DiscountStrategy` 接口，VIP、新客、无折扣各一个实现，调用方用 `Map<类型, 策略>` 查表分发——新增折扣类型只加一个实现类，不动分发逻辑，这正是开闭原则的落地。但策略模式只解决「选哪个实现」，不负责策略的注册与选择规则：实现多了要用注册表管理，Spring 下可以直接注入 `List<DiscountStrategy>` 按类型建索引。我并不推荐所有 if-else 都上策略——分支只有两三个且长期稳定时直写更可读；我是当促销类型涨到七八种、每次营销活动都往同一个方法里加分支时才做的重构，重构后新人加一种折扣只需要新建一个类，Code Review 再也不用担心误改别人的分支。

### 0.2 面试官的四个考察层次

| 层次 | 典型问法 | 期望 |
| --- | --- | --- |
| 会不会认 | 单例怎么写、JDK 里哪些地方用了策略 | 知道主流写法与代表实现 |
| 懂不懂结构 | DCL 为什么要 volatile、JDK 动态代理的前提 | 能讲清角色、结构与关键机制 |
| 能不能辨析 | 状态 vs 策略、聚合 vs 组合、享元 vs 对象池 | 对结构相似的模式用**意图**区分 |
| 有没有判断力 | 这个场景该不该上模式、什么时候不用 | 说得出过度设计信号与替代方案 |

### 0.3 阅读路线

| 读者 | 建议章节 |
| --- | --- |
| 初级（1 年内） | 1、2.1~2.2、3、4.1~4.3、6 高频辨析、10 快问快答 |
| 中级（1~3 年） | 全部正文，重点 5、6、7.1 |
| 高级（3 年+） | 8 现代演进、9 工程取舍，及各专题的「边界与代价」 |
| 选型/架构 | 1.3~1.4、9、[modern-java-and-anti-patterns.md](modern-java-and-anti-patterns.md) |

### 0.4 本主题专题文档

本主题按「一份总结 + 若干专题」组织，深入细节见下表（专题文档开头有回链到本文）：

| 文档 | 内容 |
| --- | --- |
| [singleton.md](singleton.md) | 七种写法对比、DCL 与 volatile、静态内部类与枚举防破坏、反射/序列化攻防、工程问题 |
| [factory-and-builder.md](factory-and-builder.md) | 简单工厂/工厂方法/抽象工厂选型、静态工厂 vs 构造器、建造者与不可变对象、clone 与拷贝构造器 |
| [proxy.md](proxy.md) | 静态→JDK→CGLIB 演进、$Proxy0 结构与代理缓存、RPC/MyBatis/Spring AOP 应用 |
| [structural-patterns.md](structural-patterns.md) | 适配器/装饰器/外观/组合/享元/桥接 + 四者辨析总表、java.io 装饰器族、Integer 缓存 |
| [strategy-template-state-command.md](strategy-template-state-command.md) | 策略消除 if-else、模板方法两种形态、状态机、命令与 undo、四者辨析 |
| [observer-and-event-driven.md](observer-and-event-driven.md) | 观察者 vs 发布订阅、Observable 废弃、Reactor 背压、lapsed listener 泄漏 |
| [chain-of-responsibility.md](chain-of-responsibility.md) | 全过型/首个命中型、Servlet Filter、Netty Pipeline 入站出站、短路语义 |
| [patterns-in-jdk.md](patterns-in-jdk.md) | JDK 模式全景表、AQS 模板方法、fail-fast、Integer 缓存 == 陷阱 |
| [modern-java-and-anti-patterns.md](modern-java-and-anti-patterns.md) | Lambda 替代对照、sealed+record+模式匹配 vs visitor、DDD 落点、过度设计信号清单 |
| [principles-and-uml.md](principles-and-uml.md) | SOLID 逐条反例正例、迪米特与合成复用、UML 六种关系、聚合组合辨析 |

本文目录：

1. 基础认知
2. 设计原则：SOLID + 迪米特 + 合成复用
3. 创建型模式速览
4. 结构型模式速览
5. 行为型模式速览
6. 高频辨析题专章
7. JDK 与框架应用题
8. 现代演进题：语言特性对模式的冲击
9. 工程取舍题
10. 快问快答 40 题
11. 面试答题模板
12. 参考资料

---

## 1. 基础认知

### 1.1 设计模式是什么、为什么要学

**结论**：设计模式是**在特定上下文中反复出现的设计问题的可复用解法**——1994 年 GoF（Gang of Four，四人组）在《设计模式：可复用面向对象软件的基础》中整理了 23 种，但这 23 种只是「模式」这个概念的样本集，不是全集。

为什么学，三个层次说清楚：

- **共同语言**：「这里用个观察者」一句话替代半小时的白板讲解，评审效率的直接来源；
- **经验的词汇表**：每个模式背后是前人对一组力量的权衡（扩展性 vs 复杂度、耦合 vs 灵活性），学模式是把这些权衡装进脑子，而不是背类图；
- **读源码的通行证**：JDK 的 AQS、Spring 的容器、MyBatis 的插件，全是模式的实战排列组合，不认识模式读源码处处碰壁。

反面提醒：**模式是手段不是目的**。「这段代码用了几个模式」不是好问题，好问题是「这段代码如何应对变化、代价是什么」。

### 1.2 GoF 23 种三分类速览

| 分类 | 数量 | 关注点 | 名单 |
| --- | --- | --- | --- |
| 创建型 | 5 | 对象**怎么造**：把创建从使用方剥离 | 单例 Singleton、工厂方法 Factory Method、抽象工厂 Abstract Factory、建造者 Builder、原型 Prototype |
| 结构型 | 7 | 对象**怎么组装**：用组合搭出更大结构 | 代理 Proxy、适配器 Adapter、装饰器 Decorator、外观 Facade、组合 Composite、享元 Flyweight、桥接 Bridge |
| 行为型 | 11 | 对象**怎么协作**：职责分配与通信 | 策略 Strategy、模板方法 Template Method、观察者 Observer、迭代器 Iterator、责任链 Chain of Responsibility、命令 Command、状态 State、访问者 Visitor、备忘录 Memento、中介者 Mediator、解释器 Interpreter |

两个常被问的边界：

- **简单工厂不属于 GoF 23 种**——它是常用的编码惯用法（一个类 + 静态方法 + 分支创建），面试里说「工厂三兄弟」时要主动补一句这个归属，显得口径干净；
- **MVC 是架构模式，不在 GoF 23 种之内**，Spring MVC 的「MVC」与 GoF 的「设计模式」是两个层面，别混答。

**追问：为什么这么分类？**
按**变化的方向**分：创建型隔离「创建」的变化，结构型处理「组装」的变化，行为型处理「职责与通信」的变化。分类不是目的，是记忆与检索的工具。

### 1.3 模式与原则的关系

**结论**：设计原则回答「**为什么这样设计是好的**」（方向与判断力），设计模式回答「**具体怎么做**」（可复用的结构）。模式是原则在特定场景下的具体化。

| 原则 | 落地的典型模式 |
| --- | --- |
| 开闭原则 OCP | 装饰器、策略、观察者、责任链——都是「加功能不加修改」的具体化 |
| 依赖倒置 DIP | 工厂、桥接——让高层依赖抽象，由工厂负责绑定实现 |
| 合成复用 CARP | 装饰器、代理——同接口组合替代继承扩展 |
| 单一职责 SRP | 命令（把「发起请求」与「执行请求」拆开）、命令与策略的分离 |
| 迪米特 LoD | 外观、中介者——给子系统/同事类一个统一的中间人 |

面试表达：「原则是判断力，模式是词汇」。只有模式没有原则，会为了用模式而用模式；只有原则没有模式，设计讨论没有共同语言。

### 1.4 「模式过时了吗」的辩证回答

**结论**：没有过时，但**形态下沉**了——很多模式从「你要手写的结构」变成了「语言/库内置的能力」，要背的样板代码变少，要懂的设计思想没变。

- **语言吸收模式**：Lambda（JDK 8）让单方法接口的策略、命令、观察者不再需要一层类样板；`Iterator`、`for-each` 把迭代器做成了语法；响应式 `Flow`（JDK 9+）把观察者升级成带背压的发布订阅。模式没有被淘汰，而是**被标准化**；
- **写法过时 ≠ 思想过时**：抽象工厂的类爆炸在动态语言里有更轻的表达，但「隔离产品族创建」的问题永远存在；
- **新问题不断产生**：微服务下的 saga、背压、幂等消费，本质仍是行为型模式的排列组合。

一句话立场：「23 种的某些形态过时了，但『识别变化点、隔离变化点』这个模式要教的核心能力，永远不会过时。」

---

## 2. 设计原则：SOLID + 迪米特 + 合成复用

原则题的答法：**一句话定义 + 典型违反例子 + 落地的模式**。逐条展开：

### 2.1 单一职责 SRP

**结论**：一个类只应该有**一个引起变化的原因**。

典型违反：`UserService` 里既有业务校验、又有 HTTP 参数解析、又发邮件——需求方是三类人（产品、前端、运营），任何一个需求都会动这个类。改法：按变化方向拆分。

**追问：怎么判断职责是不是单一？**
数「变化理由」：几类不相关的需求会改它，就有几个职责。粒度别走极端——一个方法一个类是过度拆分。

### 2.2 开闭原则 OCP

**结论**：对扩展开放、对修改关闭——**加功能靠加代码，不靠改旧代码**。

典型违反：每种促销类型在 `calcPrice` 里加一个 `else if`，改的是所有人共享的旧方法，随时可能踩坏别人的分支。改法：策略 + 注册表（见 5.1）。

**追问：OCP 是不是「不能改代码」？**
不是。旧代码有 bug、需求本身变了，当然要改；OCP 约束的是「**同类变化再来时**」的路径。而且完全的 OCP 做不到，它的价值是把修改收敛到新增点。

### 2.3 里氏替换 LSP

**结论**：所有用父类的地方，换成子类必须**行为不变**——子类只增强、不收紧父类的契约。

经典违反：

- 正方形继承长方形：`Square#setWidth` 同时改高，破坏了长方形「宽高独立」的预期，依赖父类契约的代码出 bug；
- `java.util.Stack extends Vector`（JDK 自身的反例）：栈本不该暴露 `insertElementAt`，继承让「只能在栈顶操作」的约束失效——这也是《Effective Java》「组合优先于继承」的论证素材。

**追问：什么时候继承是安全的？**
严格的 is-a 且子类不收紧契约；拿不准时用组合 + 委托。

### 2.4 接口隔离 ISP

**结论**：客户端不应该被迫依赖它用不到的方法——**接口要小而专**。

典型违反：`Worker { work(); eat(); }`，机器人 `Robot` 实现 `eat()` 只能抛异常或空实现。改法：拆成 `Workable` / `Eatable`。JDK 里 `Runnable`、`Comparable` 这类单方法接口就是 ISP 的体现。

**追问：接口越小越好吗？**
不是。接口是抽象的成本——太多小接口让组装复杂度上升。以「客户端视角」划界：客户端用到的才进接口。

### 2.5 依赖倒置 DIP

**结论**：高层模块不应依赖低层模块，**二者都依赖抽象**。

典型违反：`OrderService` 直接 `new MySQLOrderDao()`，换存储要改业务代码。改法：业务依赖 `OrderDao` 接口，由容器/工厂注入实现。

**追问：DIP、IoC、DI 什么关系？**
DIP 是**原则**（依赖抽象），IoC 是**思想**（把依赖装配的控制权反转出去），DI 是**实现手段**（构造器/setter 注入）。三者是从理念到落地的链条，不是同义词。

### 2.6 迪米特法则 LoD（最少知识原则）

**结论**：一个类只和**直接朋友**（成员、参数、返回值、自身创建的对象）通信，不和陌生类（`friend.getFriend().xxx()`）说话。

典型违反：`order.getCustomer().getAccount().deactivate()`——火车链，Order 顺带知道了账户体系的存在。改法：让 `order.deactivateCustomerAccount()` 封装转发。落地的模式：外观、中介者。

### 2.7 合成复用原则 CARP

**结论**：**优先用组合/聚合复用现有类，其次才考虑继承**。

理由：继承是白盒复用——子类依赖父类实现细节，父类改动波及所有子类（封装破碎）；组合是黑盒复用——只依赖接口。装饰器、代理就是「同接口组合扩展」的标准落地。

### 2.8 SOLID 总表

| 原则 | 一句话 | 最典型违反 | 常落地模式 |
| --- | --- | --- | --- |
| SRP 单一职责 | 一个变化理由 | 大杂烩 Service | 命令、外观 |
| OCP 开闭 | 加功能不改旧代码 | if-else 堆分支 | 策略、装饰器、观察者、责任链 |
| LSP 里氏替换 | 子类不收紧父类契约 | 正方形继承长方形 | ——（约束继承的用法） |
| ISP 接口隔离 | 不被迫依赖用不到的方法 | 胖接口空实现 | 单方法接口族 |
| DIP 依赖倒置 | 依赖抽象不依赖具体 | 直接 new 实现 | 工厂、桥接 |
| LoD 迪米特 | 只和直接朋友通信 | 火车链调用 | 外观、中介者 |
| CARP 合成复用 | 组合优先于继承 | 为复用而继承 | 装饰器、代理 |

---

## 3. 创建型模式速览

### 3.1 单例：唯一实例的攻防

**结论**：单例保证一个类**全局只有一个实例**并提供统一访问点；难点不在「写出一个」，而在「写出不能被破坏、不拖累测试的」。

主流写法对比（完整七种写法与源码级攻防见 [singleton.md](singleton.md)）：

| 写法 | 懒加载 | 线程安全 | 备注 |
| --- | --- | --- | --- |
| 饿汉式 | 否 | 是（类加载保证） | 简单；类加载时机不受控 |
| 懒汉 + synchronized | 是 | 是 | 锁在获取路径上，性能差 |
| 双检锁 DCL | 是 | 是（**必须 volatile**） | 见下 |
| 静态内部类 Holder | 是 | 是（类初始化锁） | 推荐，JVM 替你加锁 |
| 枚举 | 否 | 是 | 《Effective Java》推荐；防反射/序列化破坏 |

DCL 关键代码：

```java
public class Singleton {
    private static volatile Singleton instance;   // volatile：禁止重排 + 可见性
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {                   // ① 无锁快路径
            synchronized (Singleton.class) {
                if (instance == null) {           // ② 锁内二次确认
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

**追问：DCL 为什么必须 volatile？**
`new Singleton()` 不是原子操作，大致分三步：分配内存 → 调构造器初始化 → 把引用赋给 `instance`。②③ 可能被重排：另一个线程在①处拿到**已赋值但未初始化完**的引用，用到半成品对象。volatile 禁止这个重排。

**追问：单例会被什么破坏？怎么防？**

| 破坏方式 | 破坏点 | 防御 |
| --- | --- | --- |
| 反射 | 私有构造器被 `setAccessible` 调用 | 构造器里检测实例已存在就抛异常；**枚举天然免疫**（JVM 禁止反射创建枚举实例） |
| 序列化 | `readObject` 生成新实例 | 实现 `readResolve()` 返回既有实例；枚举序列化由 JVM 特殊处理 |
| 克隆 | `clone()` 复制实例 | 覆写 `clone()` 抛异常或不实现 `Cloneable` |

**追问：Spring 的单例和这个单例一样吗？**
不一样。Spring 是**注册表式单例**：唯一性由容器的 `singletonObjects` 映射保证（每容器、每 beanName 一份），类本身仍是普通 POJO——为的是容器能掌控注入、AOP、销毁全流程。GoF 单例是「类自治」，Spring 单例是「容器托管」，详见 [Spring 中的设计模式详解](../backend/java/spring/design-patterns.md) 第 3 章。

### 3.2 工厂三兄弟：把「造什么」从用的人手里拿走

**结论**：工厂家族都在回答同一个问题——**调用方依赖抽象，把「用哪个具体类、怎么构造」剥离给创建方**，差别在隔离的粒度。

| 变体 | 形态 | 隔离粒度 | 归属 |
| --- | --- | --- | --- |
| 简单工厂 | 一个类 + 静态方法 + 分支 | 一组产品 | **非 GoF**，惯用法 |
| 工厂方法 | 抽象工厂类 + 子类决定造什么 | 一个产品等级 | GoF |
| 抽象工厂 | 工厂接口产出**一组配套产品** | 一个产品族 | GoF |

选型口诀：**一种产品用工厂方法，一族配套产品用抽象工厂，够用就停在最简单的**。抽象工厂最常见的代价是产品族扩列时所有工厂类都要改。

**追问：静态工厂方法（`Integer.valueOf`、`List.of`）比构造器好在哪？**
《Effective Java》Item 1：① 有名字，表意强；② 不必每次新建对象（可缓存，`Integer.valueOf` 即享元入口）；③ 可返回子类型，隐藏实现类（`Collections.emptyList()`）；④ 泛型场景减少重复的类型参数。代价：没有 public 构造器的类**不能被继承**，以及 API 使用者不易发现静态工厂——所以要在 Javadoc 里写清。

**追问：JDK 里最典型的工厂方法是哪个？**
`Iterable#iterator()`——每个集合类返回自己的迭代器，遍历方完全不知道容器内部结构，这是「创建逻辑延迟到子类/自身」的教科书实现。

### 3.3 建造者：分步装配复杂对象

**结论**：建造者把「构造过程」与「最终对象」分离，链式调用逐步装配，`build()` 时统一校验并产出（通常不可变的）对象。解决的是**多参数构造的可读性与不变式校验**问题。

```java
User user = User.builder()
        .name("alice").age(18).email("a@x.com")
        .build();                     // build() 内统一校验（如 age 范围）
```

对比两条路：**telescoping 构造器**（参数爆炸、同类型参数易错位）和 **JavaBeans setter**（构造过程有中间状态、无法在一步内校验不变式、需自己处理线程安全）——建造者同时解决两者。

**追问：Lombok @Builder 有什么坑？**
① 生成的 builder 不做校验，校验逻辑要写进全参构造器（`@Builder` 会调它）；② `@Builder.Default` 字段每次 build 都重新赋默认值、且和 `new` 直接构造的初始化语义容易不一致；③ builder 类是可变对象，**不要多线程共享同一个 builder**。

**追问：record（JDK 16，JEP 395）会取代建造者吗？**
少量必选参数直接 record 构造器 + 紧凑构造器校验即可；参数多、有可选组合、需要分步装配时建造者仍有价值。二者是互补不是替代。

### 3.4 原型：复制已有对象

**结论**：原型通过**克隆现有实例**创建新对象，省去重复的构造成本或绕过「拿不到构造参数」的场景（如从数据库加载后再改一份）。

浅拷贝 vs 深拷贝：默认 `Object#clone` 是浅拷贝——引用字段被共享，改副本的可变字段会串到原对象。深拷贝要么递归 clone、要么序列化往返、要么手写拷贝构造器。

**追问：为什么《Effective Java》说宁用拷贝构造器不用 clone？**
`Cloneable` 接口是空的（改变 `Object#clone` 的受保护行为却无方法契约），clone 绕过构造器、与 final 字段冲突（final 字段无法在 clone 中重新赋值）、语义脆弱。《Effective Java》Item 13 的结论：优先拷贝构造器 / 拷贝工厂，`record`（JDK 16）天然不可变，连拷贝都常不需要。

---

## 4. 结构型模式速览

### 4.1 代理：控制访问的第一人选

**结论**：代理与目标**实现同一接口**，在转发调用的前后附加控制逻辑（延迟加载、权限、缓存、远程调用、日志）。它与装饰器结构几乎相同，**意图不同**：代理是「控制对目标的访问」，装饰器是「给目标叠功能」。

演进三部曲（源码级细节见 [proxy.md](proxy.md)）：

| 阶段 | 做法 | 问题 |
| --- | --- | --- |
| 静态代理 | 手写代理类 | 类爆炸，增强逻辑重复 |
| JDK 动态代理 | 运行时生成 `$Proxy0`（`extends Proxy implements 接口`），所有方法转发给 `InvocationHandler#invoke` | 目标必须实现接口 |
| CGLIB | ASM 生成目标**子类**，`MethodInterceptor` 拦截 | final 类/方法不可代理 |

```java
Hello h = (Hello) Proxy.newProxyInstance(
        loader, new Class<?>[]{Hello.class},
        (proxy, method, args) -> {
            log.info("before");
            Object r = method.invoke(target, args);   // 转发目标
            log.info("after");
            return r;
        });
```

**追问：代理在工程里哪里见到？**
RPC 客户端 stub（调用 `userService.getName()` 实际发出 HTTP 请求）、MyBatis 的 `MapperProxy`（接口无实现类，调用转成 SQL 执行）、Spring AOP（事务/缓存/异步）。三者共同点：**调用方无感知，增强在代理层发生**。Spring 视角见 [Spring 中的设计模式详解](../backend/java/spring/design-patterns.md) 第 4 章。

### 4.2 适配器：转换接口

**结论**：适配器把**已有的不兼容接口包装成目标接口**，解决「两个能干活但接口对不上的类怎么协作」。

- 对象适配器（推荐）：适配器实现目标接口、内部持有被适配者；类适配器用继承（Java 单继承，受限）。
- JDK 锚点：`Arrays.asList(T[])`（数组 → List 视图）、`Collections.list(Enumeration)`（枚举 → 集合）、`InputStreamReader`（字节流 → 字符流）。

```java
// 旧系统只有 Enumeration，新代码要 Iterator：一个薄适配层
// （Iterator 有 hasNext/next 两个抽象方法，不是函数式接口，适配层要写匿名类）
Iterator<E> it = new Iterator<>() {
    private final Enumeration<E> src = e;
    public boolean hasNext() { return src.hasMoreElements(); }
    public E next() { return src.nextElement(); }
    public void remove() { throw new UnsupportedOperationException(); }
};
```

### 4.3 装饰器：同接口叠加增强

**结论**：装饰器与被包装对象**实现同一接口**，委托 + 覆写附加行为，且**可层层叠加**。

JDK 最典型的是 `java.io` 流家族：

```java
InputStream in = new BufferedInputStream(       // 装饰 2：加缓冲
        new DataInputStream(                    // 装饰 1：加类型读取
                new FileInputStream(file)));    // 原始对象
```

每一层都 `extends InputStream`、持有内层流引用，功能像套娃一样叠加，而类型始终是 `InputStream`——这就是「用组合替代继承爆炸」：N 种能力若用继承要 2^N 个子类，装饰器只要 N 个装饰类。另一族锚点：`Collections.unmodifiableList` / `synchronizedList` / `checkedList`（覆写变更方法抛异常/加锁/查类型）。

### 4.4 外观：简化子系统入口

**结论**：外观给一组复杂子系统提供**统一的高层入口**，调用方只认识门面，不认识内部零件。注意：外观**不替换**原接口（想用底层能力仍然直达），只是降低典型场景的使用成本。

诚实说明：JDK 内没有强对应的外观锚点，典型例子在框架层——SLF4J 门面统一 logback/log4j、`SqlSession` 封装 MyBatis 的执行器/事务/映射器。面试别硬编 JDK 例子，指出「外观在框架层更常见」反而加分。

### 4.5 组合：整体与部分统一处理

**结论**：树形结构中让「单个对象」与「容器」实现同一接口，客户端递归处理整棵树而不区分叶子与枝干。

JDK 锚点：`java.awt.Container#add(Component)`——`Container` 本身也是 `Component`，按钮与面板用同一套 `add/paint` 接口操作。工程类比：菜单/子菜单、部门/子部门的统一计价与统计。

### 4.6 享元：共享细粒度对象

**结论**：享元把对象中**可共享的内部状态**抽出来共享，把不可共享的外部状态留在调用现场，用「多引用一个实例」省内存。

JDK 锚点（数值缓存就是享元）：

| 类 | 缓存范围 |
| --- | --- |
| `Integer.valueOf` | **-128 ~ 127**（JLS §5.1.7 强制；上界可用 `-XX:AutoBoxCacheMax=<n>` 调大） |
| `Long/Short/Byte.valueOf` | -128 ~ 127 |
| `Character.valueOf` | 0 ~ 127 |
| `Boolean.valueOf` | TRUE/FALSE 两个实例 |
| `String` 字面量 | 字符串常量池 |

```java
Integer a = 127, b = 127;   // 同一缓存实例
Integer c = 128, d = 128;   // 超出范围，各自 new
System.out.println(a == b); // true
System.out.println(c == d); // false —— 包装类型比较必须用 equals
```

**追问：为什么范围是 -128~127？**
JLS §5.1.7 只强制 `byte`、`short`、`int`、`long`、`char` 的这些值必须缓存（`Integer.valueOf` 的 Javadoc 明确写 always cache -128~127）；上界是 HotSpot 的实现选择且可通过参数调整——所以**跨 JVM 不可依赖 == 的结果，比较包装类型永远用 equals**。

### 4.7 桥接：解耦两个独立变化的维度

**结论**：桥接把「抽象」与「实现」两个**都会独立变化**的维度拆成两条继承/组合轴，用组合连接，避免 M×N 的类爆炸。

经典锚点：JDBC——`java.sql` API（抽象）与各厂商 `Driver` 实现（实现）通过接口分离，同一个业务代码换数据库不改结构。类比：消息「类型（普通/加急）× 渠道（短信/邮件）」，桥接后是 2+2 个类而不是 2×2 个类。

### 4.8 结构型四者辨析总表

面试最高频的「一句话区分」（展开见 [structural-patterns.md](structural-patterns.md)）：

| 维度 | 适配器 | 装饰器 | 代理 | 外观 |
| --- | --- | --- | --- | --- |
| 接口 | **改变**（转成目标接口） | 不变（同接口） | 不变（同接口） | 不变但**新增简化入口** |
| 目的 | 让不兼容者协作 | 动态叠加功能 | 控制访问（延迟/权限/远程） | 降低子系统使用成本 |
| 包装对象数 | 通常 1 个 | 1 个，可层层叠 | 1 个 | 聚合多个子系统 |
| 结构相似点 | 都持有一个被包装对象 | 同 | 同 | — |
| JDK 锚点 | `InputStreamReader` | `BufferedInputStream` | `java.lang.reflect.Proxy` | （弱，见框架） |

判断口诀：**接口变了是适配（多个接口收成一个则是外观），接口不变看意图——控制访问是代理、叠加功能是装饰、藏复杂度是外观**。

---

## 5. 行为型模式速览

### 5.1 策略：封装可互换的算法族

**结论**：策略把一族算法各自封装到统一接口后，运行时选择其一——**消除类型分支**的标准武器。

```java
interface DiscountStrategy { BigDecimal calc(Order order); }

// Spring 下直接注入全部实现，按类型建索引——新增策略零修改分发逻辑
Map<String, DiscountStrategy> strategies;
BigDecimal pay = strategies.get(order.type()).calc(order);
```

JDK 锚点：`Comparator`（`Collections.sort` 的算法参数）、`ThreadPoolExecutor.RejectedExecutionHandler`（拒绝策略）。

**追问：策略比 if-else 好在哪？什么时候不如 if-else？**
好在开闭：加类型只加类，分支逻辑不再互相影响。但当分支只有两三个、稳定不变时，策略的「一个分支一个类」是纯开销——**抽象的收益来自变化频率，不是分支数量**。

### 5.2 模板方法：骨架固定，变化点外置

**结论**：模板方法把不变的流程骨架固化在父类/模板里，把易变步骤留给钩子。**两种形态**（详见 [strategy-template-state-command.md](strategy-template-state-command.md)）：

| 形态 | 机制 | JDK/框架锚点 |
| --- | --- | --- |
| 继承式 | 抽象父类定骨架 + protected 钩子 | **AQS**：`acquire()` 固定「tryAcquire → 入队 → 挂起」骨架，子类只实现 `tryAcquire` |
| 回调式 | 模板类定骨架，变化点以参数（lambda）传入 | `JdbcTemplate`：连接/异常翻译/释放固定，SQL 执行是回调 |

继承式是 GoF 原型（好莱坞原则：别调用我们，我们调用你）；回调式更轻——一个模板服务所有业务，代价是回调嵌套深时难读。

**追问：AQS 为什么是模板方法？**
`AbstractQueuedSynchronizer#acquire` 写死了「获取失败 → 加节点 → 阻塞排队 → 中断处理」的完整流程，唯一的口子是 `tryAcquire`——ReentrantLock、Semaphore、CountDownLatch 各自只实现「怎么才算获取成功」。骨架（排队、挂起、唤醒）复用，语义（获取规则）自定义。

### 5.3 观察者：状态变化广播

**结论**：观察者建立「一对多」依赖：主题状态变化时自动通知所有观察者，双方只依赖抽象接口（详见 [observer-and-event-driven.md](observer-and-event-driven.md)）。

演进线索：

- `java.util.Observable/Observer`：**JDK 9 起废弃**——Javadoc 给的三条理由：事件模型太弱、**通知顺序无保证**、状态变化与通知不是一一对应（通知前必须手动 `setChanged` 是第三条的表现）；实现层补充（非 Javadoc 原文）：只能继承 `Observable` 使用，占掉唯一的 extends。替代：`java.beans.PropertyChangeListener`、`java.util.concurrent.Flow`（JDK 9+，响应式带背压）、Spring 事件机制；
- 现代形态：Guava `EventBus`、Spring `ApplicationEvent`、MQ（跨进程的发布订阅）。

**追问：观察者最大的工程坑是什么？**
**lapsed listener（失效监听器）内存泄漏**：观察者忘了从主题注销，主题一直持有强引用，观察者对象永远无法回收。对策：显式生命周期配对注册/注销、`WeakReference` 监听器（如 `WeakHashMap`），Spring 的容器托管 Bean 由容器销毁时统一清理。另一个坑：发布是**同步调用**时，一个慢监听器拖住发布者（Spring 事件默认同线程同事务）。

### 5.4 责任链：请求沿链传递

**结论**：多个处理者连成链，请求沿链传递，每个节点决定「处理或传给下一个」；价值是把「谁处理、什么顺序」从调用方解耦。两种形态（详见 [chain-of-responsibility.md](chain-of-responsibility.md)）：

| 形态 | 行为 | 例子 |
| --- | --- | --- |
| 全过型 | 每个节点都过一遍，任一节点可短路 | Servlet `Filter`、Netty `ChannelPipeline`、Spring Interceptor |
| 首个命中型 | 遍历到第一个支持者即停 | Spring `HandlerMethodArgumentResolver`、`HandlerExceptionResolver` |

```java
// 全过型骨架：不调 chain.doFilter 就是短路
public void doFilter(req, res, chain) {
    if (!auth(req)) { res.sendError(401); return; }   // 短路
    chain.doFilter(req, res);                          // 传给下一个
}
```

**追问：Netty 的入站出站顺序？**
入站事件（读）从头到尾，出站操作（写）从尾到头——两条方向的传播在同一个 pipeline 上交错，面试常拿「日志 Filter 排错位置」当追问题眼。

### 5.5 状态：行为随内部状态改变

**结论**：状态模式把每种状态下的行为封装成独立类，对象持有当前状态引用，**行为分派给当前状态对象**，状态切换由状态/上下文驱动。

对比 if-else 状态机：分支散在多个方法里 vs 状态内聚在一个类里、转移规则集中可见。适用信号：**同一个字段的多状态在多个方法里都有分支**，且状态流转有严格规则（订单、审批流、连接生命周期）。

**追问：状态什么时候不值得用？**
状态只有两三个、每个状态的行为差异小——直接枚举 + switch 更紧凑。状态模式买的是「转移规则集中 + 每状态行为可独立扩展」，付的是类数量。

### 5.6 命令：把请求封装成对象

**结论**：命令把「谁、做什么、参数」封装成对象，于是请求可以**存储、排队、撤销、重放、组合**——这是它区别于「直接调方法」的全部价值。

JDK 锚点：`Runnable/Callable`——行为对象化后交给线程池异步执行，就是最朴素的命令模式。工程形态：撤销/重做栈（编辑器）、任务队列、MQ 消息体、审计日志（命令对象可反序列化重放）。

**追问：undo 怎么实现？**
命令对象附带 `execute()` / `undo()` 对称逻辑，执行成功的命令压栈，undo 时逆序弹栈调用 `undo()`；有副作用的操作要把「执行前状态快照」存进命令对象。

### 5.7 迭代器与其余低频行为型

**结论**：迭代器把**遍历逻辑从容器中剥离**，调用方用统一接口 `hasNext/next` 遍历任何容器，不必知道内部结构（数组还是链表）。JDK 的 `Iterable/Iterator` 是教科书实现，`for-each` 语法直接内建。

**追问：fail-fast 是什么？**
`ArrayList` 的迭代器在 `next()` 时校验容器的 `modCount`（结构性修改计数），发现被并发/中途修改就抛 `ConcurrentModificationException`。注意两点：① 它是**尽力而为**（best-effort），Javadoc 明确不保证触发，不能当正确性保障；② 对应的 fail-safe/弱一致迭代器（`CopyOnWriteArrayList`、`ConcurrentHashMap`）遍历的是快照或弱一致视图。

其余低频行为型一句话过一遍（面试权重低，能说出「解决什么」即可）：

| 模式 | 一句话 | JDK/生态锚点（多为弱对应） |
| --- | --- | --- |
| 访问者 | 在不改元素类的前提下新增作用于元素的操作 | `java.nio.file.FileVisitor`、`javax.lang.model.element.ElementVisitor` |
| 备忘录 | 在不破坏封装的前提下保存/恢复对象状态 | JDK 内无一等公民，序列化/克隆近似 |
| 中介者 | 用中间对象封装一组对象的交互，网状 → 星状 | 常引用 `Executor`（弱对应） |
| 解释器 | 给定语言，定义文法与解释器 | 常引用 `java.util.regex.Pattern`（弱对应） |

---

## 6. 高频辨析题专章

辨析题是面试拉开差距的地方。方法统一：**结构相同看意图，意图不同看「谁决定、为什么」**。

### 6.1 聚合 vs 组合

| 维度 | 组合 Composition | 聚合 Aggregation |
| --- | --- | --- |
| 关系语义 | 独享的强拥有（part-of） | 共享的弱拥有（has-a） |
| 生命周期 | **整体亡则部分亡**，同生共死 | 部分独立存活，可被多个整体共享 |
| 代码特征 | 整体构造器内创建、不外泄引用 | 整体通过 setter/参数注入外部实例 |
| UML | 实心菱形（整体端） | 空心菱形（整体端） |
| 例子 | 订单与订单行（订单删则行删） | 部门与员工（员工可调岗、可属多团队） |

判断口诀：**问一句「部分能不能独立活、会不会被共享」——不能独立是组合，借来共用的叫聚合**。

### 6.2 状态 vs 策略

结构一模一样（接口 + 实现族 + 上下文持有引用、可切换），差异全在意图：

| 维度 | 策略 | 状态 |
| --- | --- | --- |
| 谁决定换实现 | **调用方**在调用时选 | **对象自身**随内部状态自动流转 |
| 实现之间 | 互相不知道 | 状态类通常知道下一状态（驱动流转） |
| 换法 | 一次性选择，一般不变 | 生命周期内频繁切换 |
| 场景 | 折扣算法、排序规则 | 订单状态机、TCP 连接状态 |

判断口诀：**「外部选一个用」是策略，「自己变成另一个」是状态**。

### 6.3 命令 vs 策略

| 维度 | 命令 | 策略 |
| --- | --- | --- |
| 封装的东西 | 一次**请求**（动作 + 参数 + 时刻） | 一族**算法** |
| 价值 | 存储排队、撤销重放、异步、日志 | 运行时替换算法 |
| 生命周期 | 常延迟执行、可重复执行 | 选择后即执行 |
| 锚点 | `Runnable`、MQ 消息、undo 栈 | `Comparator` |

判断口诀：**为了「以后执行/可撤销」封装的是命令，为了「现在换一种做法」封装的是策略**。

### 6.4 观察者 vs 发布订阅

| 维度 | 观察者 | 发布订阅 |
| --- | --- | --- |
| 中间层 | 无：主题直接持有观察者引用 | 有：事件通道/broker 居中 |
| 耦合 | 主题知道「有哪些观察者」（进程内） | 双方互不认识，只认识消息契约（可跨进程） |
| 典型 | Spring `ApplicationEvent`、`Flow` | Kafka/RocketMQ、Redis Pub/Sub |

判断口诀：**有 broker 是发布订阅，直接回调名单是观察者**。 Spring 事件严格说是进程内观察者（multicaster 持引用），见 [Spring 中的设计模式详解](../backend/java/spring/design-patterns.md) 第 6 章。

### 6.5 代理 vs 装饰器 vs 适配器

| 维度 | 适配器 | 装饰器 | 代理 |
| --- | --- | --- | --- |
| 接口 | 改变 | 不变 | 不变 |
| 意图 | 兼容异构 | 叠加功能 | 控制访问 |
| 谁创建 | 调用方手动包 | 调用方手动层层包 | 常由**框架/工厂**生成（对调用方透明） |
| 增强逻辑 | 不增强，只转发/转换 | 附加行为 | 延迟、权限、远程、横切 |

判断口诀：**接口变没变？变了是适配器；不变看意图——管你是代理，帮你变强是装饰器**。代理与装饰器结构最像，再补一刀：代理的典型形态是「一个代理控制一个目标、生成过程对调用方不可见」（Spring Bean 注入的其实是代理），装饰器是「调用方显式套娃、想套几层套几层」（`java.io`）。

### 6.6 模板方法 vs 策略

| 维度 | 模板方法 | 策略 |
| --- | --- | --- |
| 变化粒度 | 骨架中的**个别步骤** | **整个算法** |
| 流程控制权 | 模板（骨架写死） | 调用方选并驱动 |
| 实现手段 | 继承钩子或回调 | 组合 + 委托 |
| 锚点 | AQS、`JdbcTemplate` | `Comparator` |

判断口诀：**只换流程中的一步用模板，整段流程都换用策略**。

### 6.7 责任链 vs 装饰器

| 维度 | 责任链 | 装饰器 |
| --- | --- | --- |
| 传递 | 节点**可短路**（不传下家）或改道 | 请求必达，逐层透传给最终对象 |
| 结构 | 链节点知道链（持 chain 引用） | 装饰器只包一个内层对象 |
| 组合方式 | 节点可动态增删排序 | 调用方显式嵌套 |
| 锚点 | Servlet Filter、Netty Pipeline | `java.io`、`Collections.unmodifiableList` |

判断口诀：**链上节点能「拦下不往下传」的是责任链；必然层层包裹到核心对象的是装饰器**。

### 6.8 享元 vs 对象池

| 维度 | 享元 | 对象池 |
| --- | --- | --- |
| 共享语义 | 同一实例**同时**被多方引用 | 实例**独占**：借出后别人拿不到 |
| 对象状态 | 不可变/内部状态共享 | 可变，借出前要重置状态 |
| 回收 | 不存在借还，生命周期随 JVM | 显式归还，池管生命周期 |
| 锚点 | `Integer` 缓存、String 常量池 | 数据库连接池、Netty `ByteBuf` 池化 |

判断口诀：**大家共用一份不可变的是享元，一人借走用完要还的是对象池**。

---

## 7. JDK 与框架应用题

### 7.1 「JDK 里有哪些设计模式」怎么答

这题考的是**能不能从「背模式」切换到「认模式」**。答法：按三分类报锚点，每个锚点带一句「它在那里解决什么问题」。全景表（弱对应已标注；详见 [patterns-in-jdk.md](patterns-in-jdk.md)）：

| 分类 | 模式 | JDK 锚点 | 一句话 |
| --- | --- | --- | --- |
| 创建型 | 单例 | `Runtime#getRuntime` | 每个 JVM 一份运行时环境 |
| 创建型 | 静态工厂 | `Integer.valueOf`、`List.of`、`Optional.empty` | 有名字、可缓存、可返回子类型 |
| 创建型 | 工厂方法 | `Iterable#iterator` | 每个集合决定自己的迭代器怎么造 |
| 创建型 | 抽象工厂（弱） | `java.sql.Connection#createStatement/prepareStatement` | 同一工厂产出配套的语句对象 |
| 创建型 | 建造者 | `StringBuilder`、`Stream.Builder`、`Locale.Builder` | 分步装配复杂对象 |
| 创建型 | 原型 | `Object#clone`（`ArrayList#clone`） | 复制既有实例 |
| 结构型 | 适配器 | `Arrays.asList`、`InputStreamReader`、`Collections.list` | 数组/枚举/字节流适配成新接口 |
| 结构型 | 装饰器 | `java.io` 全家、`Collections.unmodifiableList` | 同接口层层叠加能力 |
| 结构型 | 代理 | `java.lang.reflect.Proxy` | 运行时接口代理，横切逻辑入口 |
| 结构型 | 组合 | `java.awt.Container#add(Component)` | 树形组件统一 add/paint |
| 结构型 | 享元 | Integer/Long/Character 缓存、String 常量池 | 共享小对象省内存 |
| 结构型 | 桥接 | JDBC（`java.sql.Driver`） | API 与厂商实现两轴独立演进 |
| 结构型 | 外观（弱） | JDK 内无强对应，框架层典型（SLF4J） | 统一简化入口 |
| 行为型 | 策略 | `Comparator`、`RejectedExecutionHandler` | 算法作为参数传入 |
| 行为型 | 模板方法 | **AQS**（`acquire` + `tryAcquire`）、`InputStream#read(byte[],int,int)` | 骨架固定，钩子留给子类 |
| 行为型 | 观察者 | `Observable/Observer`（9 废弃）、Swing Listener、`Flow`（9+） | 状态变化广播 |
| 行为型 | 责任链（弱） | `java.util.logging.Logger` 父子链、ClassLoader 双亲委派 | 请求沿链查找/传递 |
| 行为型 | 命令 | `Runnable`/`Callable` | 行为对象化，交给线程池 |
| 行为型 | 迭代器 | `Iterator/Iterable`、`Spliterator` | 遍历与容器解耦，fail-fast |
| 行为型 | 访问者 | `java.nio.file.FileVisitor`、`javax.lang.model` 的 `ElementVisitor` | 遍历结构时施加操作 |
| 行为型 | 状态（弱） | JDK 内无强对应（`Thread.State` 只是枚举） | 工程中状态机更典型 |
| 行为型 | 中介者/备忘录/解释器（弱） | 常引用 `Executor`/序列化/`Pattern`，对应较弱 | 一句话说出问题域即可 |

示范答案（60 秒版）：

> 按 GoF 三分类说：创建型里 `Runtime#getRuntime` 是饿汉单例，`Integer.valueOf`、`List.of` 是静态工厂，最典型工厂方法是 `Iterable#iterator`——每个集合返回自己的迭代器；结构型里 `java.io` 的 `BufferedInputStream` 是装饰器，`Arrays.asList` 是适配器，`java.lang.reflect.Proxy` 是动态代理，Integer 缓存和 String 常量池是享元，JDBC 是桥接；行为型里 `Comparator` 是策略，AQS 是模板方法——`acquire` 写死排队挂起骨架、子类只实现 `tryAcquire`，`Iterator` 是迭代器，fail-fast 靠 `modCount`。JDK 里也有弱对应，比如外观，我不会硬编，会说典型外观在框架层。

### 7.2 Spring / MyBatis / Netty 中的模式

| 框架 | 模式 → 锚点 |
| --- | --- |
| Spring | 工厂（`BeanFactory`/`FactoryBean`）、注册表单例、代理（AOP）、模板方法（`JdbcTemplate`）、观察者（事件）、适配器（`HandlerAdapter`）、策略（`InstantiationStrategy`）、责任链（Filter/Interceptor/Advice）——详见 [Spring 中的设计模式详解](../backend/java/spring/design-patterns.md) |
| MyBatis | 动态代理（`MapperProxy`）、工厂（`SqlSessionFactory`）、责任链（插件 `InterceptorChain`）、装饰器（缓存族 `LoggingCache`/`LruCache` 叠包 `Cache`）、策略（`Executor` 的 Simple/Reuse/Batch 变体）——详见 [MyBatis 常见面试题总结](../backend/java/mybatis/summary.md) |
| Netty | 责任链（`ChannelPipeline` 入站出站）、对象池（`ByteBuf` 池化，注意与享元辨析）、建造者（`ServerBootstrap`）、观察者/Promise（`Future` 回调） |

答题要领：**模式名 + 组件锚点 + 在那里解决的问题**，三段齐才有效。比如不说「MyBatis 用了代理」，而说「`MapperProxy` 用 JDK 动态代理解决『接口没有实现类』的问题——调用被转成定位 SQL 并执行」。

---

## 8. 现代演进题：语言特性对模式的冲击

### 8.1 版本口径（JDK 21 基线，先背这张表）

| 特性 | 落地版本 | JEP/JSR | 对模式的冲击 |
| --- | --- | --- | --- |
| Lambda 与方法引用 | JDK 8 | JSR 335 | 单方法接口的策略/命令/观察者不再需要一层类样板 |
| 接口默认方法/静态方法 | JDK 8 | JSR 335 | 模板钩子、缺省适配可直接进接口 |
| `var` 局部类型推断 | JDK 10 | JEP 286 | 与模式无直接关系，写法简化 |
| switch 表达式 | JDK 14 | JEP 361 | 分支可作表达式，查表分发更顺 |
| 文本块 | JDK 15 | JEP 378 | — |
| **record** | **JDK 16** | **JEP 395** | 不可变数据载体；建造者/原型的部分场景被简化 |
| instanceof 模式匹配 | JDK 16 | JEP 394 | 判别式分支简化 |
| **sealed** | **JDK 17** | **JEP 409** | 受控的继承层级声明，为穷尽匹配铺路 |
| **switch 模式匹配** | **JDK 21** | **JEP 441** | **访问者的现代替代主力** |
| record 模式（嵌套解构） | JDK 21 | JEP 440 | 深度匹配数据结构 |
| 未命名变量 `_` | JDK 22 | JEP 456 | 模式匹配收尾便利 |

面试提示：报版本时用「record 是 16 转正（14/15 预览）、sealed 是 17 转正（15/16 预览）、switch 模式匹配 21 转正（17~20 预览）」的口径，一句「我按转正版本说」能避开预览版本争议。

### 8.2 Lambda 替代了哪些「模式样板」

单方法接口的模式（策略、命令、观察者、模板回调），过去每个算法一个类，现在一个 lambda：

```java
// JDK 8 之前：策略要一个类
class VipDiscount implements DiscountStrategy {
    public BigDecimal calc(Order o) { return o.amount().multiply(new BigDecimal("0.8")); }
}
// JDK 8 之后：策略是一个函数
DiscountStrategy vip = o -> o.amount().multiply(new BigDecimal("0.8"));
```

要点：**被替代的是「样板形态」，不是「模式思想」**。Lambda 版策略依然需要：统一函数签名（接口）、注册与选择机制（Map 查表）、以及「行为可替换」的设计意图——这三样才是策略模式的本体。复杂策略（多方法、带状态）仍然值得写类。

### 8.3 sealed + record + switch 模式匹配 vs 访问者

访问者模式的痛点：加一个元素类型要改**所有** visitor 与所有元素类的 `accept`；双分派样板一堆。JDK 21 的组合拳：

```java
// JDK 16/17/21：record（395）+ sealed（409）+ switch 模式匹配（441）
public sealed interface Shape permits Circle, Square {}
public record Circle(double r) implements Shape {}
public record Square(double side) implements Shape {}

double area(Shape shape) {
    return switch (shape) {
        case Circle c -> Math.PI * c.r() * c.r();
        case Square s -> s.side() * s.side();
    };   // sealed 让编译器做穷尽检查：漏一个分支编译不过，无需 default
}
```

对比结论：

- **加操作**：两者都容易——visitor 新写一个类，sealed 版新写一个 switch；
- **加类型**：两者都难——都要改所有既有分支；这是「类型稳定、操作多变」场景的共同取舍；
- sealed 版的优势：无 `accept` 双分派样板、编译期穷尽检查、数据（record）与行为分离更直观；visitor 仍适用的场景：操作与元素**跨包/跨模块扩展**（编译期拿不到全部类型，如编译器 API、字节码框架），以及需要对**异构对象结构**（`FileVisitor` 的文件树）做访问。

### 8.4 「设计模式会消失吗」的收敛回答

语言特性吸收的是模式的**表达成本**（类样板、双分派、回调接口），模式沉淀的是**设计判断**（变化点在哪、谁依赖谁、代价是什么）。可以收尾：「20 年前考『会不会写单例』，今天考『该不该用单例』——题变了，能力没变。」

---

## 9. 工程取舍题

### 9.1 「你用过什么设计模式」怎么答

这题的真实考点是**有没有真实工程判断**。套路：**场景 → 为什么是这个模式 → 代价与验证**，绝不能报菜名。

示范（策略 + 观察者的组合实战）：

> 我们营销系统的价格计算原本是 `calcPrice` 里十几个 `else if`，每次活动规则改动都要动这个公共方法，出过一次改 A 分支踩坏 B 分支的事故。重构时我先按「变化频率」筛选：只有折扣类型是高频变化的，于是抽出 `DiscountStrategy` 接口，Spring 注入全部实现按类型建索引，加类型从此只加类；而「折扣生效后要刷新购物车、发券、埋点」这类后续动作，我用 `ApplicationEventPublisher` 发领域事件解耦，促销模块订阅事件，主流程不再依赖下游。代价也如实说：类变多了、调用链多一跳，我们用约定收口——策略实现必须无状态、事件监听器不允许抛异常回传。上线后改一次营销规则的发布风险明显下降，这个重构我认为值。

### 9.2 过度设计信号清单（自检）

| 信号 | 说明 |
| --- | --- |
| 接口只有一个实现，且看不到第二个的前景 | 抽象无对价，直接用类 |
| 为「以后可能」预留的抽象/参数 | YAGNI：等第二次出现再抽象（Rule of Three） |
| 继承层级超过 3 层 | 每层都在稀释可读性，改用组合 |
| 中间层只有一层透传 | 纯粹的调用转发，删掉 |
| 团队没人读得懂 | 模式的维护成本大于它解决的问题 |
| 单例里塞全局可变状态 | 用单例「省传参」，实际造出并发与测试坑 |
| 一个方法里三个分支也要上策略 | 抽象的收益来自重复与变化，没有就没有 |

### 9.3 什么时候不该用模式

- **需求还在剧烈变化**：先写直白代码，等形状稳定再抽——抽象错方向的代价远大于重构成本；
- **没有重复、没有变化**：模式的核心收益（隔离变化、复用结构）不存在，硬套只剩复杂度；
- **可读性损失大于耦合收益**：两行能说清的事，别让读者跳五个类；
- **性能敏感的极端路径**：多层委托的间接调用平时可忽略，热点路径上要掂量（一般仍不是瓶颈，先测再优化）。

面试收尾口径：「我反对的不是模式，是**没有问题先上模式**。每个模式我都要求自己能说出：它替我挡住了什么变化、代价是什么、什么时候我会拆掉它。」

---

## 10. 快问快答 40 题

| # | 问题 | 一句话答案 |
| --- | --- | --- |
| 1 | GoF 开篇的两大宗旨 | 面向接口编程而非实现；优先组合而非继承 |
| 2 | GoF 23 种怎么分类 | 创建型 5、结构型 7、行为型 11 |
| 3 | 简单工厂属于 GoF 吗 | 不属于，是惯用法；GoF 只有工厂方法与抽象工厂 |
| 4 | DCL 为什么要 volatile | 禁止「初始化」与「赋引用」重排，防止别的线程拿到半成品引用 |
| 5 | 静态内部类单例为何线程安全 | 类初始化由 JVM 加锁保证原子，且首次引用才加载 → 免费懒加载 |
| 6 | 枚举单例防住什么 | 反射（JVM 禁止反射建枚举）与序列化（枚举特殊处理），《Effective Java》推荐 |
| 7 | Spring 单例与 GoF 单例区别 | 容器注册表每容器一份、类是 POJO vs 类自治每 JVM 一份 |
| 8 | 静态工厂比构造器好在哪 | 有名字、可缓存复用、可返回子类型、泛型省类型参数 |
| 9 | 工厂方法 vs 抽象工厂 | 一个产品等级 vs 一族配套产品 |
| 10 | 建造者相对 JavaBeans 的优势 | 构造过程无中间状态、build 时统一校验不变式、对象可不可变 |
| 11 | 为什么少用 clone | Cloneable 无契约、绕过构造器、final 字段受限；优先拷贝构造器 |
| 12 | 原型浅拷贝的风险 | 引用字段被共享，改副本串改原对象；可变字段要深拷贝 |
| 13 | 代理与装饰器的区别 | 结构同、意图异：控制访问 vs 叠加功能；代理常由框架生成 |
| 14 | JDK 动态代理的前提 | 目标必须实现接口；无接口用 CGLIB 子类代理（final 不可） |
| 15 | 适配器 vs 装饰器 | 转换接口让不兼容者协作 vs 同接口叠加增强 |
| 16 | 外观与适配器的区别 | 外观简化多子系统入口（原接口保留），适配器一对一转接口 |
| 17 | `Integer.valueOf(127) == Integer.valueOf(127)`、128 呢 | 127 true（缓存 -128~127）；128 false，各自 new，比较用 equals |
| 18 | Integer 缓存范围能调吗 | 下界固定 -128，上界可 `-XX:AutoBoxCacheMax` 调大，跨 JVM 不可依赖 |
| 19 | java.io 流是什么模式 | 装饰器：每层 extends InputStream 且持有内层流，能力层层叠加 |
| 20 | `Collections.unmodifiableList` 是什么 | 装饰器：覆写变更方法抛 UnsupportedOperationException |
| 21 | 享元 vs 对象池 | 多方共享一份不可变 vs 独占借还要重置 |
| 22 | 桥接解决什么 | 抽象与实现两个维度独立变化，避免 M×N 类爆炸；锚点 JDBC |
| 23 | 组合模式的锚点 | `Container#add(Component)`：容器也是组件，树形统一处理 |
| 24 | 策略怎么消除 if-else | 统一接口 + 实现族 + Map 查表分发，加类型只加类 |
| 25 | 策略什么时候不如 if-else | 分支两三个且稳定时，策略的类爆炸是纯开销 |
| 26 | 模板方法两种形态 | 继承式钩子（AQS）与回调式（JdbcTemplate） |
| 27 | AQS 是什么模式 | 模板方法：acquire 固定骨架，tryAcquire 留给子类 |
| 28 | Observable 为什么废弃 | JDK 9 起 javadoc 三条：模型弱、顺序无保证、状态与通知不一一对应；补充：只能继承使用 |
| 29 | 现代观察者的替代品 | `PropertyChangeListener`、`Flow`（响应式带背压）、Spring 事件 |
| 30 | lapsed listener 是什么 | 忘注销导致监听器泄漏；对策显式注销或弱引用 |
| 31 | 责任链两种形态 | 全过型可短路（Filter）与首个命中型（Resolver） |
| 32 | Netty pipeline 入站出站顺序 | 入站头→尾，出站尾→头 |
| 33 | 状态 vs 策略 | 自己随内部状态流转是状态，调用方选算法是策略 |
| 34 | 命令模式的价值 | 请求对象化：可排队、撤销、重放、日志；锚点 Runnable |
| 35 | 迭代器 fail-fast 机制 | 校验 modCount 抛 CME；best-effort，不是正确性保障 |
| 36 | 聚合 vs 组合 | 借来共享可独立活（空心菱形）vs 独享同生共死（实心菱形） |
| 37 | 命令 vs 策略 | 封装「以后执行的请求」是命令，封装「现在替换的算法」是策略 |
| 38 | record/sealed/switch 模式匹配的版本 | record 16（JEP 395）、sealed 17（JEP 409）、switch 匹配 21（JEP 441） |
| 39 | sealed+record+switch 替代谁 | 访问者：无双分派样板、编译期穷尽检查；跨包扩展仍可用 visitor |
| 40 | 答模式题的三问 | 解决什么问题、角色是谁、代价是什么（+ 什么时候不该用） |

---

## 11. 面试答题模板

### 11.1 模式题三问

任何「讲讲 XX 模式」都按三问走，缺一问就掉档：

| 问 | 答什么 | 反例 |
| --- | --- | --- |
| ① 解决什么问题 | 一句话点出痛点（变化点/耦合点） | 背定义「定义了算法族分别封装……」 |
| ② 角色是谁 | 接口/实现族/上下文/工厂各是谁，最好带一个 JDK/框架锚点 | 只画类图不落锚点 |
| ③ 代价是什么 | 类数量、间接层、维护点、失效场景 | 只说好处 |

### 11.2 完整示范答案

问「说说你熟悉的某个模式」，选装饰器：

> 我用装饰器最多。它解决的问题是：**想给一个已有类型叠加可选能力，又不想到继承爆炸**。java.io 是范本——`BufferedInputStream`、`DataInputStream` 都 `extends InputStream` 且持有内层流，N 种能力组合从 2^N 个子类降为 N 个装饰类，我们在网关里给请求体做「可重复读 + 压缩解压」就是两层装饰。角色上：被装饰者与装饰器同接口（`InputStream`），装饰器持有同类型引用（组合）。代价要主动说：①层层委托的调用链让栈变深，排障要先理清包装顺序；②装饰器之间对「语义顺序」敏感——先缓冲再解压和先解压再缓冲结果不同；③能力一多，包装表达式变得难读。什么时候我不用它：只是临时加一个行为、且只有一个包装层时，直接写一个明确的包装类反而好懂——装饰器的价值在于**可叠加**，没有叠加需求就别付这个复杂度。

### 11.3 辨析题模板

「A 和 B 什么区别」一律三步：**① 承认结构相似（显格局）→ ② 用意图/控制权一句话切开 → ③ 各配一个锚点收尾**。例：「代理和装饰器结构完全一样——同接口、持内层引用；区别在意图：代理控制访问且常由框架透明生成（Spring Bean 注入的其实是代理），装饰器由调用方显式套娃叠加（java.io）。」

---

## 12. 参考资料

- GoF，《设计模式：可复用面向对象软件的基础》（Design Patterns: Elements of Reusable Object-Oriented Software, 1994）——23 种模式与两大宗旨的原始出处
- 《Effective Java（第 3 版）》Joshua Bloch——Item 1 静态工厂、Item 3 枚举单例、Item 13 拷贝替代 clone、Item 18 组合优先继承
- 《Refactoring to Patterns》Joshua Kerievsky——「模式是重构的目标而非起点」的工程视角
- 《Head First 设计模式》——入门图形化教材，角色记忆友好
- JDK 官方文档：
  - `Integer.valueOf` Javadoc（缓存 -128~127）：`docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Integer.html#valueOf(int)`
  - JLS §5.1.7 Boxing Conversion（缓存强制范围）
  - `java.util.Observable` Javadoc（`@Deprecated(since="9")` 及废弃理由）
  - `java.util.concurrent.Flow`（JDK 9+ 响应式发布订阅）
- JEP 索引：Records（JEP 395，JDK 16）、Sealed Classes（JEP 409，JDK 17）、Pattern Matching for switch（JEP 441，JDK 21）、Record Patterns（JEP 440，JDK 21）、Pattern Matching for instanceof（JEP 394，JDK 16）
- Refactoring.Guru（design-patterns.com 中文版）——模式图解与反模式提示
- 本库相关专题：[Spring 中的设计模式详解](../backend/java/spring/design-patterns.md)、[MyBatis 常见面试题总结](../backend/java/mybatis/summary.md)，以及本目录下 10 篇专题（见 0.4）

---

## 附：一页速记

**总纲**
> 模式 = 识别变化点 + 隔离变化点的词汇表；答题三问：解决什么问题、角色是谁、代价是什么，加一句「什么时候不该用」直接上档。

**原则层**
1. SOLID 一句话：职责一个变化理由、扩展不加修改、子类不缩契约、接口不塞闲货、依赖抽象不依赖具体；外加迪米特（不跟陌生人说话）与合成复用（组合优先继承）。
2. 原则是「为什么」，模式是「怎么做」：OCP → 策略/装饰器/观察者；DIP → 工厂/桥接。

**创建型**
3. 单例五写法：饿汉、懒汉锁、DCL（必须 volatile）、Holder（类初始化锁）、枚举（防反射/序列化）；另有 DCL-无 volatile（错误示范）与 CAS 两种教学变体，合计七种，详见 singleton.md。
4. Spring 单例是注册表式：每容器每名字一份 ≠ JVM 一份 ≠ 线程安全。
5. 工厂：一种产品用工厂方法，一族产品用抽象工厂，简单工厂不是 GoF；静态工厂四优势：名字、缓存、子类型、省泛型。
6. 建造者解决多参数可读性与不变式校验；clone 脆弱，优先拷贝构造器。

**结构型**
7. 四者口诀：接口变了是适配（多个收成一个则是外观），不变看意图——控制访问是代理、叠加功能是装饰、藏复杂度是外观。
8. 装饰器范本 java.io；适配器锚点 `InputStreamReader`/`Arrays.asList`；代理演进 静态→JDK（$Proxy0）→CGLIB。
9. 享元：Integer 缓存 -128~127（上界 `AutoBoxCacheMax` 可调），== 只对缓存内成立，包装类型必用 equals。
10. 桥接 = 两个独立变化维度拆两轴（JDBC）；组合 = 树形整体部分同一接口（`Container#add`）。

**行为型**
11. 策略消除 if-else：接口 + 实现族 + Map 查表；分支少且稳定就别上。
12. 模板两种形态：继承钩子（AQS：acquire + tryAcquire）与回调（JdbcTemplate）。
13. 观察者：Observable 废弃于 JDK 9；同步发布连累发布者；lapsed listener 泄漏要防。
14. 责任链两形态：全过型可短路（Filter/Netty Pipeline），首个命中型（Resolver）。
15. 状态机买「转移规则集中」，命令买「请求可存储撤销」，迭代器买「遍历解耦 + fail-fast（modCount，best-effort）」。

**辨析口诀**
16. 聚合组合：能独立活、可共享是聚合；同生共死是组合。
17. 状态策略：自己变是状态，外部选是策略。
18. 命令策略：以后执行/撤销是命令，现在换算法是策略。
19. 代理装饰适配：变接口是适配，控制访问是代理，叠加增强是装饰。
20. 享元对象池：共享不可变是享元，独占借还是对象池。

**现代演进**
21. 版本口径：Lambda 8（JSR 335）、record 16（JEP 395）、instanceof 匹配 16（394）、sealed 17（409）、switch 匹配 21（441）、record 模式 21（440）。
22. sealed + record + switch 替代 visitor：无双分派样板、穷尽编译检查；类型稳定操作多变的场景。

**取舍**
23. 过度设计信号：单实现接口、为以后预留的抽象、继承超 3 层、纯透传层、团队读不懂。
24. 反对的不是模式，是没有问题先上模式——每个模式都要说得出「挡住了什么变化、代价是什么、什么时候拆掉」。
