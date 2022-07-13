# 自定义序列化器

以下代码为本MOD的默认序列化器，可参照该内容自定义特殊的序列化器

使用时只需要在注解中填写该类即可

如：`@SkillPacket(serializer = JsonPacketSerializer.class)`

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