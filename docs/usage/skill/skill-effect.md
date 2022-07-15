# 技能效果

## 介绍

技能效果是组合成动态技能的基本单元，每个效果中的参数可自由调整从而更好的组合成不同的技能。

## 创建一个简单的技能效果

!!! note "用法"

    - 使用`@SkillEffect`来标记技能效果类，并使该类继承`AbstractSkillEffect`
    - 在`unleash`方法中添加你想要实现的效果

以向玩家发送消息为例：

```java
@SkillEffect
public class DemoSkillEffect extends AbstractSkillEffect {
    @Override
    public void unleash(EntityPlayer player, SkillExtraInfo extraInfo) {
        Message.send(player, "Demo skill effect");
    }
}
```

这样一个简单的技能效果就创建完成了

## 添加参数

技能的参数可以在管理员编写技能时填写，让动态技能的编写更加灵活。

添加技能参数也非常简单，只要在技能效果类中的成员属性上加上`@SkillParam`即可，我们对上面的示例做一些改进：

```java
@SkillEffect
public class DemoSkillEffect extends AbstractSkillEffect {
    @SkillParam
    private String message;

    @Override
    public void unleash(EntityPlayer player, SkillExtraInfo extraInfo) {
        Message.send(player, this.message);
    }
}
```

!!! warning "注意"

    目前技能参数只支持Java基本类型：`int`、`float`、`double`、`boolean`、`String`

    当然，它们的包装类也是OK的

## 添加释放条件

添加技能释放条件与静态技能中所述类似，这里只作简单描述。详见：[静态技能](./static-skill
.md#客户端参数传递)

### 客户端

在`clientBeforeUnleash`函数中可以传递额外参数至服务器，同时也可以用于设置技能释放前置条件。

```java
@Override
public boolean clientBeforeUnleash(EntityPlayer player, SkillExtraInfo extraInfo) {
    EntityLivingBase pointedLivingEntity = ClientUtils.getPointedLivingEntity(5);
    if (pointedLivingEntity instanceof EntityPlayer) {
        extraInfo.put("target", pointedLivingEntity.getEntityId());
        return true;
    }
    return false;
}
```

### 服务端

通过添加`canUnleash`方法可以设置技能效果执行的前置条件。当一个技能内包含的所有效果前置条件通过时才会释放该技能。

```java
@Override
public boolean canUnleash(EntityPlayer player, SkillExtraInfo extraInfo) {
    Integer targetId = extraInfo.get("target");
    Entity target = player.getEntityWorld().getEntityByID(targetId);
    if (target instanceof EntityPlayer) {
        extraInfo.replace("target", target);
        return true;
    }
    return false;
}
```

## 添加释放技能后置方法

之所以要设置这么一个方法主要还是因为想写一个吸血效果，但是由于目标收到的伤害可能拆分到了各个技能效果中，所以要得到玩家的受到的伤害量必须在所有效果执行完成后才能取得，因此也有了这个方法。

```java
@Override
public void afterUnleash(EntityPlayer player, SkillExtraInfo extraInfo) {
    EntityPlayer target = extraInfo.get("target");
    float damage = target.prevHealth - target.getHealth();
    player.heal(damage);
}
```

希望这个方法能意外的起到作用。

??? summary "一些想法"

    其实可以给技能效果设置优先级，或者说按照技能的添加顺序来执行，不过这里还是偷了个懒，直接加个函数就完事了

## 语言路径

| 路径                                                 |      内容      |
| :--------------------------------------------------- | :------------: |
| `skill.effect.<ModID>.<技能效果类名>`                |   技能效果名   |
| `skill.effect.<ModID>.<技能效果类名>.<技能效果参数>` | 技能效果参数名 |

