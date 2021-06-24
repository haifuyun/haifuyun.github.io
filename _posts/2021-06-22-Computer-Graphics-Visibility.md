---
title: Computer Graphics Visibility
date: 2021-06-22
categories: [Graphics, Visibility]
tags: [Graphics]
math: true
---



---

#### Z-buffering

场景物体前后遮挡的关系

Z-Buffer Algorithm

```
Initialize depth buffer to ∞ During rasterization:
for (each triangle T)
  for (each sample (x,y,z) in T)
    if (z < zbuffer[x,y]) 
      framebuffer[x,y] = rgb; 
      zbuffer[x,y] = z;
    else
  		// do nothing, this sample 
```

![1](/Users/hfy/Documents/Github/haifuyun.github.io/_img/Graphics/Z-buff/1.png)