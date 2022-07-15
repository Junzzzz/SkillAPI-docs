# Message 消息工具

该工具类主要用于简化向玩家发送消息的方法。

## 方法

### send

```java
Message.send(player, message);
```

等价于

```java
player.addChatComponentMessage(new ChatComponentText(message));
```

### sendTranslation

在服务器中并不会存储多语言翻译内容
一般是客户端收到消息后再进行转换

```java
Message.sendTranslation(player, key, params...);
```

等价于

```java
player.addChatComponentMessage(new ChatComponentTranslation(key, params...));
```

## 总结

是不是比起原有的写法看着更舒服点呢。