---
title: 抓重点学Gradle
date: 2017-09-06 01:29:42
categories:

- Gradle

tags:

- Gradle
- Groovy

photos:
 - https://cdn-images-1.medium.com/max/1200/1*gImAplWne6yxtDytygjiYw.png
---

## 前言

网上关于Gradle的教程很多，但很多都是以“面”切入— 通过大量讲解其用法及其API分类来阐述。但Gradle API使用技巧众多，API更是成千上百，臣妾记不住呀。个人深信，技术之道与练功之道是一致的，"想要旷世武功，先练内功"。本文尝试以“点”切入，阐述一下学习Gradle的门路。

<!--more-->

## 为什么使用Gradle

所谓构建，简单来说就是根据不同的输入信息，执行不同的任务，得到不同的输出的过程。而构建工具就是把这些过程自动化、管理执行不同的任务来满足不同需求。常见的构建工具有Ant、maven等。但它们都有一些缺点：

- Ant的缺点

  依赖管理较弱，很容易变臃肿， XML作为构建脚本

- Maven的缺点

  比较精简，但限制性太多，编写插件扩展麻烦，XML作为构建脚本。

而Gradle可以说是集大成者，比Ant 语法精简， 比Maven灵活，抛弃了XML的繁琐配置，极其强大的依赖管理，基于Groovy动态语言而使得自定义行为十分方便。

关于为什么使用Gradle这个问题，官方也作出了解释：

![why-gradle](http://ww4.sinaimg.cn/mw1024/0063dNt1jw1f7j8imyrwmj31kw0ayju4.jpg)

简单翻译总结，就是使用Gradle很方便很灵活。

好吧，前面的都是“废话”，作为Android开发者，你不用Gradle编译试试...

## Gradle

要学好Gradle，最重要的是学会**查文档、查文档、查文档**...重要的事情说三遍！

### Groovy

由于Gradle是基于Groovy，所以要学习Gradle，必须要掌握一定的Groovy基础。

#### Groovy是什么

简单来说，Groovy是拓展了Java语言的一种动态语言，可以作为Java平台的脚本语言使用，拥有类似[Python](https://zh.wikipedia.org/wiki/Python)、[Ruby](https://zh.wikipedia.org/wiki/Ruby)和[Smalltalk](https://zh.wikipedia.org/wiki/Smalltalk)中的一些特性。

#### Groovy学习

当然，第一步肯定是配置环境，关于Groovy的环境搭建，具体可以参见[Groovy官网](http://www.groovy-lang.org/download.html#gvm),特别简单，不在此重复说明。然后，创建一个test.groovy文件，里边只有一行代码：

```python
println  "hello groovy"
```

运行一下：

 ![hello](http://ww1.sinaimg.cn/mw690/0063dNt1jw1f7j8ii4hmrj30ex01saa4.jpg)

**是不是感觉和python好类似**

作为一门语言，Groovy是复杂的。要深入研究的话，不会比学任一语言要简单。但是作为一名普通开发者，个人觉得掌握其中一点—**Closure(闭包)**，足矣。

- Groovy基础

  为啥这么说，首先，Groovy是完全兼容Java语法的，在学习前期，你完全可以使用Java语法代替Groovy语法编写Groovy程序，毕竟是Groovy程序，随着后期对于Groovy越来越熟悉，自然而然就会转回更舒服的Groovy语法；其次，Groovy语法真心不难，举几个常见的特殊语法：

  - Groovy中支持动态类型，即**定义变量的时候可以不指定其类型，同一个变量可以反复赋值，而且可以是不同类型的变量**。这是动态语言和静态语言比较重要的区别

    譬如，我们把上面的test.groovy修改为：

    ```python
    def test=2
    test='tt'
    println  "${test}"
    ```

    运行一下：
      ![tt](http://ww2.sinaimg.cn/mw690/0063dNt1jw1f7j8ikvqwsj30g201zwej.jpg)

  但是这种语法早在别的动态语言，如Python、Ruby中出现过了，估计不知道的程序员几乎没有吧。

  - 在上面例子中，最后输出的语句`println  "${test}"`其实包含了Groovy两个语法糖

    - Groovy中函数调用的时候还可以不加括号
    - 如果字符中有$号的话，则它会把**$表达式**先求值

    其实上述例子完全可以写成这样：

    ```java
    def test=2
    test='tt'
    //println  "${test}"
    println(test)
    ```

  还有一些别的语法，如`可以不用分号结尾`、`函数的返回值也可以是无类型`等，但这些稍微查一下[GroovyAPI文档](http://www.groovy-lang.org/api.html)或google一下就懂了,没啥技术门槛。

- **Closure(闭包)**

  说了这么多，是时候到主角—**Closure(闭包)**出场了。**Closure(闭包)**是Groovy中函数式编程的最好体现。所谓`函数式编程`跟我们熟知的`面向过程编程`、`面向对象编程`一样也是一种["编程范式"](https://zh.wikipedia.org/wiki/%E7%BC%96%E7%A8%8B%E8%8C%83%E5%9E%8B)，也就是如何编写程序的方法论。函数式编程最鲜明的特点是`函数是“第一等公民“`，所谓["第一等公民"](https://zh.wikipedia.org/wiki/%E7%AC%AC%E4%B8%80%E9%A1%9E%E7%89%A9%E4%BB%B6)，指的是函数与其他数据类型一样，处于平等地位，可以赋值给其他变量，也可以作为参数，传入另一个函数，或者作为别的函数的返回值。

  在Groovy中，闭包表示一段可以执行的代码块，定义如下：

  ```python
  { [closureParameters -> ] statements }
  ```

  其中，**闭包必须使用花括号括起来，`[closureParameters -> ] `表示闭包参数列表，可以为空，但其为空时，闭包默认会隐含一个参数`it`,`statements`是闭包需要执行的代码，最后一行代码是返回值**。

  定义好闭包后，可以通过`closure(参数)`或者`closure.call(参数)`调用

  举个例子：

  ```groovy
  def code={123}
  println  code.call(1)
  println  code()
  输出：
  123
  123
  
  def add = { int x,y-> x+y } 
  println add(3,4) 
  输出：
  7
  
  def isEven = { it%2 == 0 }   
  println isEven(7)
  输出：
  false
  ```

  **Closure使用注意点**

  - 省略圆括号

    在Groovy中我们要遍历一个List，一般做法是这样的：

    ```groovy
    def list = [1,2,3,4,5] 
    list.each{  
      println it
    }
    ```

  我们查一下它的API文档，是这样的

   ![method](http://ww2.sinaimg.cn/mw1024/0063dNt1jw1f7j8im4wvqj30n70fh75t.jpg)

  **What the hell!!!**完全跟上面写法对应不上呀，实际上是因为，当函数的最后一个参数是闭包的话，可以省略圆括号，所以上面完整写法应该是这样的：

  ```groovy
  def list = [1,2,3,4,5] 
  list.each({
    println it
  })
  ```

  还是不对呀，明明是两个参数呀，怎么只传了一个。让我们查一下文档，这个方法定义在[DefaultGroovyMethods](http://docs.groovy-lang.org/latest/html/api/org/codehaus/groovy/runtime/DefaultGroovyMethods.html)中，看一下它的类说明：

  ```python
  This class defines new groovy methods which appear on normal JDK classes inside the Groovy environment. Static methods are used with the first parameter being the destination class, i.e. public static String reverse(String self) provides a reverse() method for String.
  ```

  恍然大悟，原来这个类定义了Groovy环境下一些类支持的方法，而其中**定义的静态方法第一个参数仅仅是为了标示目标类**，所以是不需要传递的。

### Gradle

#### Gradle是什么

	Gradle是基于Groovy定义了一套DSL，所谓DSL(领域专用语言)，就是专门针对某一特定问题的计算机语言。而Gradle我们可以认为是经过“定制”的Groovy，专门用于项目构建的语言。

#### Gradle学习— [Task](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html)，[Project](https://docs.gradle.org/current/javadoc/org/gradle/api/Project.html),[Plugin](https://docs.gradle.org/current/userguide/plugins.html)

虽然使用Android Studio开发的话，Gradle环境在IDE会自动下载集成，但要学好Gradle，建议还是自己部署一下环境。从[Gradle官网](http://gradle.org/)下载gradle后，配置一下环境变量。Gradle的环境就搭建起来了。

和Groovy同理，要深入Gradle的话，要学的东西也不会少，但是作为普通的开发者，个人觉得把三个概念搞懂就可以了：[Task](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html)，[Project](https://docs.gradle.org/current/javadoc/org/gradle/api/Project.html),[Plugin](https://docs.gradle.org/current/userguide/plugins.html)

- **Project**

  在Gradle中，每一个project，Gradle都会创建一个Project对象，并将这个对象与构建脚本相关联。也就是说，**Project对象与`build.gradle`是一对一的关系**，所以你在`build.gradle`写的每一个配置其实就是它对应的[Project](https://docs.gradle.org/current/javadoc/org/gradle/api/Project.html)对象的一个方法或者一个变量值，譬如说我们配置项目依赖：

  ```python
  dependencies {
          classpath 'com.android.tools.build:gradle:1.5.0'
      }
  ```

  它其实对应Project对象`void dependencies(Closure configureClosure);`方法。

  从面向对象的角度去理解Gradle，是不是觉得容易多了。


- **Task**

  Task表示一些需要执行的构建任务，比如说lint检查任务。定义一个Task可以这样写：

  ```groovy
  task hello << {
      println "hello"
  }
  ```

  看起来挺莫名其妙的吧,还是从面向对象角度去把上面代码还原一下：

  ```groovy
  task("hello").leftShift({
     println "hello"
  })
  ```

  1. 定义一个task相当于调用了Project对象的task方法
  2. 任务名是task方法的参数名
  3. `<< `是Groovy的[运算符重载](http://groovy.codehaus.org/Operator+Overloading)，在Groovy中，其实就是`leftShift`方法,`leftShift`方法 等价于 `doLast`，`doLast` 是gradle提供访问task任务的一个API，类似的还有 `doFirst`，当一个task被执行的时候，可以通过 `doFirst` 和 `doLast` 向task中动态添加操作。`doFirst` /`doLast` 会在task本身被执行之前/之后才会被执行
  4. `leftShift`方法接收的参数是一个`Closure`

  从上述分析可知，其实**一个Task就是一个标准的Groovy函数调用**

- **Plugin**

  Gradle是一个框架，作为框架，它负责定义流程和规则。而具体的编译工作则是通过插件的方式来完成的。比如**编译Java有Java插件，编译Groovy有Groovy插件，编译Android APP有Android APP插件，编译Android Library有Android Library插件**。简单来说，插件就是一系列任务的集合，主要作用是把一些重复利用的逻辑打包，这样就可以在不同的项目中可以重复的使用。要应用插件，可以通过引入依赖的方式添加，举个例子，要引入Android APP插件，就需要在build.gradle引用Android APP插件：

  ```groovy
  buildscript {
      repositories {
          jcenter()//表示编译过程中依赖的仓库
      }
      dependencies {
        //依赖android开发的gradle插件,groupId:artifactId:version
          classpath 'com.android.tools.build:gradle:1.5.0'
      }
  }
  //应用插件
  apply plugin: 'com.android.application'
  
  //配置插件属性
  android {
      compileSdkVersion 23
      buildToolsVersion "23.0.2"
      defaultConfig {
          applicationId "com.test"
          versionCode 1
          versionName "1.0.0"
      }
      buildTypes {
           release {
              minifyEnabled false
              proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
          }
      }
  }
  ```

#### Gradle进阶— 自定义插件

	Gradle允许开发者自定义插件来实现项目一些特殊的构建需求。譬如说：动态注入代码。我们可以直接在build.gradle定义自己的插件，但一般很少这么干，毕竟插件更多是为了项目复用的。更多情况，Gradle插件是以独立插件工程的形式开发的。下面以一个简单例子来介绍一下开发一个独立Gradle插件基本步骤：

假设现在我们开发一个直接输出输入内容的插件：

1. 建立插件工程

   由于Gradle是基于Groovy开发的，所以我们建立的必然是Groovy工程，项目目录结构如下

    ![plugin](http://ww1.sinaimg.cn/mw1024/0063dNt1jw1f7j8ilmjipj309q0bngm7.jpg)

   build.gradle如下：

   ```groovy
   apply plugin: 'groovy'
   apply plugin: 'maven'
   version = '1.0.0'//插件version
   group = 'com.adison.gradleplugin'//插件groupId
   archivesBaseName = 'TestPlugin'//插件artifactId
   
   dependencies {
       compile gradleApi()//gradle sdk
       compile localGroovy()//Groovy sdk
   }
   
   //发布配置
   uploadArchives {
       repositories.mavenDeployer {
         //发布到本地
           repository(url: 'file:repo/')
       }
   }
   ```

2. 编写插件

   - 实现Plugin接口

     ```Groovy
     package com.test
     import org.gradle.api.Plugin
     import org.gradle.api.Project
     class TestPlugin implements Plugin<Project> {
         @Override
         void apply(Project target) {
         }
     }
     ```

     这是插件的入口类

   - 定义输入信息扩展类

     ```groovy
     package com.test
     class TestExtension {
         String message;
     }
     ```

   - 定义插件id

     Gradle定义插件id需要建立这样一个目录结构：**/resources/META-INF/gradle-plugins**，然后在其底下创建一个properties文件：`print-message.properties`文件名即是插件id，也是`apply plugin: 'xxx'`使用的名字，在文件中输入插件入口类：

     ```groovy
     implementation-class=com.test.TestPlugin
     ```

   - 完善插件

     ```groovy
     package com.test
     
     import org.gradle.api.Plugin
     import org.gradle.api.Project
     
     class TestPlugin implements Plugin<Project> {
     
         public static final String EXTENSION_NAME = "printMessage";//扩展别名
     
         @Override
         void apply(Project target) {
           //创建扩展
        TestExtension extension=  target.extensions.create(EXTENSION_NAME,TestExtension)
             target.task("printMessage")<< {
                 println extension.message
             }
         }
     }
     ```

3. 发布插件

   命令行执行：

   ```groovy
   ./gradlew clean uploadArchives
   ```

   会在当前repo目录生成插件文件

4. 插件使用

   - 插件文件导入项目，譬如导入项`根目录/gradleplugin`

   - 引入本地仓库

     ```groovy
     buildscript {
         repositories {
             jcenter()
             mavenCentral()
             //本地仓库
             maven {
                 url 'gradleplugin'
             }
         }
         dependencies {
             classpath 'com.android.tools.build:gradle:1.5.0'
        		//自定义插件
             classpath 'com.adison.gradleplugin:TestPlugin:1.0.0'
         }
     }
     ```

   - 应用插件

     ```groovy
     apply plugin: 'print-message'
     printMessage{
         message="test"
     }
     ```

     ```groovy
     命令行输入：
     ./gradlew printMessage
     输出：
     test
     ```

   举这个列子只是为了描述了一下自定义Gradle插件的流程，结合Groovy和Gradle内建的Task，可以实现更复杂实用的插件。

## Android Gradle使用

从上述可以得知，Android其实就是写了两个插件：`com.android.application`和`com.android.library`,应用这两个插件就可以实现Android APP和Android Library的构建了，所以也没啥好说的，不知道怎么配置？查文档：[Gradle Plugin User Guide](http://tools.android.com/tech-docs/new-build-system/user-guide)

这里简单分享几个有用的经验：

- 自定义模块结构

  项目还是是Eclipse结构，想保留原有目录结构的同时导入到AS开发，可以自己手动生成build.gradle,配置项目目录结构：

  ```groovy
  android {
   	....
      sourceSets {
          main {
              manifest.srcFile 'AndroidManifest.xml'
              java.srcDirs = ['src']
              resources.srcDirs = ['src']
              aidl.srcDirs = ['src']
              renderscript.srcDirs = ['src']
              res.srcDirs = ['res']
              assets.srcDirs = ['assets']
              jniLibs.srcDirs = ['libs']
          }
      }
  }
  ```

- 由于项目协作, 以及跨平台编译, 在任何时候, 你都应该使用 Gradle Wrapper 而非本机安装Gradle

  - Gradle Wrapper是Gradle的封装。即便你的机器上没有安装Gradle，使用Gradle Wrapper也可以执行Gradle的构建工作。
  - Gradle Wrapper在项目新创建是会自动生成，使用时会自动下载对应版本的Gradle。
  - 版本一致可以避免很多因为协作或跨平台的问题。

 Gradle Wrapper使用很简单：

```python
  使用gradle：
  gradle tasks
```

```python
  使用gradle wrapper
  ./gradlew tasks
```

- Build Variants(构建变种版本)

  **Build Type + Product Flavor = Build Variant**。

  Android通过Build Variants可以很方便构建不同的版本，满足各种版本需求。

  - Build Type: 构建类型，如debug，release，一般用于构建不同类型提供团队内部使用
  - Product Flavor : 定制类型，一般用于区分渠道打包

  ```groovy
  android {
      ...
      buildTypes {
          debug{
  		  ...
          }
          release {
             ...
          }
      }
      productFlavors {
          flavor1 {
            ...
          }
          flavor2 {
           ...
          }
      }
  }
  ```

  如上面配置，则会生成4个*Build Variant*

  - Flavor1 - debug
  - Flavor1 - release
  - Flavor2 - debug
  - Flavor2 - release

## 小结

至此，相信大家对gradle已经有一定认识。我在上述并没有对一些细节展开论述，而仅仅对我个人认为比较重要的点进行了阐述，是因为我觉得对于Gradle的学习，掌握理解其重要的概念远比记住其API重要。万变不离其宗，掌握了“点”，“面”只是时间的问题。最后，再重申一句，**学会查文档真很重要**。

## 参考文章

http://www.infoq.com/cn/articles/android-in-depth-gradle

http://benweizhu.github.io/blog/2015/03/31/deep-into-gradle-in-action-6/