# 注解处理器

模组里使用了许多注解来对类进行初始化和加载，比如`@SkillEvent`、`@SkillPacket`、`@StaticSkill`等等

开发人员也可以用本模组中的注解处理器来完成自定义的功能

## 创建一个注解处理器

以`SkillPacket`为例：

### 创建一个注解

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface SkillPacket {
    Class<? extends PacketSerializer<? extends AbstractPacket>> serializer() default DefaultPacketSerializer.class;
}

```

### 创建处理器

```java
@SkillAnnotation
public final class SkillPacketAnnotationImpl implements SkillAnnotationRegister<SkillPacket> {
    @Override
    @SuppressWarnings("unchecked")
    public void register(Class<?> target, SkillPacket annotation, ModMetadata mod) {
        if (!AbstractPacket.class.isAssignableFrom(target)) {
            throw new SkillRuntimeException("Skill packet registration failed. Class: %s", target.getName());
        }
        Packet.register((Class<? extends AbstractPacket>) target, annotation);
    }
}
```

处理器中调用了`Packet.register()`来完成包的注册，开发者可以根据自己的需要来编写自己的功能。

---

这样注解处理器就完成了，至于扫描、调用什么的就交给本模组吧🌟~