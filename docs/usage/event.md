# 监听事件

免去了原有的手动监听函数设置，并且不需要再去手动区分`FML`还是`Minecraft`事件，只需要在类上添加`@SkillEvent`即可，其余部分与原有的使用一致

```java
@SkillEvent
public class DemoEvents {
    @SubscribeEvent
    public void onConstructing(EntityEvent.EntityConstructing event) {
        // TODO
    }

    @SubscribeEvent
    public void onPlayerRespawn(PlayerEvent.PlayerRespawnEvent event) {
        // TODO
    }
    
    // ...
}
```