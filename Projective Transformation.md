# Projective Transformation

标签（空格分隔）： vision robotics

---

## 一、相机位姿恢复
相机位姿指的是相机坐标系与世界坐标系的关系，也就是变换矩阵$M_{cw}$

---
### （一）旋转的恢复：
根据灭点解出世界坐标系各轴在相机坐标系下的方向


#### 简单情况：已知$x,y$轴方向，求$z$轴方向，通过观测一个灭点即可。

- $z$灭点：将世界坐标系$z$轴方向上的消影点称为$z$消影点。
    世界坐标：$z_\infty =\left[\begin{matrix}0,0,1,0\\\end{matrix}\right]^T$
    像素坐标：$v_z=[u,v,1]^T$
- 关系：$$Zv_z=K[r_1,r_2,r_3|t]z_\infty=K[r_3]z_\infty$$
    - 由外参矩阵只剩下$r_3$推出：相机平移或者绕$z$轴旋转时，相机光轴方向上的消影点在像素坐标系上的位置不变，只有绕$x,y$轴旋转时才会变。
    $$r_3=K^{-1}v_z||K^{-1}v_z||=\left[\begin{matrix}sin\alpha cos\beta\\sin\beta\\cos\alpha sin\beta\\\end{matrix}\right]$$
    - $\alpha,\beta$是世界坐标系$z$轴在相机坐标系下的方向（pan and tilt angles）
    $$\alpha=tan^{-1}{{r_3(1)}\over {r_3(3)}}$$$$\beta=cos^{-1}r_3(2)$$
#### 求三轴方向，观测两个消影点
- $z$消影点和$x$消影点

$$r_3=K^{-1}v_z$$$$r_1=K^{-1}v_x$$$$r_2=r_3\times r_1$$
 








