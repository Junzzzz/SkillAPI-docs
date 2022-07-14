# 静态技能

## 介绍

虽然本模组的主要特点是可以动态编辑技能效果、参数，但是依旧保留了静态技能的编写。但是预置的参数比如技能名、技能描述、冷却时间等等无法在游戏启动后修改。当然好处也就是相比技能效果来说{==稍微==}灵活一点。

## 创建一个简单的静态技能

!!! note "用法"

    - 使用`@StaticSkill`来标记静态技能类，并使该类继承`AbstractStaticSkill`
    - 在初始化函数`init`中设置技能的基本信息
    - 在函数`unleash`中添加你想要的技能效果


```java
@StaticSkill
public class DemoStaticSkill extends AbstractStaticSkill {
    @Override
    protected void init(StaticSkillBuilder builder) {
        builder.mana(1)       // 消耗魔力
                .cooldown(1); // 冷却时间
    }

    @Override
    public void unleash(EntityPlayer player, SkillExtraInfo extraInfo) {
        Message.send(player, "Unleash static skill");
    }
}
```

如上述代码所示，这样即可创建一个最简单的静态技能，其效果是消耗1点魔力向玩家发送一条文本`Unleash static skill`，冷却时间为1秒。

!!! hint "提示"

    所示代码中`Message.send()`方法为模组中编写的工具类，其效果等价于`player.addChatComponentMessage(new ChatComponentText("Unleash static skill"));`

    详见：[消息工具](./utils/message.md "消息工具")

## 技能初始化

技能初始化主要使用`init`方法，其中`builder`参数可以填写技能的基本参数。

```java
@Override
protected void init(StaticSkillBuilder builder) {
    ResourceLocation icon = new ResourceLocation("skillapi", "textures/icons/xxx.png");
    builder.mana(1)      // 消耗魔力
            .cooldown(1) // 冷却时间
            .icon(icon); // 技能图标
}
```

比起最简易版本，这里多了技能图标的添加，如果不添加将会使用技能名的首字母作为图标展示。

至于技能名与技能描述这两个内容存放在语言文件中，路径分别为：

| 路径                                          |   内容   |
| :-------------------------------------------- | :------: |
| `skill.static.<ModID>.<技能类名>`             |  技能名  |
| `skill.static.<ModID>.<技能类名>.description` | 技能描述 |


以上述代码以及中文语言为例：

```properties title="zh_CN.lang"
skill.static.skillapi.DemoStaticSkill=静态技能样例
skill.static.skillapi.DemoStaticSkill.description=技能描述样例
```

??? help "不知道语言文件存放路径？"

    默认路径为: `assets/<modId>/lang/<language>.lang`

    如本模组的中文语言翻译存放在: `assets/skillapi/lang/zh_CN.lang`

## 客户端参数传递

以传递玩家攻击目标为例：

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

上述代码获取了玩家镜头指向5格内的目标，并将其存放至`SkillExtraInfo`中。函数返回`false`表示释放失败，释放技能的数据包将不会发送。

!!! warning "注意"

    1. 由于客户端数据和服务器数据并不会在时间上保持一致。例如当玩家朝目标释放技能时，会向服务器发送释放技能的数据包，如果在{==服务器收到指令后==}再获取玩家的释放目标，会导致{==获取目标错误==}甚至无法获取目标的情况。
    2. `SkillExtraInfo`只能存放基本数据类型

    详见：[SkillExtraInfo](./other/skill-extra-info.md "技能额外参数传递")


## 释放条件

继续以攻击目标为例：

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

@Override
public void unleash(EntityPlayer player, SkillExtraInfo extraInfo) {
    EntityPlayer target = extraInfo.get("target");
    Message.send(target, "You got hit by a skill");
    Message.send(player, "Unleash static skill");
}
```

上述代码将会再服务端中执行，在`SkillExtraInfo`获取了目标ID后转换为`EntityPlayer`（这里设定的是只攻击玩家）重新存放至`SkillExtraInfo`中。

事实上不写也可以，可以将条件判断全部移至`effect`函数中，还可以少两次从Map中读写目标参数。保留这一方法主要是为了让代码更清晰一些，同时与动态技能效果的结构保持近似。

## 客户端释放

该部分主要用于存放在客户端中执行的内容，比如人物动作、技能粒子特效等等。该部分在通过`clientBeforeUnleash`后就会执行，即使在服务端中执行失败也会播放特效，默认动作为玩家挥动手中物品。

```java
@Override
public void clientUnleash(EntityPlayer player, SkillExtraInfo extraInfo) {
    // 默认动作为玩家挥动手中物品
    super.clientUnleash(player, extraInfo);
}
```

??? faq "设计思路"

    Q: 为什么不在服务端成功执行后再释放特效？

    A: 如果要达成这一条件不可避免的要受到网络延迟的影响，如果服务器运算量大的情况下会显得很“卡”

    Q: 如果在服务端上技能并没有执行成功，而客户端播放了动画特效不会很奇怪？

    A: 这是对流畅度的妥协，更何况如果在代码编写得当的情况下，很难出现在服务端执行失败的情况。

## 完整示例

```java
@StaticSkill
public class DemoStaticSkill extends AbstractStaticSkill {
    @Override
    protected void init(StaticSkillBuilder builder) {
        ResourceLocation icon = new ResourceLocation("skillapi", "textures/icons/xxx.png");
        builder.mana(1)      // 消耗魔力
                .cooldown(1) // 冷却时间
                .icon(icon); // 技能图标
    }

    @Override
    public boolean clientBeforeUnleash(EntityPlayer player, SkillExtraInfo extraInfo) {
        EntityLivingBase pointedLivingEntity = ClientUtils.getPointedLivingEntity(5);
        if (pointedLivingEntity instanceof EntityPlayer) {
            extraInfo.put("target", pointedLivingEntity.getEntityId());
            return true;
        }
        return false;
    }

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

    @Override
    public void unleash(EntityPlayer player, SkillExtraInfo extraInfo) {
        EntityPlayer target = extraInfo.get("target");
        Message.send(target, "You got hit by a skill");
        Message.send(player, "Unleash static skill");
    }

    @Override
    public void clientUnleash(EntityPlayer player, SkillExtraInfo extraInfo) {
        super.clientUnleash(player, extraInfo);
    }
}
```