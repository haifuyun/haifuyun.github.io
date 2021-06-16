---
title:  Xamarin 绑定iOS第三方库
date:   2017-07-19
categories: [iOS, Xamarin]
tags: [iOS]
---
## Xamarin 绑定iOS第三方库



Xamarin iOS项目通常会需要用到一些第三方库，如AFNetworking、SDWebImage等，这些库源代码都是用Objective-c写的，因此我们就需要将Objective-C代码API转换成C#代码API来使用。接下来我们根据Xamrain的官方文档里面的几篇文章来绑定



绑定一个Objective-c库用于Xamarin需要三个步骤:

1.编写一个C#的“API definition”.cs文件去描述原生API在.NET中的暴露方式，以及c#是怎么映射到底层Objective-c. 这里使用C#结构体像interface和各种属性来完成

2.一旦你用C#写好了”API definition”文件,你就可以编译它产生一个绑定集dll. 这个过程可以用command line或者Xamarin Studio、Visual Studio建一个binding project来完成.

3.最后添加这个绑定集dll到你的Xamarin应用项目，这样你就可以成功的从你定义好的API调用原生功能. 这个绑定项目是完全从你的应用项目分离的



NOTE:“步骤一”可以使用Xamarin官方提供的一个命令行工具Objective Sharpie自动化帮助你转换生成. 它会检查Objective-C API并生成一个它提供的C#所写的API definition文件. 然后你可以自定义修改这个生成的文件和使用它来创建你的绑定集. 另外还可以参照官方文档的手写定义文件，呃，这个我就忽略吧 - -，但是这个文档可以参考用来修改生成的文件中一些转换不准确或者不明确的API

“步骤二”这里是用的是Mac OS X系统的Xamarin Studio里的Binding Project生成的，另外也可以使用Visual Studio 或者Command Line来绑定

Binding Project（绑定项目）一个绑定项目可以在Xamarin Studio or Visual Studio (Visual Studio only supports iOS bindings)创建.它可以很容易的编辑和构建用于绑定的API definitions文件

跟着 getting started guide 可以了解怎么生成和使用一个binding project去产生绑定.

Objective Sharpie（命令行工具）Objective Sharpie是一个在创建绑定初始阶段提供帮助的一个独立的命令行工具. 可以在初始步骤自动化的帮你转换生成目标原生库的c#apidefinition文件

Objective Sharpie docs可以帮你了解怎么解析native libraries, native frameworks, CocoPods成API definitions文件然后去构建一个绑定

正文:步骤一: 生成ApiDefinitions.cs和StructsAndEnums.cs

1. 首先获取第三方原生库,下面的例子是友盟![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/umengSDk.png)
2. CD到当前文件夹，用下面命令会直接转换framework里面的头文件$ sharpie bind \-framework UMMobClick.framework \-sdk iphoneos10.3但是有个要注意的是，xxx.framework内部的xxx.h的头文件前缀名要和framework的前缀名一致，sharpie工具才能找到，否则它会报错找不到文件，因此要把xxx.h和xxx.framework前缀改成一致UMMobClick.framework内部会有一个Headers文件夹，里面就是我们需要的头文件

A文件夹下面有个UMMobClick文件，这其实是.a静态库，我们后面需要用到, 需要添加.a文件扩展名![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/headerfile.png)![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/headerfile1.png)

但是有时framework内部不止一个头文件，并且名字不一样，上面的例子友盟就是如此，因此我们就可以用另一个命令来转换，工具还是不太智能啊！$ sharpie bind \-sdk iphoneos10.3 \/…/Headers/MobClick.h /…/Headers/MobClickGameAnalytics.h/…/Headers/MobClickSocialAnalytics.h-scope MobClick.framework/Headers \-c -F .执行后会生成ApiDefinitions.cs和StructsAndEnums.cs

![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/Api.png)

现在我们就有了三个文件: 2个cs文件, 1个.a静态库

![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/file.png)





步骤二: 在Xamarin Studio新建个iOS binding project![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/xamarin.png)

在项目方案中右键添加进.a静态文件

![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/add.png)

添加成功后本机引用中会出现静态库文件，然后把之前生成的api和struct文件里的内容拷进项目对应的文件中覆盖![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/bindproject.png)

这里需要设置下.a静态库的属性，Frameworks中是库需要用到的框架,LinkerFlags是需要用到的库，以及默认的smart link 和 force load属性

![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/p.png)

然后就可以build 项目，会报错，这并不奇怪，因为api转换是命令行工具帮我们转换的，转换的文件中的一些api类型它无法确定，会添加类似的转换警告[Verify (StronglyTypedNSArray)][Verify (MethodToProperty)]需要自己确认转换类型，可以参考文档，没问题可以把这个警告删掉，重新再编译就成功了

然后友盟这里需要在ApiDefiinitions.cs文件中把所有API添加进一个namespace中，否则编译时会报错，因为会生成一个SupportDelegate.cs的文件，里面会有一个没名字的namespace，包含着两个API![img](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/XmarinBind/error.png)

ApiDefiinitions.cs里的一些API会需要调用到这两个API，并且错误说明报出找不到这两个API，没名字的namespcae当然找不到啦，并且这两个方法还是在两个不同名字的namespace里，因此我们需要把这些api都添加进同一个namespcae里并且命个名，重新再编译就成功了

然后就可以在项目文件夹中的bin文件夹中找到xxx.dll文件，把dll引用到需要的应用项目中，就可以使用这个库提供的接口

最后在转换生成ApiDefinitions.cs和StructsAndEnums.cs中，有几种方法，上面用到的是直接提供三个.h文件转换生成，另外还可以直接用命令将cocoapod中的有的库直接转换，还有一种是将xcode项目直接转换, 在这个例子页面有链接可以参考Example，cocoapod方法中的一些坑和上面的类似，需要将库和头文件命名一致