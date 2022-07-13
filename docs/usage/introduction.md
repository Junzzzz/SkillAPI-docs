# 使用介绍

!!! warning "以下内容基于`1.7.10`版本"

## 前言

虽然`FML`提供了许多接口帮助我们进行开发，理论上可以在Minecraft中实现任何想要的内容<del>（反射大法好）</del>，但是作为接口，它使用起来并不能算的上“方便”。

以监听事件为例，比如你需要监听`PlayerRespawnEvent`这个事件，那么你需要创建一个类来存放监听函数，比如：

```java
public class CommonEvents {
    @SubscribeEvent
    public void onPlayerRespawn(PlayerEvent.PlayerRespawnEvent event) {
        // Do something...
    }
}
```

然后，你需要注册该类至事件总线：

```java
public void init(FMLInitializationEvent event) {
    // 在初始化事件中注册
    MinecraftForge.EVENT_BUS.register(new CommonEvents());
}
```

这里新手可能会遇到第一个问题<del>（没错就是我）</del>，注册好的事件并不生效。其实要解决也很简单，就是事件总线选择错了。换成下面的代码就搞定了：

```java
public void init(FMLInitializationEvent event) {
    FMLCommonHandler.instance().bus().register(new CommonEvents());
}
```

当时我的心情有点复杂，为什么监听个事件都要找到触发该事件的总线才行……每次挑个事件还得翻翻源码看看用哪个去触发的。类似的麻烦还有很多，于是根据自己的理解编写了一套用于简化开发的框架，不过主要还是用于方便二次扩展本模组用的，许多东西并没有考虑的很周全。
