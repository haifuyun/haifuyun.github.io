---
title: Computer Graphics Viewing transformation
date: 2021-06-18
categories: [Graphics, Viewing transformation]
tags: [Graphics]
math: true
---

#### View / Camera Transformation

如何呈现一个画面？

Define the camera first

首先定义一个摄像机

* Position (位置)
  $$
  \vec e
  $$
  
* Look-at / gaze direction (朝向物体方向)
  $$
  gˆ
  $$
  
* Up direction (顶上方向)
  $$
  tˆ
  $$

然后摄像机朝向物体

当物体在空间中是随意摆放的，摄像机的位置和角度是随机不一样的

我们为了标准化设置，我们将摄像机的标准位置设置在

原点(origin)  (0,0,0)

顶部朝向与Y轴方向一致

看向-Z方向

将处于空间任意位置的摄像机变换到标准位置和朝向的矩阵称为Mview

Transform the camera by Mview



Mview in math?

Translates e to origin

Rotates g to -Z

Rotates t to Y

Rotates (g x t) To X



Mview = RviewTview

Translate e to origin
$$
Tview
=
\left [
\begin{matrix}
1 & 0 & 0 & -xe \\
0 & 1 & 0 & -ye \\
0 & 0 & 1 & -ze \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
$$


Rotate g to -Z, t to Y, (g x t) To X


$$
R^{-1}view
=
\left [
\begin{matrix}
x(gxt) & xt & x-g & 0\\
y(gxt) & yt & y-g& 0 \\
z(gxt) & zt & z-g & 0 \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
=>
Rview
=
\left [
\begin{matrix}
x(gxt) & y(gxt) & z(gxt) & 0\\
xt & yt & zt & 0 \\
x-g & y-g & z-g & 0 \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
$$


将摄像机变换到标准位置，物体也与相机一起变换

以上就是ModelView变换



---



#### **Projection transformation**

3D to 2D

Orthographic projection

Perspective projection



#### Orthographic projection

Translate (**center** to origin) first, then scale (length/width/height to **2**)


$$
Mortho=
\left [
\begin{matrix}
2 /r-l & 0 & 0 & 0\\
0& 2/t-b, & 0 & 0 \\
0 & 0 & 2/n-f & 0 \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
\left [
\begin{matrix}
1 & 0 & 0 & -(r+l)/2\\
0 & 1 & 0 & -(t+b)/2 \\
0 & 0 & 1 & -(n+f)/2 \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
$$

#### Perspective Projection

透视投影是将一个矩形立方体空间的后平面，往内部挤压，挤成一个与前平面等宽高的面，这个过程就是近大远小的空间变换成能进行平行投影的长方体空间，然后再进行平行投影变换，就完成了屏幕空间的转换，数学的推到细节可在网上搜索到，这里就不做太多重复详解，以下是变换的矩阵组合


$$
Mpersp->ortho =
\left [
\begin{matrix}
n & 0 & 0 & 0\\
0 & n & 0 & 0 \\
0 & 0 & n+f & -nf \\
0 & 0 & 1 & 0
\end{matrix}
\right ]
$$

$$
Mpersp = Mortho Mpersp->ortho
$$



![](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Viewing%20transformation/2.png)

![](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Viewing%20transformation/1.png)
