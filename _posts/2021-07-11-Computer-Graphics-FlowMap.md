---
title: Computer Graphics FlowMap
date: 2021-07-11
categories: [Graphics, Texture Effect]
tags: [Graphics]
math: true
---

Flow map如字面意思，会流动的贴图，原理是采用两张贴图，一张展示底图，一张提供位置数据(Red、Green通道映射x、y位置)让底图采样得到位置数据叠加时间后产生位移效果，表现为流动效果



提供位移数据的贴图分为RGBA四个通道，我们绘制RG两个通道，让他们分布在我们想要改变的位置坐标上

![2](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/FlowMap/2.png)

底图

![1](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/FlowMap/1.png)

叠加效果

![3](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/FlowMap/3.png)

代码

```
float4 f = tex2D(flowMap, i.uv) * 2 + 1;
float4 t = tex2D(testTex, i.uv + f.xy * flowIntensity + float2(0, _Time.y * 0.1));
return t;
```







waterflowmap

水流动地图，模拟水面流动效果，其实是flowmap基础上加强版本

加强版使用淡入淡出效果，消除了贴图流动周而复始运动的假象



代码逻辑:

先设定一个时间长度(duration)，是完整流动效果一个周期的时间

假设duration为5，每5秒t才会增长一，完整变化的一个周期，t为我们实际想要的时间变化，真实世界5秒，t才过1秒，增长范围为0-1

| 0.1  | 0.02 |
| ---- | ---- |
| 5    | 1    |
| 10   | 2    |
| 15   | 3    |
| 15.1 | 3.02 |

取得t后，用t与2做模运算，模运算可以理解为限定在一个范围，这个范围是0.0 - 2.0。就像时间一天24小时，过了午夜12点，又从0开始。

[mod运算](https://www.calculators.org/math/modulo.php)

a%b=(b*商)+a



那为什么是模2呢？这里要讲一下贴图的取值范围，和扩大它的范围。

我们贴图的正常取值范围是0-1，如果我们将其 * 2 - 1 就变成 -1 ～ 1，就是范围为2，这样做就扩大了我们的位移范围，原本只可以往一个方向，现在可以往上下左右，当然这样修改后我们需要在做对应颜色位移图时，位移距离的值设定要变更。

因为采样偏移贴图时，取的是Red、Green颜色作为XY坐标，那么一个颜色范围时0-255，映射到0-1，如果乘二减一之后，UV坐标0应该对应颜色值128，UV坐标-1对应颜色值0，UV坐标1对应颜色值255。

所以如果想要让UV往左偏移0.5应该设置颜色值为64，以此类推。

因此模运算为2，t取值在0.0-2.0范围内，t增长超过2之后，模运算后会重置到0再开始一个循环。

t用在下面的这个运算中

```
uv - f.xy * t * flowIntensity
```

f.xy范围在-1 ~ 1，t范围在0.0 ～ 2.0，flowIntensity流动变化速度假设为1

假如某一点像素的f.xy在映射转换前取到的原贴图值是绿色，red = 13，green = 251，那么xy = (-0.89，0.96)，uv - xy往左上移动。

t随时间增加，位移的范围越远，t = 0.5，xy=(-0.445，0.48)，t=1.5，xy=(-1.335，1.44)。

flowIntensity越大速度越快，越小越慢

如果原贴图值是橙色，red = 230，green = 184，那么xy = (0.89，0.44)，uv-xy往右上移动

最后通过lerp，以及设置一个w值，当t变换到1时，两张贴图的渐变替换颜色，第一张图在整个变化周期过一半时会渐变成渲染第二张颜色，此时因为第二张的t1时间是在t基础上+1，所以比第一张快一秒，刚好模2重置为0，从头开始，再等道第二张再变化到一半时，此时第一张因为时间变化刚好重置为0，再渐变到第一张渲染，这样周而复始的效果就是最后我们需要的



代码:

```
float t = _Time.y / duration;
float t0 =  t    % 2;
float t1 = (t+1) % 2;

float w  = abs(t % 2 - 1);

float4 c0 = getLayer(i.uv, t0);
float4 c1 = getLayer(i.uv, t1);
return lerp(c0, c1, w);

float4 getLayer(float2 uv, float t) 
{
float4 f = tex2D(flowMap, uv) * 2 - 1;
return tex2D(testTex, uv - f.xy * t * flowIntensity);
}
```



