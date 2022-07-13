# 创建一个技能效果

如下列代码所示

- 使用`@SkillEffect`来标记技能效果类
- 使用`@SkillParam`来标记可变参数，在配置界面将会增加该变量
- 在`canUnleash`函数中设置该技能效果执行前提
- `unleash`函数中的返回值`return true`表示执行成功，若返回`false`则一系列技能效果执行至此中断并且不会执行`afterUnleash`函数
- 在`afterUnleash`函数将在所有技能效果释放完成后执行，根据所需使用

```java
@SkillEffect
public class DemoSkillEffect extends AbstractSkillEffect {
    @SkillParam
    private int demoParam;

    @Override
    public boolean canUnleash(EntityPlayer player, EntityLivingBase target) {
        return target != null;
    }

    @Override
    public boolean unleash(EntityPlayer player, EntityLivingBase target) {
        player.addChatComponentMessage(new ChatComponentText("param: " + demoParam));
        return true;
    }

    @Override
    public void afterUnleash(EntityPlayer player, EntityLivingBase target) {
        // Default: none
    }
}
```