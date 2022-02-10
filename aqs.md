# AQS源码研究

## AQS是什么
AQS是AbstractQueuedSynchronizer的简写，AbstractQueuedSynchronizer是java.util.concurrent包中的一个抽象类，concurrent包是java并发大师Doug Lea通过JSR-166提议在JDK1.5引入的，提供了丰富的并发工具类。这个包中很大一部分类都是基于AQS实现的，AQS是这个包的基石。

## AQS的数据结构
