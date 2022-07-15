# 数据包

该部分对FML提供的`Packet`接收事件做了封装，无需进行繁琐的定义，更不需要进行手动变量序列化/反序列化。

## 定义一个简单数据包

!!! note "用法"

    - 使用`@SkillPacket`来标记数据包类，并使该类继承`AbstractPacket`
    - 在`run`函数中填写接收数据包后所执行的内容

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@SkillPacket
public class DemoPacket extends AbstractPacket {
    private String testString;

    @Override
    protected void run(EntityPlayer player, Side from) {
        Message.send(player, testString)
    }
}
```

默认的序列化工具是`Jackson`，所有对象将会转换成`Json`格式文本进行传输，因此需要定义一个空的构造器和`Getter/Setter`方法。示例使用了`lombok`的注解去自动生成了上述内容。

## 自定义序列化器

以下代码为本模组的默认序列化器，可参照该内容自定义特殊的序列化器

```java
public class JsonPacketSerializer implements PacketSerializer<AbstractPacket> {
    @Override
    public void serialize(AbstractPacket packet, ByteBuf buffer) throws Exception {
        PacketSerializer.writeString(buffer, JsonUtils.getMapper().writeValueAsString(packet));
    }

    @Override
    public AbstractPacket deserialize(Class<?> packetClass, ByteBuf buffer) throws Exception {
        return (AbstractPacket) JsonUtils.getMapper().readValue(PacketSerializer.readString(buffer), packetClass);
    }
}
```

使用时只需要在注解中填写该类即可

```java
@SkillPacket(serializer = JsonPacketSerializer.class)
```