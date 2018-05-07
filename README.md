#前言#
>由于工作原因，可能会被调到隔壁组。此架构为隔壁组leader设计，并不是我原创，已取得leader同意，此博文只分享我对此架构的理解。特此声明。

最近看了下Kotlin的一些语法，外加隔壁组采用非常规MVC/MVP/MVVM开发，所以写此Demo，一是为了练习Kotlin语法，二是为了尝试使用隔壁组架构进行开发。由于为了练习Kotlin语法，所以在代码的具体实现上，可能会有些“炫技”的语法。比如能用一种语法实现的功能，Demo用了两种及以上的语法。了解即可，不必深究。还有一部分“非常规”操作，比如直接就能实现的功能，具体实现上却“山路十八弯”，注意，这不属于“语法炫技”的范围！如果觉得“这里不用山路十八弯”，请往架构方面考虑。

#Kotlin#
这是一款[壁纸](https://github.com/yikousamo/wallpaper)应用，虽然功能比较简单，但是用Java代码实现起来还是有些代码。除去框架部分，用kotlin实现，能省很多代码。比如属性定义、finViewById、lambda、协程、闭包等等。代码的具体实现没撒好说的，还请自行查看源码。本文的重点是下个部分“服务架构设计”。
#服务架构设计#
由于公司产品比较多而小，并且有很多共通的功能点，现比较流行的MVC/MVP/MVVM开发模式，运用到实际开发效率不高，所以leader设计了一套开发模式供我们这些菜鸡使用。在开发/维护效率上，简直是贼高！有多高？去年Android组5个人，iOS有25+个。真正的一打五，恐怖如斯！
##总体设计#
1. 以模块划分，这个可以简单理解成“模块化开发”中的模块。每个模块可以是一个页面也可以是几个页面，以业务/功能划分。
2. 除主工程外，每个模块以aar包的形式向其他模块提供业务/功能
3. 每个模块分为四个部分，这四个部分分别又是4个arr。
 1. Service: 业务接口定义
 2. ServiceImpl: 业务接口具体实现
 3. ViewService: UI接口定义
 4. ViewServiceImpl: UI接口具体实现
4. 所有业务统一管理，针对到**每个**APP，在主工程Application中注册具体实现
5. 脚本打包，代码管理

###我的一些个人理解###
 - 模块：不吹不黑，总体设计里就是我对模块的理解...
 - 主工程：一般代码很少，只放“主页”相关相关的几个类，甚至主页对应的service/serviceImpl都会提供相应的aar。
 - 模块四元素，四元素遵循“面向接口编程”的理念。每定义一种业务/功能时，先定义好接口规范，这样的话，在其他相似模块或者其他APP相似功能的时候，接口是不用修改的，只需要复用或者重写接口具体实现即可（开闭原则）。这样的话，还有一些好处，合作开发，AB模块通过接口调用，有效降耦合，而且AB模块的开发者并不需要知道对方具体实现。
  - Service：业务接口定义，我理解的这个模块相当于MVP中的P层
  - ServiceImpl：业务接口具体实现，相当于P层的具体实现
  - ViewService：UI接口定义，定义当前模块有什么UI相关的功能/业务，比如扫码、登陆等等。
  - ViewServiceImpl：UI接口具体实现。在单APP中作用不是很大，多APP相似UI的时候，效率提升巨大。

 四元素是此框架很精髓的部分，开发效率高很大一部分是由于四元素的设计。对于不同APP差别不大的模块，可使用相同业务/UI接口，具体APP编写具体实现即可。
 - 业务管理：由于多个APP的很多业务/UI接口是差不多的，只是业务/UI实现上会有些差别，所以针对到不同的APP，注册进相应的业务/UI接口实现即可，甚至有些具体实现也是可复用的。
 - 代码管理：每个APP会有很多模块，每个模块下又有四元素，每个模块和四元素都是module/app的形式，AS每次都去构架的话，估计一次打包没个十来分钟搞不定，所以代码管理采用SNAPSHOT+AAR的形式，将每个AAR以SNAPSHOT的形式依赖，这样就不用频繁build每个module/app，每次更新，只更新相应的SNAPSHOT即可。
 
 我理解的大体架构大概就是上面所描述的，当然，为了解耦还会有一些其他的小技巧，不过这些无伤大雅，demo中也体现了一部分，感兴趣可以看下[壁纸Demo](https://github.com/yikousamo/wallpaper)。

#壁纸APP的说明#
壁纸APP有两个界面：“主页”和“预览页”，也可以理解为两个模块：“主页模块”和“预览模块”。为了图方便也为了方便读者看代码，这里没有将对网络框架的封装、Util类封装成通用AAR库，没有将预览模块写成单独的module/app，没有将每个service/serviceImpl写成单独的module/app。说来有些讽刺，比较精髓的部分居然被我一笔略过，这点读者明白即可。

#碎碎念小心得#
对我而言，架构这个东西说起来有些缥缈的感觉。之前照着现有的MVX模式照搬，鲜有考虑为什么要这么写？好处是什么？或者说只是看了别人的讲解-这么做有bulabula好处。但生搬硬套始终是有些生硬，自己理解甚至能设计出合理的架构才是王道。这也是我非常佩服leader的地方，我有些觉得这已经超出了“小聪明”的程度，堪称智慧了。我也在下意识提升自己这方面的能力，一起努力吧~

#Demo下载#
壁纸APPDemo: [https://github.com/yikousamo/wallpaper](https://github.com/yikousamo/wallpaper)




