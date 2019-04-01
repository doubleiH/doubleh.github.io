---
layout: post
title:  Vue、springboot环境整合
date:   2019-02-20 11:40:00 +0800
categories: Vue
tag:  vue、springboot
excerpt: vue与springboot整合
---


* content
{:toc}


---------------------------------

#### 一 、SpringBoot，vue前后端分离开发，一起部署
>   新建springboot的web工程，默认会在resources目录下生成static以及templates文件夹
    templates文件用于存放后端渲染的模板(采用前后端分离的方式，因此该文件夹就没有用了)   
    static文件夹就是存放静态文件的地方

springboot 项目中pom.xml`使用了maven-resources-plugin插件，将vue工程npm run build之后的dist文件夹下的文件拷贝到static目录下`

```
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <artifactId>maven-resources-plugin</artifactId>
                <executions>
                    <execution>
                        <id>copy Vue.js frontend content</id>
                        <phase>generate-resources</phase>
                        <goals>
                            <goal>copy-resources</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>src/main/resources/static</outputDirectory>
                            <overwrite>true</overwrite>
                            <resources>
                                <resource>
                                    <!--<directory>${basedir}/vue-demo/dist</directory>-->
                                    <directory>F:/03_Source/test/my-first-vue/dist</directory>
                                    <includes>
                                        <include>static/</include>
                                        <include>index.html</include>
                                    </includes>
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

        </plugins>
    </build>
```
这里vue工程名为my-first-vue，
对于vue工程，首先执行npm run build生成静态文件，之后对maven工程执行mvn process-resources，就可以一键拷贝


参考文档：https://blog.csdn.net/weixin_33826268/article/details/86861188


#### 二 、直接搭建springboot和vue环境

[转载：https://blog.csdn.net/angelhua520/article/details/83451683]

[toc]
开发环境
```
    IDEA V2018.5
    npm v6.4.x
    vue-cli v2.9.x
```
创建项目

    `IDEA > Create New Project > Gradle`
    ![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-ecac4200.png'| prepend: site.baseurl  }})


`此处的 Frameworks全部取消勾选,接着就一直 Next，提示填写的地方填写，不需要填写的就用默认配置就 ok。`

`项目创建成功，项目结构应该是这样的。`
![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-b63df650.png" | prepend: site.baseurl  }})
创建后台模块

  ```
  ▼ 项目 > 右键 > New > Module
  ```

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-a5e261b9.png" | prepend: site.baseurl  }})

  ```
   ▼ Spring Initializr
  ```

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-8ced34d5.png" | prepend: site.baseurl  }})

  ```
  ▼ 这一步按照正常项目信息填写。
    注意：Type 属性需要选择 Gradle Config
  ```

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-e22a0174.png" | prepend: site.baseurl  }})

  ```
  ▼ 按照自己需要的依赖进行选取
  ```

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-864ed9a5.png" | prepend: site.baseurl  }})

  ```
  ▼ 下一步默认，然后 Finish。之后需要等待 Gradle 进行模块的初始化，如果是首次创建 SpringBoot 项目，此处初始化可能需要比较长的时间，需要耐心和良好的网络环境。
  ```

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-af6c23c5.png" | prepend: site.baseurl  }})

   `成功后是如上所示结构，之后我们需要手动完善一些结构和配置`

配置后台模块

一、配置模块依赖

`在根项目的settings.gradle文件中增加 include 'server'`

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-b4e6e288.png" | prepend: site.baseurl  }})

二、完善 server 项目工程结构

`server 目录默认创建出来只有一个build.gradle文件，我们需要手工完善项目结构`

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-8638713a.png" | prepend: site.baseurl  }})

```
    如上图所示，可以看到，我们其实建立出来的就是一个标准的 java 工程结构。

        src 目录下游 main 目录，正常情况此处src 下还需要有 test 目录，这里演示就略过了。
        main 下面创建 java 和 resources 。
        java 下面创建对应的包结构和 SpringBoot 的启动类，代码如图的右边区域，非初次接触 SpringBoot 对这个代码应该不陌生。
        resources 目录下暂时就只创建配置文件application.yml。
```
三、配置后台数据源

`加入了数据源的 SpringBoot 项目需要在application.yml中配置数据源信息，否则项目会启动失败。`
 ![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-711331f6.png" | prepend: site.baseurl  }})

```
  # 配置项目的数据源
  spring:
    datasource:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://localhost:3306/test?characterEncoding=UTF-8&useUnicode=true&useSSL=false
      password: root
      username: root
```
到此后台模块创建配置完成
创建前端模块

  `▼ 项目 > 右键 > New > Module`

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-732cb46f.png" | prepend: site.baseurl  }})

  `前端模块这里选择 Gradle，然后注意右边 Frameworks 这里需要取消 java 的勾选`

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-ab3a2a2a.png" | prepend: site.baseurl  }})

  `这一步只需要填写一下 ArtifactId，名字可以自己定义，不一定要照搬我的命名习惯。然后一直下一步 直到 Finish。`

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-020ecb81.png" | prepend: site.baseurl  }})

    模块完成初始化后，结构如上图所示，多出一个 client 目录，也是只有一个 build.gradle 文件。由于这个我们之间选择的新增 Gradle 的模块，所以在 settings.gradle 文件中，工具自动帮我们把 include 补全了。

    ▼▼▼接下来就是比较关键的步骤了,敲黑板.....▼▼▼

我们需要将 client 模块初始化为 vue 项目。这里我们用到的 vue 提供的vue-cli模块。

    关于 vue-cli 的安装请参考百度。

初始化 vue 项目

打开 idea 底部的 Terminal
![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-f661c72b.png" | prepend: site.baseurl  }})

    在控制台输入：
    vue init webpack client

注意： 此处的项目名称一定需要和你的创建前端模块的名称一致。例如我的前端模块目录是 client，我这里就是vue init webpack client。然后其他的配置，的配置选项就是正常的 vue 初始化过程了。我这里选择的构建工具是 Yarn，不是 npm。此处的选择不影响后面的构建。

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-299468bb.png" | prepend: site.baseurl  }})

这一步完成后，基本的前端模块算初始化完成。接下来，我们需要将前端模块整合道 Gradle 中便于统一编译和管理。
配置前端模块

一、clinet/build.gradle配置

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-fd8700ac.png" | prepend: site.baseurl  }})

    将clinet/build.gradle内容修改为如下：
```
    plugins {
        id "com.moowork.node" version "1.2.0"
    }

    node {
        version = '8.7.0'
        yarnVersion = '1.3.2'
        download = true
    }

    task bootRun(dependsOn: 'start') {
        group = 'application'
        description = 'Run the client app (for use with gradle bootRun -parallel）'
    }

    task start(type: YarnTask, dependsOn: 'yarn') {
        group = 'application'
        description = 'Run the client app'
        args = ['run', 'start']
    }

    task build(type: YarnTask, dependsOn: 'yarn') {
        group = 'build'
        description = 'Build the client bundle'
        args = ['run', 'build']
    }
```
此处主要是为Gradle 配置了几个任务。大致意思就是用 Gradle 来代理执行前端的编译命令。

二、build.gradle配置

这里需要配置是根项目下的build.gradle

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-05bd915a.png" | prepend: site.baseurl  }})

在build.gradle 中追加下面代码
```
    task copyHtml(type: Copy) {
        group = 'build'
        description = '复制编译后的index.html到 server 的 resource 目录'
        from 'client/dist/index.html'
        into 'server/build/resources/main/static'
    }

    task copyStatic(type: Copy, dependsOn: 'copyHtml') {
        group = 'build'
        description = '复制编译后的静态文件到 server 的 resource 目录'

        from 'client/dist/static'
        into 'server/build/resources/main/static/static'
    }

    copyStatic.mustRunAfter('client:build')

    task mergeBuild(dependsOn: ['client:build', ':copyStatic', 'server:bootJar']) {
        group = 'build'
        description = '合并编译输出'
    }

    task independentBuild(dependsOn: ['client:build', 'server:bootJar']) {
        group = 'build'
        description = '独立编译输出'
    }
```
此处主要解释一下mergeBuild和independentBuild

mergeBuild：合并编译输出

合并编译输出输出的意思，表示前后端开发的时候是分模块分离开发，但是最终编译到发布项目的时候，会将前端编译的静态内容和html 复制道后台SpringBoot 项目的静态资源目录中,从而达到一体发布。

independentBuild:独立编译输出

独立编译输出区别于mergeBuild是不会复制前端文件到SpringBoot端来，所以我们在部署项目的时候，需要单独部署前端服务。

三、配置前端开发模式代理

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-b423ac3c.png" | prepend: site.baseurl  }})

    client/config/index.js 修改proxyTable和port两处配置，如图所示。端口修改是因为，SpringBoot 运行的默认端口也是8080，为了不冲突，前端修改端口。proxyTable表示在开发阶段，前端所有已/server开头的请求,都代理到http://localhost:8080后台服务进行处理，pathRewrite意思是代理的转发时，剔除/server部分。

    例：
    前台请求：http://localhost:8081/server/user/list
    实际代理执行请求：http://localhost:8080/user/list
    端口和路径都发生了变化

到此项目的初始配置就基本完成。
最后：如何运行项目

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-dcb453dc.png" | prepend: site.baseurl  }})

    找到 idea 右边栏上的 Gradle 选项卡，打开找到 vueb 下面的 Tasks>appplication>bootRun 右键选择Create 'vueb [bootRun]'...

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-3325ba4e.png" | prepend: site.baseurl  }})

    在 Arguments 中增加-parallel参数，然后 ok。

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-019fa502.png" | prepend: site.baseurl  }})

    最后在运行栏中可以看到配置的启动项。点击旁边的三角形就可以同时启动前端和后端服务。
![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-4d24d5e8.png" | prepend: site.baseurl  }})

    最后可以看到控制台提示两个启动成功的信息，则表示项目构建成功。

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-c58a3c5d.png" | prepend: site.baseurl  }})

    上图所示，表示 SpringBoot 项目启动成功

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-f1d958e8.png" | prepend: site.baseurl  }})

    上图所示，前端项目启动成功，我们可以访问http://localhost:8081

![]({{ '/styles/images/assets/vue_springboot整合/2019-02-20-Vue与springboot整合-ff95fb67.png" | prepend: site.baseurl  }})
Over
---------------------
作者：____′↘夏悸
来源：CSDN
原文：https://blog.csdn.net/angelhua520/article/details/83451683
版权声明：本文为博主原创文章，转载请附上博文链接！
