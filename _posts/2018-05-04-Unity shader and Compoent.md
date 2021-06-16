---
title:  Unity shader and Compoent
date:   2018-05-04
categories: [Graphics, Unity]
tags: [Graphics]
---
## Unity shader and Compoent

UnityShader是Unity引擎提供的图形渲染框架，在上一篇博客里有详细的讲了Shader是用来干嘛的，UnityShader是对OpenGL、DIrectx两个底层图形渲染接口的封装，使用Unity自家的语言ShaderLab来编写, 因为提供了封装，所以写起来更加简便，省略了很多一些在GLSL、HLSL下编写代码中的繁琐重复的细节. 这里我们只讲Unity中的Vertex And Fragment Shader.

UnityShader 内部结构

- Shader
  - Properties
  - SubShader
    - Tags
    - RenderSetup
    - Pass
      - Tags
      - Lod
      - RenderSetup
  - Fallback

------

- UnityShaderFile

> 在Unity中, 一个Shader文件中包含上面所展示的完整的一个Shader框架内容，也可以包含多个完整的Shader.

- Shader

> Shader内部包含Properties、SubShader、Fallback, 其中SubShader可以存在多个.

- Properties

> 用于与Unity客户端的图形界面中Inspector面板中纹理(Texture)、颜色(Color)、数字类型(Int、Float、Range)、四维向量(Color、Vector)数据进行传输交换.
>
> 例子:
>
> Properties {
>
>  _MainTex ( “Texture”, 2D ) = “white” {}
>
>  _Color ( “Color Tint”, Color ) = (1,1,1,1)
>
>  _VerticalBillboarding ( “Vertical Restraints”, Range(0, 1) ) = 1
>
> }
>
> Int _Int (“Int”, Int) = 2
>
> Float _Float (“Float”, Float) = 1. 5
>
> Range(min, max) _Range(“ Range”, Range( 0. 0, 5. 0)) = 3. 0
>
> Color _Color (“Color”, Color) = (1, 1, 1, 1)
>
> Vector _Vector (“Vector”, Vector) = (2, 3, 6, 1)
>
> 2D Textures _2D (“2D”, 2D) = “” {}
>
> Cube _Cube (“Cube”, Cube) = “white” {}
>
> 3D _3D (“3D”, 3D) = “black” {}

- SubShader、Fallback

> SubShader就像字面意思”替补着色器”, 通常在一个UnityShader里面可以有多个SubShader, 而每个SubShader内部其实就是实际用于渲染操作的代码.
>
> 我们先不讨论SubShader内部的内容, 先讨论SubShader在于Sahder这个内部结构中的作用, 其实作用主要就是用于适配不同性能和功能类型的显卡，结合Pass中的Lod来使用.
>
> 通常顺序是自上而下执行, 直至最后你所定义的所有SubShader都不能执行, 就会执行Shader内部结构里处于最后定义的Fallback这个属性所指定的最基础通用的能执行的Shader,或者你自己所定义的Shader, 但基本上Fallback的作用就是保证最后能被渲染出画面这么个最基础的要求.

- SubShader 内部

  > - Tags
  >
  >   > Tags是一个标签，它是一个键值对类型，是SubShader和渲染引擎之间的沟通桥梁,用来声明当前SubShader要如何渲染当前这个对象
  >   >
  >   > Pass内部还有一个Tags，与当前Subshader内部的Tags有不同的作用
  >   >
  >   > 定义: Tags { “TagName_1” = “Value_1” “TagName_2 = Value_2” }
  >   >
  >   > 例子: Tags { “Queue” = “Transparent” “RenderType” = “Opaque” }
  >   >
  >   > 那么TagName有下列几种标签:
  >   >
  >   > - Queue
  >   >
  >   >   > 渲染序列 (渲染优先级)
  >   >
  >   >   - Background
  >   >
  >   >     > 用于背景渲染, 在其他物体前渲染
  >   >
  >   >   - Geometry
  >   >
  >   >     > 默认几何渲染, 不透明物体用这个队列
  >   >
  >   >   - AlphaTest
  >   >
  >   >     > 透明度物体测试队列, 独立于Geometry队列, 在不透明物体渲染后执行
  >   >
  >   >   - Transparent
  >   >
  >   >     > 物体透明度混合渲染队列， 在Geometry和AlphaTst队列执行后执行，没有深度测试
  >   >
  >   >   - Overlay
  >   >
  >   >     > 用于最后渲染的叠加效果，如镜头光耀
  >   >
  >   > - RenderType
  >   >
  >   >   > 渲染类型 (渲染物体所显示的类型)
  >   >
  >   >   - Opaque
  >   >
  >   >     > 不透明物体
  >   >
  >   >   - Transparent
  >   >
  >   >     > 大多数透明物体 （粒子，字体，地形)
  >   >
  >   >   - TransparentCutout
  >   >
  >   >     > 遮罩透明
  >   >
  >   >   - Background
  >   >
  >   >     > 天空盒
  >   >
  >   >   - Overlay
  >   >
  >   >     > 覆盖物, GUITexture(图形界面), Halo(光晕), Flare(耀斑)
  >   >
  >   >   - TreeOpaque
  >   >
  >   >     > 不透明树, 地形引擎树皮
  >   >
  >   >   - TreeTransparentCutout
  >   >
  >   >     > 地形引擎树皮
  >   >
  >   >   - TreeBillboard
  >   >
  >   >     > 地形引擎 “广告牌” 树木,广告牌是一种总是2D物体总是朝向摄像机视角的一种渲染技术
  >   >
  >   >   - Grass
  >   >
  >   >     > 地形引擎草
  >   >
  >   >   - GrassBillboard
  >   >
  >   >     > 地形引擎 “广告牌” 树木
  >   >
  >   > - DisAbleBatching
  >   >
  >   >   > 是否开启批处理，三个选项 “True” “False” “LODFading”, LODFading开启时会判断是否DisAbleBatching, 大多数时用于树木
  >   >
  >   > - IgnoreProjector
  >   >
  >   >   > 不接受投影, True不会接受其他物体到本物体的投影, False为接受
  >   >
  >   > - ForceNoShadowCasting
  >   >
  >   >   > 不会产生头晕, True为不会对其他物体投影, False为会对其他物体投影
  >   >
  >   > - CanuseSpriteAtlas
  >   >
  >   >   > 是否使用精灵地图集, False为启用
  >   >   >
  >   >   > 2D精灵图片资源在制作时导出为文件通常为一小张一小张，这样在把很多场景里的元素载入内存时，很多张小的Sprite会让显存空间占用更多读取速度降低，如果把很多小的Sprite合成一张大的Sprite，一次读取会加快读区速度和减少内存占用
  >   >
  >   > - PreviewType
  >   >
  >   >   > 指示材质检查器预览应如何显示材质。默认情况下，材质显示为球体，但PreviewType也可以设置为“Plane”（将显示为2D）或“Skybox”（将显示为天空盒）
  >
  > - Pass
  >
  >   > 渲染通道，一个SubShader内部可以有一个渲染通道也可以多个渲染通道，一个Pass就会调用一次DarwCall，物体就会被渲染一次.
  >   >
  >   > 多个Pass就会多次调用DrawCall，物体就会被渲染多次，但是效率和帧数就会下降.
  >   >
  >   > 在特殊情况下才会编写多个Pass对一个物体进行多次渲染，比如对一个透明物体的渲染，在第一个Pass中我们需要对物体面向摄像机的Front面进行剔除，对背面Back进行透明渲染后，传入下一个Pass对原数据的背面进行剔除后，再对面对摄像机的Front面进行透明度渲染，最后与上一个Pass传入的背部透明度渲染后的数据混合成一个前后透明，可以从前到后都透明看到后面的物体的这么一个效果.
  >   >
  >   > 因为透明混合需要关闭深度测试，所以没有深度测试的再一个Pass里做混合会让物体产生不能区分前后面的效果，所以就需要用到两个Pass进行渲染.
  >   >
  >   > - Tags
  >   >
  >   >   > 通过使用标记来说明它们如何给绘制引擎来渲染
  >   >   >
  >   >   > - LightMode
  >   >   >
  >   >   >   > LightMode tag defines Pass’ role in the lighting pipeline.
  >   >   >   >
  >   >   >   > 定义该Pass在光照流水线中的角色
  >   >   >
  >   >   >   - Always
  >   >   >
  >   >   >     > Always rendered; no lighting is applied
  >   >   >     >
  >   >   >     > 总是被渲染
  >   >   >
  >   >   >   - ForwardBase
  >   >   >
  >   >   >     > 用于正向渲染，应用环境光，主定向光，顶点/ SH灯和光照贴图
  >   >   >     >
  >   >   >     > 这个模式会渲染场景中光照强度最强的平行光，如果没有平行光会渲染成黑色
  >   >   >     >
  >   >   >     > Base Pass 中渲染的平行光默认是支持阴影
  >   >   >     >
  >   >   >     > 每进行一次完整的前向渲染， 我们需要渲染该对象的渲染图元， 并计算两个缓冲区的信息： 一个是颜色缓冲区， 一个是深度缓冲区。 我们利用深度缓冲来决定 一个片元是否可见， 如果可见就更新颜色缓冲区中的颜色 值
  >   >   >
  >   >   >   - ForwardAdd
  >   >   >
  >   >   >     > 用于前向渲染；加上每个像素光，每一个额外光源进行一次pass渲染
  >   >   >     >
  >   >   >     > 一个Base Pass仅会执行一次( 定义了多个Base Pass的情况除外） 而 一个 Additional Pass 会 根据 影响该物体的其他逐像素光源的数目被多次调用， 即每个逐像素光源会执行一次Additional Pass
  >   >   >
  >   >   >   - Deferred
  >   >   >
  >   >   >     > 前 向渲染的问题是： 当 场景 中 包含 大量 实时 光源 时， 前 向 渲染 的 性能 会 急速 下降。 例如， 如果 我们 在 场景 的 某一 块 区域 放置 了 多个 光源， 这些 光源 影响 的 区域 互相 重叠， 那么 为了 得到 最终 的 光照 效果， 我们 就 需要 为 该 区域 内 的 每个 物体 执行 多个 Pass 来 计算 不同 光源 对 该 物体 的 光照 结果， 然后 在 颜色 缓存 中 把这 些 结果 混合 起来 得到 最终 的 光照。 然而， 每 执行 一个 Pass 我们 都 需要 重新 渲染 一遍 物体， 但 很多 计算 实际上 是 重复 的。
  >   >   >     >
  >   >   >     > 延迟 渲染 主要 包含 了 两个 Pass。 在 第一个 Pass 中， 我们 不进 行 任何 光照 计算， 而是 仅仅 计算 哪些 片 元 是 可见 的， 这 主要 是 通过 深度 缓冲 技术 来 实现， 当 发现 一个 片 元 是 可见 的， 我们 就把 它的 相关 信息 存储 到 G 缓冲区 中。 然后， 在 第二个 Pass 中， 我们 利用 G 缓冲区 的 各个 片 元 信息， 例如 表面 法线、 视角 方向、 漫反射 系数 等， 进行 真正 的 光照 计算
  >   >   >
  >   >   >   - ShadowCaster
  >   >   >
  >   >   >     > 渲染对象深度到阴影图或深度纹理
  >   >   >
  >   >   >   - MotionVectors
  >   >   >
  >   >   >     > 用于计算每个对象的运动矢量
  >   >   >
  >   >   >   - PrepassBase
  >   >   >
  >   >   >     > 用于传统的延迟照明，渲染法线和高光指数
  >   >   >
  >   >   >   - PrepassFinal
  >   >   >
  >   >   >     > 在传统延迟照明中，通过组合纹理、光照和发射来渲染最终颜色。
  >   >   >
  >   >   >   - Vertex
  >   >   >
  >   >   >     > 在对象未被光映射时用于遗留顶点照明渲染；应用所有顶点光
  >   >   >
  >   >   >   - VertexLMRGBM
  >   >   >
  >   >   >     > 在对象被光映射时用于遗留顶点点亮渲染；在LAMP映射为RGBM编码的平台上（PC）
  >   >   >
  >   >   >   - VertexLM
  >   >   >
  >   >   >     > 在对象被光照贴图时用于传统顶点点亮渲染;在光照贴图是双LDR编码的平台上（移动平台）
  >   >   >
  >   >   > - PassFlags
  >   >   >
  >   >   >   > 一个Pass可以标示一个标志来改变渲染管线怎么传输数据给到自己. 当前使用PassFlags来完成这个，使用一个分割空格的Flag名字值，当前只支持OnlyDirectional,
  >   >   >   >
  >   >   >   > 当用于ForwardBase通道类型时，该标志使得只有主定向光和环境/光探测数据才被传递到着色器。这意味着非重要光源的数据不会传递到顶点光或球面谐波着色器变量中
  >   >   >
  >   >   > - RequireOptions
  >   >   >
  >   >   >   > 用于指定当满足某些条件时才渲染该Pass
  >   >
  >   > - Lod ( Shader “Level of Detail” )
  >   >
  >   >   > 着色器”细节等级”, Lod是用来限制当前渲染的细节程度的高低的, 当给定的Lod值小于Shader里声明的数值时，才会进行Pass下的渲染.
  >   >   >
  >   >   > 这里结合官方描述和我自己的思考想了下有下面几个因素:
  >   >   >
  >   >   > 1.设备的显卡支持我们编写的Shader中的所有功能，但是一些设备的性能速度并不是很好，虽然都支持这些功能，但是渲染效率很低，画面帧数低，那么就可以根据Lod值来对设备性能进行区分，如果低于这个值那么可以进行这种方式渲染，否者就使用另一个Subshader进行渲染.
  >   >   >
  >   >   > 2.可以做画质等级的渲染变更.
  >   >   >
  >   >   > 3.对游戏场景中远近物体的渲染细节的调整，降低渲染功耗，提高渲染效率.
  >   >
  >   > - RenderSetup
  >   >
  >   >   > 状态设置
  >   >   >
  >   >   > ShaderLab提供了一系列渲染状态的设置指令， 这些指令可以设置显卡的各种状态， 例如是否开启混合/ 深度测试等。
  >   >   >
  >   >   > - Cull
  >   >   >
  >   >   >   > 剔除模式
  >   >   >   >
  >   >   >   > Cull Back | Front | Off
  >   >   >   >
  >   >   >   > 剔除 背面 | 前面 | 关闭
  >   >   >
  >   >   > - ZTest
  >   >   >
  >   >   >   > 设置深度测试函数
  >   >   >   >
  >   >   >   > ZTest Less | Greater | LEqual | GEqual | Equal | NotEqual | Always
  >   >   >   >
  >   >   >   >  小于 | 大于 | 小于等于 | 大于等于 | 等于 | 不等于 | 都渲染
  >   >   >   >
  >   >   >   > 如何进行深度测试。默认是LEqual(绘制前面的对象或自己相同深度的对象，并隐藏后面的对象)
  >   >   >
  >   >   > - Zwrite
  >   >   >
  >   >   >   > 深度写入
  >   >   >   >
  >   >   >   > Zwrite On | Off
  >   >   >   >
  >   >   >   >  开启 | 关闭
  >   >   >
  >   >   > - Blend
  >   >   >
  >   >   >   > 混合模式
  >   >   >   >
  >   >   >   > 句法:
  >   >   >   >
  >   >   >   > 1 Blend Off
  >   >   >   >
  >   >   >   > > 关闭混合
  >   >   >   >
  >   >   >   > 2 Blend SrcFactor DstFactor
  >   >   >   >
  >   >   >   > > 开启混合，设置混合因子，源颜色乘与SrcFactor, 目标颜色乘与 DstFactor, 最后相加存入颜色缓冲
  >   >   >   >
  >   >   >   > 3 Blend SrcFactor DstFactor, SrcFactorA DstFactorA
  >   >   >   >
  >   >   >   > > 同上, 额外增加不同因子来混合Alpha通道，即透明度
  >   >   >   >
  >   >   >   > 4 BlendOp Op
  >   >   >   >
  >   >   >   > > 并非是把源颜色和目标颜色简单 相加后混合， 而是使用BlendOperation 对它们进行其他操作
  >   >   >   >
  >   >   >   > 5 BlendOp OpColor, OpAlpha
  >   >   >   >
  >   >   >   > > 彩色值操作(RGB)同上, 但使用不同的操作与Alpha通道混合
  >   >   >
  >   >   >   ------
  >   >   >
  >   >   >   > 混合操作:
  >   >   >   >
  >   >   >   > Add
  >   >   >   >
  >   >   >   > > 源颜色加目标色
  >   >   >   >
  >   >   >   > Sub
  >   >   >   >
  >   >   >   > > 源颜色减目标色
  >   >   >   >
  >   >   >   > RevSub
  >   >   >   >
  >   >   >   > > 目标色减源颜色
  >   >   >   >
  >   >   >   > Min
  >   >   >   >
  >   >   >   > > 取源颜色和目标色的比较后小的值
  >   >   >   >
  >   >   >   > Max
  >   >   >   >
  >   >   >   > > 取源颜色和目标色的较较后大的值
  >   >   >   >
  >   >   >   > ( 以下只在DX11才能使用 )
  >   >   >   >
  >   >   >   > LogicalClear 逻辑运算
  >   >   >   >
  >   >   >   > LogicalSet 逻辑操作
  >   >   >   >
  >   >   >   > LogicalCopy 逻辑操作
  >   >   >   >
  >   >   >   > LogicalCopyInverted 逻辑操作 - 反复制
  >   >   >   >
  >   >   >   > LogicalNoop 逻辑操作 - 空操作
  >   >   >   >
  >   >   >   > LogicalInvert 逻辑运算
  >   >   >   >
  >   >   >   > LogicalAnd 逻辑运算
  >   >   >   >
  >   >   >   > LogicalNand 逻辑运算
  >   >   >   >
  >   >   >   > LogicalOr 逻辑运算
  >   >   >   >
  >   >   >   > LogicalNor 逻辑运算
  >   >   >   >
  >   >   >   > LogicalXor 逻辑操作 - 异或
  >   >   >   >
  >   >   >   > LogicalEquiv 逻辑运算 - 等价
  >   >   >   >
  >   >   >   > LogicalAndReverse 逻辑运算 - 扭转和
  >   >   >   >
  >   >   >   > LogicalAndInverted 逻辑运算 - 反转并
  >   >   >   >
  >   >   >   > LogicalOrReverse 逻辑运算 - 反转或
  >   >   >   >
  >   >   >   > LogicalOrInverted 逻辑运算
  >   >   >
  >   >   >   ------
  >   >   >
  >   >   >   > 混合因子:
  >   >   >   >
  >   >   >   > One
  >   >   >   >
  >   >   >   > > 因子值为1， 使用它可以让源颜色或目标颜色在渲染管线中混合时完全通过管线后，保持它原来的值
  >   >   >   >
  >   >   >   > Zero
  >   >   >   >
  >   >   >   > > 因子值为0, 会清除源颜色和目标颜色的原始值
  >   >   >   >
  >   >   >   > SrcColor
  >   >   >   >
  >   >   >   > > 因子为源颜色值，这个阶段的值会乘与源颜色的RGB分量值
  >   >   >   >
  >   >   >   > SrcAlpha
  >   >   >   >
  >   >   >   > > 因子为源颜色的alpha值，这个阶段的值会乘与源颜色的alpha通道值(a)
  >   >   >   >
  >   >   >   > DstColor
  >   >   >   >
  >   >   >   > > 因子为目标颜色的值，这个阶段的值会乘与目标颜色的RGB分量值
  >   >   >   >
  >   >   >   > DstAlpha
  >   >   >   >
  >   >   >   > > 因子为目标颜色的alpha值，这个阶段的值会乘与目标颜色的alpha通道的值(a)
  >   >   >   >
  >   >   >   > OneMinusSrcColor
  >   >   >   >
  >   >   >   > > 因子为 1 - source color (源颜色的RGB分量值), 这个阶段的值会乘与1 - source color的值
  >   >   >   >
  >   >   >   > OneMinusSrcAlpha
  >   >   >   >
  >   >   >   > > 因子为 1 - source alpha (源颜色的Alpha值), 这个阶段的值会乘与1 - source alpha的值
  >   >   >   >
  >   >   >   > OneMinusDstColor
  >   >   >   >
  >   >   >   > > 因子为 1 - destination color (目标颜色的RGB分量值), 这个阶段的值会乘与1 - destination color的值
  >   >   >   >
  >   >   >   > OneMinusDstAdlpha
  >   >   >   >
  >   >   >   > > 因子为 1 - destination alpha (目标颜色的Alpha值), 这个阶段的值会乘与1 - destination alpha的值
  >   >   >
  >   >   >   ------
  >   >   >
  >   >   >   > 混合操作与混合因子组合 - 混合类型
  >   >   >   >
  >   >   >   > > 正常 - 透明度混合
  >   >   >   > >
  >   >   >   > > Blend SrcAlpha OneMinusSrcAlpha
  >   >   >   > >
  >   >   >   > > 柔和相加
  >   >   >   > >
  >   >   >   > > Blend OneMinusDstColor One
  >   >   >   > >
  >   >   >   > > 正片叠底
  >   >   >   > >
  >   >   >   > > Blend DstColor Zero
  >   >   >   > >
  >   >   >   > > 两倍相乘
  >   >   >   > >
  >   >   >   > > Blend DstColor SrcColor
  >   >   >   > >
  >   >   >   > > 变暗
  >   >   >   > >
  >   >   >   > > BlendOp Min
  >   >   >   > >
  >   >   >   > > BlendOp Min Blend One One
  >   >   >   > >
  >   >   >   > > 变亮
  >   >   >   > >
  >   >   >   > > BlendOp Max
  >   >   >   > >
  >   >   >   > > Blend One One
  >   >   >   > >
  >   >   >   > > 滤色
  >   >   >   > >
  >   >   >   > > Blend OneMinusDstColor One
  >   >   >   > >
  >   >   >   > > 等同于
  >   >   >   > >
  >   >   >   > > Blend One OneMinusSrcColor
  >   >   >   > >
  >   >   >   > > 线性减淡
  >   >   >   > >
  >   >   >   > > Blend One One
