---
title: Computer Graphics Shading Texture
date: 2021-06-24
categories: [Graphics, Shading Texture]
tags: [Graphics]
math: true
---



---



上一篇描述了着色的整体大概流程，这篇最后描述下着色最后一个环节，材质贴图映射的细节



一张2D贴图纹理会映射到一个3D物体表面，3D物体表面的三维坐标是怎么映射到2D贴图的二维坐标的？



#### 使用重心坐标(Barycentric Coordinates)

用三角形的三个顶点坐标进行内部坐标插值

![1](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/1.png)
$$
(x,y) = \alpha A + \beta B + \gamma C
$$

$$
\alpha + \beta + \gamma = 1
$$


$$
\alpha、\beta、\gamma加起来的总和为1，它们的占比越大越靠近对应的顶点，以此可以插值得到三角形内部所有坐标
$$


![2](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/2.png)

![3](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/3.png)

![4](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/4.png)



当然，重心坐标不仅可以插值坐标，还可以插值其他线性变化的属性，例如颜色、三角形法线、三角形面深度、材质其他属性



简单的贴图映射伪代码:

```
for each rasterized screen sample (x,y):  //Usually a pixel’s center
(u,v) = evaluate texture coordinate at (x,y)  //Using barycentric coordinates!
texcolor = texture.sample(u,v);
set sample’s color to texcolor;   //Usually the diffuse albedo Kd (recall the Blinn-Phong reflectance model)
Using barycentric coordinates!
```

```c++
static std::tuple<float, float, float> computeBarycentric2D(float x, float y, const Vector4f* v){
    float c1 = (x*(v[1].y() - v[2].y()) + (v[2].x() - v[1].x())*y + v[1].x()*v[2].y() - v[2].x()*v[1].y()) / (v[0].x()*(v[1].y() - v[2].y()) + (v[2].x() - v[1].x())*v[0].y() + v[1].x()*v[2].y() - v[2].x()*v[1].y());
    float c2 = (x*(v[2].y() - v[0].y()) + (v[0].x() - v[2].x())*y + v[2].x()*v[0].y() - v[0].x()*v[2].y()) / (v[1].x()*(v[2].y() - v[0].y()) + (v[0].x() - v[2].x())*v[1].y() + v[2].x()*v[0].y() - v[0].x()*v[2].y());
    float c3 = (x*(v[0].y() - v[1].y()) + (v[1].x() - v[0].x())*y + v[0].x()*v[1].y() - v[1].x()*v[0].y()) / (v[2].x()*(v[0].y() - v[1].y()) + (v[1].x() - v[0].x())*v[2].y() + v[0].x()*v[1].y() - v[1].x()*v[0].y());
    return {c1,c2,c3};
}

auto[alpha,beta,gamma] = computeBarycentric2D(pixel_pos_x,pixel_pos_y,v4f);
float Z = 1.0 / (alpha / v[0].w() + beta / v[1].w() + gamma / v[2].w());
float zp = alpha * v[0].z() / v[0].w() + beta * v[1].z() / v[1].w() + gamma * v[2].z() / v[2].w();

zp *= Z;
```



---



接下来是关于处理纹理映射中出现的一些问题，纹理倍率

1.纹理分辨率太小，显示不清晰

2.纹理分辨率太大，显示出现锯齿、摩尔纹



定义一下在贴图上的一个像素称为

A pixel on a texture — a texel (纹理元素、纹素)



### 1.too small

贴图纹理的分辨率太小，没有足够的像素点对应屏幕显示的像素点

当屏幕上某一个像素想要采样显示纹理的纹理元素，映射到某一个纹理的纹理元素位置内，但是纹理坐标是整数值，就会出现多个像素显示同一个纹理元素，我们可以使用插值解决这个问题.

![5](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/5.png)

Bilinear Interpolation 双线性插值

取周围4个最近的像素点样本，纹理值如标示。

两个纹理像素之间的距离定义为1

红点是映射投影到纹理上的点

s为红点在横坐标上的分量，在0-1之间

t为红点在纵坐标上的分量，在0-1之间

lerp(x,v0,v1)插值单维度(横轴)的值后，求的u0、u1，是红点在上下两个横坐标之间的值

再在u0、u1两个坐标之间进行纵向插值，即可得到红点的值

![6](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/6.png)

![7](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/7.png)



### 2.too large

当纹理分辨率太大时超过屏幕分辨率，

因为显示近大远小的透视原因

近处物体较大，需要更多的屏幕像素覆盖物体来完全显示

所以，近处的像素映射到纹理上的覆盖范围较小

远处处物体较小，需要很少的像素覆盖物体即可完全显示

所以，远处的像素映射到纹理上的覆盖范围较大

一个屏幕像素内部就包含很大一块的纹理像素，这块纹理是一直在变化的，它的变化频率很高，可是只用一对一的一个屏幕像素采样一个纹理像素，这样屏幕的采样频率跟不上纹理的变化频率，就会出走样，我们应该用更多的纹理像素平均起来对应作为一个屏幕像素的采样，这样才不会出问题

但是不想用这么多采样点采样纹理怎么办？

Mipmap - 一个屏幕像素覆盖纹理很大一块区域像素，我们立刻就可以知道这块区域的平均值，不就可以不采样了嘛！预处理！

![8](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/8.png)



---



#### MipMap

允许做范围查询，快、不准确、方形区域

从一张高分辨纹理生成一系列成倍缩小的不同分辨率的纹理，提前计算生成

原始纹理为第0层，分辨率往下，层数增高

![10](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/10.png)



那么如何知道怎么查询第几层的，当我们从屏幕像素映射查询纹理像素时，计算得到对应到原始纹理像素的坐标点，然后再计算不同坐标点之间的距离，获得一个大概的矩形面积，纹理坐标上两个坐标之间的距离是一，假设原始纹理分辨率是4x4，如果映射到原始纹理上的近似的矩形面积是4x4，那么在第二层就是我们原始纹理放大了4倍的对应一个像素需要的纹理了

Level 0 4x4

Level 1 2x2

Level 2 1x1 

总结: 屏幕像素 1x1 对应原始纹理像素 占4x4 就需要将原始纹理像素放大4倍对应一个屏幕像素，刚好是level 2 的 1x1



![11](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/11.png)



![12](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/12.png)

但是层与层之间没有过渡，level1和levle2之间没有1.0-2.0层级的过渡，就会出现明显的边界，因此还是需要用Trilinear Interpolation来再不用的层级纹理中进行插值来过渡

![13](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/13.png)

![14](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/14.png)



以上是MipMap解决的问题，但是MipMap也有不能解决的问题，因为MipMap只能查询正方形区域，当屏幕一个像素(正方形区域)显示的是斜着角度拉伸的直线，对应到纹理应该是一个长方形的区域，那MipMap就不能正确查询到对应区域了，因为MipMap只能查询正方形区域，而各向异性过滤能解决某个轴 (x或y) 变窄或者变长后查询纹理显示不正确的问题，通过将原始纹理在各个轴方向生成不同的压缩层级进行查询显示，但是斜着的长条形各项异性过滤也不能很好的解决

各向异性过滤会增加额外的1/3开销，

![16](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/16.png)

![15](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/15.png)



![17](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading%20Texture/17.png)

