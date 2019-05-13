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

修改Application继承MultiDexApplication

```
public class MyApplication extends MultiDexApplication { ... }
```

在Application的attachBaseContext内调用MultiDex.install\(this\)

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



