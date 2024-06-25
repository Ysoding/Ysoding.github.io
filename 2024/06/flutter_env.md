
#flutter  
[在中国网络环境下使用 Flutter | Flutter 中文文档 - Flutter 中文开发者网站 - Flutter](https://docs.flutter.cn/community/china)

#### 项目停在Running Gradle task 'assembleDebug'

基本上就是网络问题  
[Flutter App stuck at "Running Gradle task 'assembleDebug'... " - Stack Overflow](https://stackoverflow.com/questions/59516408/flutter-app-stuck-at-running-gradle-task-assembledebug)  
可能在下载gradle 可以查看下 `project/andriod/gradle/gradle-wrapper.properties` 文件中的

```gradle
distributionUrl=https\://services.gradle.org/distributions/gradle-7.6.3-all.zip
```

默认目录在 `C:\Users\Administrator\.gradle\wrapper\dists\gradle-7.6.3-all`

也可以手动修改

```gradle
distributionUrl=file\:/C:/Users/{yourname}/.gradle/wrapper/dists/gradle-7.5-all/gradle-7.5-all.zip
```

### flutter run 出错

```
flutter FAILURE: Build failed with an exception.* What went wrong:Could not determine the dependencies of task ':app:compileDebugJavaWithJavac'.> Failed to find Build Tools revision 30.0.3
```

需要安装Andriod SDK Build-Tools 30.0.3版本Languages/Android SDK/ SDK Tools里记得勾选Show Package Details
