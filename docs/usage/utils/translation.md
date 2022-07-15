# Translation 语言转换工具

文本内容一般都会存放在对应语言的lang文件下，并使用`I18n.format()`这个方法进行翻译。

但是`I18n`工具类被限定在了客户端使用，在服务端加载类的时候会被`ASM`裁剪掉，如果在服务端内调用该方法会直接抛出`ClassNotFoundException`的异常并崩溃。

由于模组中服务端和客户端的代码都是杂糅在一个文件中的，难免有时候没有分清使用的地方导致服务端线程调用了该代码而崩溃，在后来又意外发现了`StatCollector.translateToLocalFormatted`这个通用的语言文本转化工具后，发现这个方法可以在所有环境下使用，不过在服务端时只会提取英文罢了，但是由于方法太长，类名取的有点怪，所以重新包装成了一个新的工具类`Translation`。

## 使用

该工具类调用与`I18n`一致，或者说和`String.format()`的用法时一样的，区别就是中间的`key`填写的是语言文件中的路径名，如`skill.static.skillapi.DemoStaticSkill`。

```java
Translation.format(key, params...);
```