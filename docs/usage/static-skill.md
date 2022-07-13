# 创建一个静态技能

如下列代码所示
- 使用`@StaticSkill`来标记静态技能类
- 在初始化函数`init`中设置技能的基本信息
- 在效果函数中添加你所要产生的效果，示例是向玩家发送一段文本

```java
@StaticSkill
public class DemoStaticSkill extends AbstractStaticSkill {
    @Override
    protected void init(StaticSkillBuilder builder) {
        builder.mana(1)
                .cooldown(1)
                .charge(1);
    }

    @Override
    public void effect(EntityPlayer player, EntityLivingBase target) {
        player.addChatComponentMessage(new ChatComponentText("Static Skill!"));
    }
}
```