限定单个DEX文件内引用方法数为：65536

### 处理方法

在模块级**build.gradle**文件内声明

```
android {
    defaultConfig {
        ...

        multiDexEnabled true
    }
    ...
}
```

* android 5.0（api 21）之前，添加分包库（multidex）依赖 

```
dependencies {
  compile 'com.android.support:multidex:1.0.3'
}
```

第一种方法是修改Application继承MultiDexApplication

```
public class MyApplication extends MultiDexApplication { ... }
```

第二种方法在Application的attachBaseContext内调用MultiDex.install\(this\)

```
public class MyApplication extends Application {
  @Override
  protected void attachBaseContext(Context base) {
     super.attachBaseContext(base);
     MultiDex.install(this);
  }
}
```

* android 5.0（api 21）之后，直接声明分包即可

### 出现 java.lang.NoClassDefFoundError 异常

应用启动时需要的类没有在主DEX文件中，则会出现改异常。通过配置**multiDexKeepFile** 属性解决

* 在模块级**build.gradle** 同级目录下新建一个txt文本（例：multidex-config.txt），把缺失的类以全路径形式，添加到文本内

```
com/example/MyClass.class// 普通类配置方式
com/example/MyOtherClass$InnerClass.class// 内部类配置方式
```

* 在模块级**build.gradle **内对应的构建中声明

```
android {
    buildTypes {
        release {
            multiDexKeepFile file('multidex-config.txt')
            ...
        }
    }
}
```

> 若有混淆，则需要配置**multiDexKeepProguard**属性，用法参照上面，只是新建pro文件（例：multidex-config.pro）。混淆规则同普通混淆一样
>
> ```
> -keep class com.example.MyClass
> -keep class com.example.MyClassToo
>   ...
> -keep class com.example.** { *; }
>
>
> android {
>     buildTypes {
>         release {
>             multiDexKeepProguard('multidex-config.pro')
>             ...
>         }
>     }
> }
> ```



