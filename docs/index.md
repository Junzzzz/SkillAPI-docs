# SkillAPI

一个修改自静态技能模组[GotoLink/SkillAPI](https://github.com/GotoLink/SkillAPI)的动态技能编辑器。用户可根据预置的基本技能效果进行拼装、填写参数，设置技能名、描述内容、冷却时间等等基本参数来完成一个技能的构建。

## 使用方法

添加该MOD作为依赖，并在初始化函数中添加以下内容

```java
@Mod(modid = "demo", name = "Demo", useMetadata = true, dependencies = "required-after:skillapi")
public final class DemoMod {
    @EventHandler
    public void preInit(FMLPreInitializationEvent event) {
        // 扫面全部包
        SkillApi.preInit(event);

        // 或者 限定扫描范围
        SkillApi.preInit(event, "demo");
    }

    @EventHandler
    public void init(FMLInitializationEvent event) {
        SkillApi.init(event);
    }
}
```

然后就能快乐的使用本MOD提供的各种<del>奇怪</del>接口了