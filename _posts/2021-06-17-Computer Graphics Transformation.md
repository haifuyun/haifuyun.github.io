---
title: Computer Graphics Transformation
date: 2021-06-17
categories: [Graphics, Transformation]
tags: [Graphics]
math: true
---

资料来源：https://sites.cs.ucsb.edu/~lingqi/teaching/games101.html

---





笛卡尔坐标系中，空间坐标的位置变换是通过矩阵相乘实现的.

---

Linear Transforms = Matrices




$$
\left [
\begin{matrix}
x^{new} \\
y^{new}
\end{matrix}
\right ]
=
\left [
\begin{matrix}
a & b\\
c & d
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y
\end{matrix}
\right ]
$$

$$
x^{new} = Mx
$$

$$
x^{new} = ax+ by
$$

$$
y^{new} = cx+ dy
$$



2D Scale Matrix


$$
\left [
\begin{matrix}
x^{new} \\
y^{new}
\end{matrix}
\right ]
=
\left [
\begin{matrix}
s & 0\\
0 & s
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y
\end{matrix}
\right ]
$$


2D Reflection Matrix


$$
\left [
\begin{matrix}
x^{new} \\
y^{new}
\end{matrix}
\right ]
=
\left [
\begin{matrix}
-1 & 0\\
0 & 1
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y
\end{matrix}
\right ]
$$


Shear Matrix


$$
\left [
\begin{matrix}
x^{new} \\
y^{new}
\end{matrix}
\right ]
=
\left [
\begin{matrix}
1 & a\\
0 & 1
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y
\end{matrix}
\right ]
$$

Rotation Matrix


$$
\left [
\begin{matrix}
x^{new} \\
y^{new}
\end{matrix}
\right ]
=
\left [
\begin{matrix}
cos \Theta & -sin\Theta\\
sin \Theta & cos \Theta
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y
\end{matrix}
\right ]
$$


---



#### Homogeneous coordinates



有些变换不能以矩阵形式表示


$$
\left [
\begin{matrix}
x^{new} \\
y^{new}
\end{matrix}
\right ]
=
\left [
\begin{matrix}
a & b\\
c & d
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y
\end{matrix}
\right ]
+
\left [
\begin{matrix}
tx \\
ty
\end{matrix}
\right ]
$$


平移不是线性变换

但我们不希望平移成为一个特殊的案例

是否有一个统一的方法来表示所有的转换？ (以及额外消耗是多少？)



添加第三个坐标（w坐标）

2D point = (x, y, 1)

2D vector = (x, y, 0)

平移的矩阵表示


$$
\left [
\begin{matrix}
x^{new} \\
y^{new} \\
w^{new}
\end{matrix}
\right ]
=
\left [
\begin{matrix}
1 & 0 & tx \\
0 & 1 & ty\\
0 & 0 & 1
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y\\
1
\end{matrix}
\right ]
+
\left [
\begin{matrix}
x+tx \\
y+ty \\
1
\end{matrix}
\right ]
$$


所以在齐次坐标下，有这些运算结果:

vector + vector = vector


$$
\left [
\begin{matrix}
xa \\
ya \\
0
\end{matrix}
\right ]
+
\left [
\begin{matrix}
xb \\
yb \\
0
\end{matrix}
\right ]
=
\left [
\begin{matrix}
xa+xb \\
ya+yb \\
0
\end{matrix}
\right ]
$$

point - point = vector


$$
\left [
\begin{matrix}
xa \\
ya \\
1
\end{matrix}
\right ]
+
\left [
\begin{matrix}
xb \\
yb \\
1
\end{matrix}
\right ]
=
\left [
\begin{matrix}
xa-xb \\
ya-yb \\
0
\end{matrix}
\right ]
$$

point + vector = point



$$
\left [
\begin{matrix}
xa \\
ya \\
0
\end{matrix}
\right ]
+
\left [
\begin{matrix}
xb \\
yb \\
1
\end{matrix}
\right ]
=
\left [
\begin{matrix}
xa-xb \\
ya-yb \\
1
\end{matrix}
\right ]
$$

point + point = point (两坐标中点) w = 2  w!=0



$$
\left [
\begin{matrix}
xa \\
ya \\
1
\end{matrix}
\right ]
+
\left [
\begin{matrix}
xb \\
yb \\
1
\end{matrix}
\right ]
=
\left [
\begin{matrix}
xa-xb \\
ya-yb \\
2
\end{matrix}
\right ]
=
\left [
\begin{matrix}
xa-xb /w \\
ya-yb /w\\
2 /w
\end{matrix}
\right ]
=\left [
\begin{matrix}
xa-xb /2\\
ya-yb /2\\
1
\end{matrix}
\right ]
$$


Affine map = linear map + translation

仿射变换 = 线性变换 + 平移



$$
\left [
\begin{matrix}
x^{new} \\
y^{new}
\end{matrix}
\right ]
=
\left [
\begin{matrix}
a & b\\
c & d
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y
\end{matrix}
\right ]
+
\left [
\begin{matrix}
tx \\
ty
\end{matrix}
\right ]
$$

Using homogenous coordinates:



$$
\left [
\begin{matrix}
x^{new} \\
y^{new} \\
1
\end{matrix}
\right ]
=
\left [
\begin{matrix}
a & b & tx \\
c & d & ty\\
0 & 0 & 1
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y\\
1
\end{matrix}
\right ]
$$

Scale



$$
S(sx,sy)
=
\left [
\begin{matrix}
s & 0 & 0\\
0 & s & 0\\
0 & 0 & 1
\end{matrix}
\right ]
$$
Rotation



$$
R(\Theta)
=
\left [
\begin{matrix}
cos\Theta & -sin\Theta & 0\\
sin\Theta & cos\Theta & 0\\
0 & 0 & 1
\end{matrix}
\right ]
$$

Translation



$$
T(tx,ty)
=
\left [
\begin{matrix}
1 & 0 & tx\\
0 & 1 & ty\\
0 & 0 & 1
\end{matrix}
\right ]
$$


---

#### Inverse Transform

逆变换，物体经过缩放矩阵缩放后，可以通过缩放矩阵的逆矩阵给还原，这就是逆矩阵的逆变换的作用
$$
M^{-1}
$$


#### Composite Transform

复合变换

矩阵乘法没有交换性
$$
R45·T(1,0) != T(1,0)·R45
$$




---



#### 3D Transformations


$$
3D point = (x, y, z, 1)^T
$$

$$
3D vector = (x, y, z, 0)^T
$$



In general, (x, y, z, w) (w != 0) is the 3D point:

(x/wa,y/w,z/w)



$$
\left [
\begin{matrix}
x^{new} \\
y^{new} \\
z^{new} \\
1
\end{matrix}
\right ]
=
\left [
\begin{matrix}
a & b & c & tx \\
c & d & d & ty \\
e & f & g & tz \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
\left [
\begin{matrix}
x\\
y\\
z\\
1
\end{matrix}
\right ]
$$

Rotation around x-, y-, or z-axis



$$
Rx(\alpha)
=
\left [
\begin{matrix}
1 & 0 & 0 & 0 \\
0 & cos\alpha & -sin\alpha & 0 \\
0 & sin\alpha & cos\alpha & 0 \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
$$

$$
Ry(\alpha)
=
\left [
\begin{matrix}
cos\alpha & 0& sin\alpha & 0 \\
0 & 1 & 0 & 0 \\
-sin\alpha & 0 & cos\alpha  & 0 \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
$$

$$
Rz(\alpha)
=
\left [
\begin{matrix}
cos\alpha & -sin\alpha & 0 & 0 \\
sin\alpha & cos\alpha & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
$$


从Rx、Ry、Rz构成任何三维旋转？

所谓的欧拉角，就可以从任何三维旋转

在飞行模拟器中经常使用：滚动、俯仰、偏航 (roll, pitch, yaw)

所以有以下公式可以实现任意轴角度的旋转，替代上面三个不同轴的旋转矩阵

Rotation by angle **α** around axis **n**



$$
N = \left [
\begin{matrix}
0 & -nz & ny\\
nz & 0 & -nx\\
-ny& nx & 0
\end{matrix}
\right ]
$$

$$
R(n,\alpha) = cos(\alpha)I + (1-cos(\alpha))nn^T+sin(\alpha)N
$$



$$
R(n,\alpha) = cos(\alpha)I + (1-cos(\alpha))nn^T+sin(\alpha)\left [
\begin{matrix}
0 & -nz & ny\\
nz & 0 & -nx\\
-ny& nx & 0
\end{matrix}
\right ]
$$


```
Eigen::Matrix4f rotation = Eigen::Matrix4f::Identity();
    
    float rad = (angle / 180.0f) * acos(-1);

    Eigen::Matrix3f N = Eigen::Matrix3f::Identity();
    N << 0, -axis.z(), axis.y(),
         axis.z(), 0, -axis.x(),
         -axis.y(),axis.x(), 0;

    rotation.block<3,3>(0,0) = cos(rad) * Eigen::Matrix3f::Identity() +
                (1-cos(rad))*axis*axis.transpose() +
                sin(rad)* N;
```

