# AQS源码研究

## AQS是什么
AQS是AbstractQueuedSynchronizer的简写，AbstractQueuedSynchronizer是java.util.concurrent包中的一个抽象类，concurrent包是java并发大师Doug Lea通过JSR-166提议在JDK1.5引入的，提供了丰富的并发工具类。这个包中很大一部分类都是基于AQS实现的，AQS是这个包的基石。

## AQS的数据结构
![AQS_CLASS_DIAGRAM](https://user-images.githubusercontent.com/24705875/153891626-142dbac4-2db2-4e21-b610-7a484fe72f80.png)

AQS通过一个内部类Node实现了一个类似CLH队列的队列Sync Queue，这个队列对传统的CLH队列进行了一些改变。每个Node代表一个参与资源竞争的线程，通过一个AQS的内部变量state来表示同步状态，每个node都有指向前置节点和后继节点的指针。AQS还有一个内部类ConditionObject，这个内部类维护了一个Condition Queue，主要用来实现Condition的功能(wait,signal)，Condition Queue的每个节点也是Node，通过Node的nextWaiter属性把节点连接起来。

每个Node都有一个waitStatus变量，waitStatus默认为0；当线程获取锁失败被cancel时，会把状态设置为1(CANCEL)；当后继结点入队时，会将前继结点的状态更新为-1(SIGNAL),表示等待前置节点唤醒中；当线程调用ConditionObject的await方法等待时，waitStatus值会被设置为-2(CONDITION),当其它线程调用了ConditionObject的signal方法后，CONDITION状态的Node会从Condition Queue转到Sync Queue，等待获取同步锁；在共享模式下(SHARED),waitStatus还有个可能的取值-3(PROPAGATE),表示当前结点不仅会唤醒其后继结点，同时也可能会唤醒后继的后继结点。

![AQS_STRUCTURE (1)](https://user-images.githubusercontent.com/24705875/153898927-bbd2f0c0-09e4-4a7d-b4e9-039dd8651cde.png)
