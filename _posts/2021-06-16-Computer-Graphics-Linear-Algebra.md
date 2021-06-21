---
title:  Computer Graphics Linear Algebra
date: 2021-06-16
categories: [Graphics, Linear Algebra]
tags: [Graphics]
math: true
---

资料来源：https://sites.cs.ucsb.edu/~lingqi/teaching/games101.html

---





前段时间学习完成了Games101的计算机图形学入门课程作业，一直没抽时间回顾下知识点，所以以个人理解，写的精简点，用来回顾搜索。



本篇以线性代数(Linear Algebra)基础知识点开始.

#### Vectors(带方向的长度，指向)

$$
\vec a = \overrightarrow{AB} = B - A
$$

#### ![截屏2021-06-16 下午1.16.11](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Linear%20Algebra/vector.png)		



#### [Magnitude](https://en.wikipedia.org/wiki/Magnitude_(mathematics)#Vector_spaces) (length(长度) - 向量也等于与自身点积的平方根) 

$$
\begin{Vmatrix} \vec a \end{Vmatrix} = \sqrt{A^2 + B^2}
$$

$$
\begin{Vmatrix} \vec b \end{Vmatrix} = \sqrt{B^2 + B^2}
$$



```
Eigen
----
VectorXf v(2);
MatrixXf m(2,2), n(2,2);
  
  v << -1,
       2;
  
  m << 1,-2,
       -3,4;
       
Output:

v.norm() = 2.23607
m.norm() = 5.47723

norm() = sqrt(dot(A, B))

In Eigen，由于向量是矩阵的一个特例，它们也被隐含地处理在那里，所以矩阵-向量积实际上只是矩阵-矩阵积的一个特例，向量-向量外积也是如此

- Scalar * Vector use *
- Scalar * Martix use *
- Martix * Martix use *
- Martix * vector use *
- vector * vector use dot 
- vector * vector use cross

```



#### Unit vector(单位向量)

$$
\hat a = \vec a / \vec a
$$

```
a = a.normalized() = a.norm() / a.norm() = sqrt(dot(a, a)) / sqrt(dot(a, a))
a = |a| / |a|
#向量自身的长度相除
```



#### Dot(scalar) product(点乘，标量乘)

$$
\vec a · \vec b = \begin{Vmatrix} \vec a \end{Vmatrix} \begin{Vmatrix} \vec b \end{Vmatrix} cos \Theta
$$

$$
cos \Theta = \vec a · \vec b \div \begin{Vmatrix} \vec a \end{Vmatrix} \begin{Vmatrix} \vec b \end{Vmatrix}
$$

#### For unit vectors

$$
 \vec a = 1 
$$

$$
\vec b = 1
$$

$$
cos \Theta = \vec a · \vec b \div \begin{Vmatrix} 1 \end{Vmatrix} \begin{Vmatrix} 1 \end{Vmatrix}
$$

$$
cos \Theta = \vec a · \vec b
$$

```
dot(a,b)
#用于求两向量夹角角度
```



#### Cross (vector) Product(叉乘，向量乘)

$$
\vec x \times \vec y = + \vec z
$$

$$
\vec y \times \vec x = - \vec z
$$

$$
a \times b = \begin{bmatrix}
& YaZb - YbZa & \\
& ZaXb - XaZb & \\
& XaYb - YaXb &
\end{bmatrix}
$$

Determine **left / right**

Determine **inside / outside**

![crossProduct](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Linear%20Algebra/crossProduct.png)



#### Matrices

Array of numbers (m × n = m rows, n columns)


$$
\begin{matrix}
1 & 2  \\
4 & 5  \\
7 & 8 
\end{matrix}
$$


\# (number of) columns in A must = # rows in B  

一个矩阵列的数量必须等于另一个矩阵行的数量

或

一个矩阵行的数量必须等于另一个矩阵列的数量

(M x N) (N x P) = (M x P)


$$
\left \{
\begin{matrix}
1 & 2 \\
4 & 5 \\
7 & 8
\end{matrix}
\right \}
$$



$$
\left \{
\begin{matrix}
1 & 2 & 3 & 4 \\
4 & 5 & 6 & 8 \\
\end{matrix}
\right \}
$$



$$
\left \{
\begin{matrix}
? & ? & ? & ? \\
? & ? & ? & ? \\
? & ? & ? & ? \\
\end{matrix}
\right \}
$$


(3 x 2)(2 x 4) = (3 x 4)



#### Matrix-Matrix Multiplication

矩阵相乘顺序不能调换

(AB and BA are different in general)

Associative and distributive

A(B+C) = AB + AC



#### Matrix-Vector Multiplication


$$
\begin{bmatrix}
-1 & 0\\
 0  & 1
\end{bmatrix}

\begin{bmatrix}
x\\
y
\end{bmatrix}
=
\begin{bmatrix}
-x\\
y
\end{bmatrix}
$$


#### Transpose of a Matrix

Switch rows and columns


$$
\begin{matrix}
1 & 2 \\
3 & 4 \\
5 & 6
\end{matrix} ^ \intercal
=
\begin{matrix}
1 & 3 & 5 \\
2 & 4 & 6
\end{matrix}
$$


Property


$$
(AB)^\intercal = B^\intercal A^\intercal
$$


#### Identity Matrix and Inverses


$$
I_{3\times 3} = 
\begin{matrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0	& 1
\end{matrix}
$$

$$
AA^{-1} = A^{-1}A = I
$$

$$
(AB)^{-1} = B^{-1}A^{-1}
$$



#### Vector multiplication in Matrix form

Dot product?


$$
a·b = a^\intercal b = 
\left \{
\begin{matrix}
Xa & Ya & Za \\
\end{matrix}
\right\}
\left \{
\begin{matrix}
Xb \\
Yb \\
Zb
\end{matrix}
\right\}
=
\left \{
\begin{matrix}
XaXb + YaYb + ZaZb \\
\end{matrix}
\right\}
$$


Cross product?


$$
\vec a \times \vec b = A *b = 
\left \{
\begin{matrix}
0 & -Za & Ya & Xb \\
Za & 0 & -Xa & Yb \\
-Ya & Xa & 0 & Zb
\end{matrix}
\right\}
$$


向量a的对偶矩阵

