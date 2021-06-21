---
title: Computer Graphic Screen
date: 2021-06-19
categories: [Graphics, Screen]
tags: [Graphics]
math: true
---

资料来源：https://sites.cs.ucsb.edu/~lingqi/teaching/games101.html

---





##### What’s after MVP?

**M**odel transformation (placing objects)

**V**iew transformation (placing camera)

**V**iew transformation (placing camera)

**P**rojection transformation

* Orthographic projection (cuboid to “canonical” cube [-1, 1]3) 

* Perspective projection (frustum to “canonical” cube)



#### Canonical cube to Screen

将物体画在屏幕上，屏幕是由像素组成的，一个像素颜色由RGB三种颜色混合构成

屏幕覆盖范围(0，0)至(width，height)

像素索引从(0, 0) to (width - 1, height - 1)

像素坐标中心是(x + 0.5，y + 0.5)

无Z方向

变换到XY平面，[-1, 1]2 to [0, width] x [0, height]

Viewport transform matrix:
$$
\left [
\begin{matrix}
width/2 & 0 & 0 & width/2\\
0 & height/2 & 0 & height/2 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1
\end{matrix}
\right ]
$$


---

绘制屏幕上的画面，就是绘制空间中的物体，在获得屏幕空间坐标信息后，绘制物体，物体在屏幕上是由一个个点连成线，再由线绘成三角形，三角形组成物体。



在屏幕坐标中，有哪些像素是近似可以连起来成一个三角形的?

![1](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Screen/1.png)

一个简单的方法，可以进行采样(*采样*一般指取样。取样是指从总体中抽取个体或样品的过程，也即对总体进行试验或观测的过程。分随机抽样和非随机抽样两种类型)

Sampling is a core idea in graphics.

We sample time (1D), area (2D), direction (2D), volume (3D) ...

Sample If Each Pixel Center Is Inside Triangle

![2](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Screen/2.png)

```
for (int x = 0; x < xmax; ++x)
    for (int y = 0; y < ymax; ++y)
      image[x][y] = inside(tri,
                           x + 0.5,
                           y + 0.5);
```



Inside? Recall: Three Cross Products!

![3](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Screen/3.png)

只检查在三角形内的像素，不遍历全屏幕，减少采样数量

```c++
const Triangle& t

auto v = t.toVector4();

auto ext_pt_x = std::minmax_element(v.begin(), v.end(),
                                    [](const Eigen::Vector4f& lhs, const Eigen::Vector4f & rhs){
                                         return lhs.x() < rhs.x();
                                    });
 auto ext_pt_y = std::minmax_element(v.begin(), v.end(),
                                    [](const Eigen::Vector4f& lhs, const Eigen::Vector4f & rhs){
                                         return lhs.y() < rhs.y();
                                    });
 xMinBox = floor(ext_pt_x.first->x());
 xMaxBox = floor(ext_pt_x.second->x());
 yMinBox = floor(ext_pt_y.first->y());
 yMaxBox = floor(ext_pt_y.second->y());

for (int x = xMinBox; x <= xMaxBox; ++x){
  for (int y = yMinBox; y <= yMaxBox; ++y){   
      if(insideTriangle(x,y,v3f)){
          Eigen::Vector3f point(x,y,1.0f);
          set_pixel(point,t.getColor());
      };
  }
}

static bool insideTriangle(int x, int y, const Vector3f* _v)
{   
    Eigen::Vector3f pt;
    pt << (float)x, (float)y, 0.0f;
    bool isInside = isSameSide(pt, _v[0], _v[1], _v[2]) && isSameSide(pt, _v[1], _v[0], _v[2])
      && isSameSide(pt, _v[2], _v[0], _v[1]);
    return isInside;

}

```

