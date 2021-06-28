---
title: Computer Graphics Shading
date: 2021-06-23
categories: [Graphics, Shading]
tags: [Graphics]
math: true
---

资料来源：https://sites.cs.ucsb.edu/~lingqi/teaching/games101.html

---

在绘制像素后，我们要对其着色，着色模型使用Blinn-Phong Reflectance Model，着色的像素称点为shading point



着色点需要使用到以下参数

摄像机观察着色点方向，Viewer direction - v

物体表面法线，Surface normal - n

光照方向，Light direction - l

物体表面参数，物体颜色、物体光泽度 ，color、shininess

![1](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/1.png)



#### 1.Diffuse Reflection

光照射到物体表面然后被均匀的散射都各个方向，所有观察方向的表面颜色都是一样的

那么需要思考一个问题，光照射到物体上，物体收到的光占光总能量的多少？

答案是：

我们可以根据光线入射角度和物体表面法线的夹角角度获得能量百分比，如果是垂直入射获取到的能量是100%，光线与平面角度越接近于0，能量越少

![2](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/2.png)

![3](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/3.png)

#### 2.Specular Term (Blinn-Phong)

物体在特定角度观察它时，会看到物体上有很小一处有高光，观察角度越接近光的反射方向，越容易接收到完整的反射光能量



![4](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/4.png)

![5](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/5.png)

物体高光的可见范围其实是很小的角度，所以我们要设定一个P值，让可见角度范围控制在一定范围内

![6](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/6.png)



3.Ambient Term

物体固有色，忽略光照和阴影投射，这是近似的一个颜色，没有光照时没有任何颜色，但是我们虚拟一个假的

![7](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/7.png)




$$
L=La +Ld +Ls
=kaIa +kd(I/r^2)max(0,n·l)+ks(I/r^2)max(0,n·h)^p
$$


---

#### Shading Frequencies 着色频率

Shade each triangle (flat shading)

逐三角形着色

![8](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/8.png)

Shade each vertex (Gouraud shading)

逐顶点着色

顶点法线用顶点周围面法线求和权重平均求的

![9](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/9.png)

Shade each pixel (Phong shading)

逐像素着色

像素法线用顶点法线和重心坐标插值求的

![10](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/10.png)

---

#### Graphics Pipeline

图形管线



![12](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/12.png)

![13](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/13.png)

![14](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/14.png)

![15](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/15.png)

![16](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/16.png)

![17](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/17.png)



以上是图形渲染管线的流程，综合以上步骤我们可以编写如下着色程序(Shader Programs)



```
 
uniform  sampler2D myTexture; 												// program parameter
uniform vec3 lightDir; 																// program parameter
varying vec2 uv; 																			// per fragment value (interp. by rasterizer)
varying vec3 norm;																		// per fragment value (interp. by rasterizer) 

void diffuseShader() {
vec3 kd;

																											// material color from texture 
kd = texture2d(myTexture, uv);												// Lambertian shading model 
kd *= clamp(dot(–lightDir, norm), 0.0, 1.0); 					// output fragment color
gl_FragColor = vec4(kd, 1.0);
}

```





---

#### Texture Mapping



所有的3D物体表面网格带有材质，都会有一张2D的贴图映射到3D物体上，表现更加真实的表皮



![18](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/18.png)

![19](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/19.png)



每个3D模型的三角形顶点坐标对应一张2D贴图的二维坐标

![20](https://raw.githubusercontent.com/haifuyun/haifuyun.github.io/main/_img/Graphics/Shading/20.png)

