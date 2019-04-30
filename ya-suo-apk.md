# 压缩代码和资源

通过ProGuard提供。压缩规则在**build.gradle **文件的构件类型中添加 `proguardFiles getDefaultProguardFile('proguard-android.txt'),'proguard-rules.pro'` 引入，前一个压缩文件默认路径：sdk/tools/proguard；后一个跟**build.gradle**同一级

> `proguard-android-optimize.txt 还包括其他在字节码一级（方法内核方法外）执行分析的优化`

## 压缩代码

在**build.gradle **文件中相应的构建类型中启用`minifyEnable true `，在风味**flavor**中还可增加其特有压缩规则文件

> 开启Instant Run时会停用压缩，若想使用，则需在`minifyEnable true `后添加 `useProguard false `，只会删除未使用代码，不会混淆和优化

在该路径：build/outputs/mapping/构建类型  保存压缩后生成的文件：

* dump.txt       所有类文件的内部结构
* mapping.txt  原始和混淆后的类、方法、字段名称之间的转换
* seeds.txt       未混淆的类和成员
* usage.txt       移除的代码

### 自定义保留代码

参考混淆规则或添加注解。例如：-keep public void MyClass

### 解码混淆过的堆栈追踪

混淆后发布的apk堆栈追踪需要发布时生成的mapping.txt文件，再使用retrace.bat命令追踪，位于sdk/tools/proguard。

```
retrace.bat -verbose mapping.txt obfuscated_trace.txt（堆栈跟踪文件）
```

## 压缩资源

在**build.gradle **文件中相应的构建类型中启用` shrinkResource true` ，需开启代码压缩后才能使用。

会在build/outputs/mapping/构建类型生成resource.txt的诊断文件，包括移除了哪些资源以及哪些资源引用其他资源

> 不会移除values/文件夹定义的资源

### 自定义保留资源

创建 `res/raw/keep.xml` 包含`<resources>` 标记的文件，其中`tools:keep` 修饰保留的资源，`tools:discard` 修饰删除的资源，以逗号分隔多个资源。

```
<? xml version = "1.0" encoding = "utf-8" ?>

<resources xmlns:tools = "http://schemas.android.com/tools" 
 tools:keep = "@layout/l_used*_c,@layout /l_used_a,@layout/l_used_b*" 
 tools:discard = "@layout/unused2" />
 
```

> 可用\*作通配符。该文件不会被打包
>
> tools:discard 常用于构建变体时

### 严格引用检查

在`keep.xml`文件中将`shrinkMode`设置为`strict ` 

```
<resources xmlns:tools = "http://schemas.android.com/tools" 
shrinkMode="strict"/>
```

> 使用Resource.getIdentifier\(\) 获取的资源或在raw/ 中的所有字符串常量 匹配的资源，在严格模式下需要手动添加tools:keep 保留

### 移除未使用的备用资源

在**build.gradle **文件中`defaultConfig` 中启用 `resConfigs` `支持的资源`

例如语言支持en 和 zh ：

```
android{
  defaultConfig {
    ...
    resConfigs "en","zh"
   }
}
```

### 合并重复资源

主要在构建顺序中有完全相同文件时（资源名称，类型，限定符）且合并遵循构建优先顺序。若重复资源出现在同一源集，则会报错





