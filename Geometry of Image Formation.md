# Geometry of Image Formation


---

##一、相机成像原理

$${1\over f}={1\over a}+{1\over b}$$

$${Y\over a}={y\over b}$$

- 物距越大，像越小（$Y,b$不变，$a$越大$y$越小）
- 像距越大，像越大（$Y,a$不变，$b$越大$y$越大）
## 二、单视角几何


- 规律：真实世界里的平行线在成像平面上为灭影线，相交于灭点；反之，成像为平行线，真实世界中为以光心为灭点的灭影线
- 应用：利用真实世界中的平行线测图片上两点的实际距离
    - 测量地面上两点距离时，可旋转图片使地板上的平行线在图像上也为平行线

## 三、双透视绘图仪
两个参数：

- 相机高度（两定点间距），改变**形状**不改变大小，相机越高，椭圆越圆，相机越低，椭圆越椭
- 相机焦距（相当于改变玻璃板的位置），改变**大小**不改变形状，焦距越大，椭圆越大，焦距越小，椭圆越小

## 四、Vanishing Point

- 真实世界里地平面上所有方向的线指向的灭点连成的线称为地平线
- **地平线永远与光心同高（相对地平面）**
- 应用：
    1.测身高，过脚底的线与地平线交点找到灭点，从该灭点反射一条过头顶的线，两条线在尺子上的交点间距即位身高，相当于人走到尺子旁边会有多高
    2.地平线代表着相机（摄像师）实际高度

---
##五、齐次坐标法

### Homogeneous Coordinates齐次坐标
    
- 成像平面上的点坐标为$(x,y,1)$
- 平面上的点都看成原点到该点的一个向量，物理世界上的点也在这个向量方向上，只是差一个尺度因子
- 平面上的直线都看成原点和该直线所确定的一个平面，用平面法向量描述
    - 直角坐标形式：$[a,b,c]$
    - 极坐标形式：$[\rho,\theta]$
    - 转换关系：
$$cos\theta={a\over {\sqrt {a^2+b^2} \quad}} $$

    $$sin\theta={b\over {\sqrt {a^2+b^2} \quad}} $$

$$\rho={-c\over {\sqrt {a^2+b^2} \quad}} $$

- 直线满足直线方程$$ax+by+cz=0$$
  则成像平面上的直线方程为$$ax+by+c=0$$
-  两点成一线：两点向量做外积求出旋转向量即为直线法向量$l=x\times x'$
 两线交点：两线法向量做外积$x=l\times l'$
 
- Point-line Duality对偶性：（点向量和线法向量）点乘点得线，线乘线得点

- 齐次坐标：$[x,y,z]$转换回二维坐标：$[{x\over z},{y\over z}]$

### 理想的点和线

- Infinite Point：若得到的两线交点齐次坐标为$[x,y,0]$，即代表两线平行，求得的交点即为灭点，$[x,y]$为这组平行线的方向。

    结论：平行线$[a,b,c]$的灭点为$[b,-a,0]$
- Infinite Line：法向量为$[0,0,1]^T$
因为$\left[\begin{matrix}
0,0,1
\end{matrix}\right]
\left[\begin{matrix}
x\\y\\0\\
\end{matrix}\right]=0$

- 法向量为$[a,b,0]$的直线一定过图像主点（注意法向量确定的是直线与原点所在的平面！不是某条直线！）
    
    
---
##六、坐标转换

###方法一：平移向量+三个列向量表示旋转矩阵

- A坐标系到B坐标系的转换：$$P_a=R_{ab}P_b+T_{ab}$$
    - $T_{ab}$表示A坐标系原点到B坐标系原点的**平移向量**，在A坐标系下表示
    - $R_{ab}=\left[
\begin{matrix}r_1,r_2,r_3\end{matrix}\right]$表示A坐标系到B坐标系的**旋转矩阵**，由三个列向量组成：
$r_1$表示B坐标系的$x$轴方向在A坐标系下的方向
$r_2$表示B坐标系的$y$轴方向在A坐标系下的方向
$r_3$表示B坐标系的$z$轴方向在A坐标系下的方向
- $M_{ab}$表示A坐标系到B坐标系的**变换矩阵**
    - 定义：将旋转和平移合并到一个矩阵中    $$M_{ab}=
\left[\begin{matrix}
R_{ab},T_{ab}\\
0,0,0,1\\
\end{matrix}\right]$$
    - 好处：便于多个坐标系之间的转换    $M_{ac}=M_{ab}M_{bc}$
- 反变换
    - 旋转矩阵的逆为旋转矩阵的转置（正交矩阵性质）$R_{ba}=R_{ba}^T$
    - 变换矩阵的逆$$M_{ba}=
\left[\begin{matrix}
R_{ab}^T,-R_{ab}^TT_{ab}\\
0,0,0,1\\
\end{matrix}\right]$$
    - 平移向量的逆$T_{ba}=-R_{ab}^TT_{ab}$，*由此可得相机光心的世界坐标为   $P_{w}=T_{wc}=-R_{cw}^TT_{cw}$*

###方法二：n个真正的移动叠加==>n个四维变换矩阵连续右乘

- **三步法**将相机坐标变换到世界坐标系
    - 将光心平移到世界坐标系原点
$$T=
\left[\begin{matrix}
I,T_{cw}\\0,0,0,1\\
\end{matrix}\right]=\left[\begin{matrix}
1,0,0,0\\0,1,0,5\\0,0,1,10\\0,0,0,1\\
\end{matrix}\right]$$
    - 绕$x$轴逆时针转$90^\circ$将相机坐标系$z$轴与世界坐标系$z$轴对齐
$$R_{x,90^\circ}=
\left[\begin{matrix}
1,0,0,0\\0,cos90^\circ,-sin90^\circ,0\\0,sin90^\circ,cos90^\circ,0\\0,0,0,1\\
\end{matrix}\right]=\left[\begin{matrix}
1,0,0,0\\0,0,-1,0\\0,1,0,0\\0,0,0,1\\
\end{matrix}\right]$$
    - 绕$z$轴逆时针转$90^\circ$将相机坐标系$x,y$轴与世界坐标系$x,y$轴对齐
$$R_{z,180^\circ}=
\left[\begin{matrix}
cos180^\circ,-sin180^\circ,0,0\\sin180^\circ,cos180^\circ,0,0\\0,0,1,0\\0,0,0,1\\
\end{matrix}\right]=\left[\begin{matrix}
-1,0,0,0\\0,-1,0,0\\0,0,1,0\\0,0,0,1\\
\end{matrix}\right]$$
（注意：默认均为右手坐标系）
- Golden Rule：每步变换以当前的坐标轴为准的（即第三步是绕第二步变换之后的$z$轴变换），变换一步就右乘一个变换矩阵（四维）
$$M_{cw}=TR_{x,90^\circ}R_{z,180^\circ}$$

---

##七、Pinhole Model

###相机模型的三个参数：

$$\left[\begin{matrix}x\\1\\\end{matrix}\right]=
L{KM\left[\begin{matrix}x\\1\\\end{matrix}\right]}$$

- $L$（**内参**）描述镜头参数
- $K$（**内参**）描述针孔（光心）与传感器的空间关系
- $M$（**外参**）描述机身的运动

针孔模型：在小孔成像模型基础上，将成像平面由针孔后移到针孔前，距离仍为焦距$f$（成像由倒立变为正立）
###投影方程：
$$x'=f{X\over Z}$$

$$y'=f{Y\over Z}$$

- 理解一：除以$Z$是将3D世界收缩到距与相机距离为1的2D平面上，距离越远的点收缩得越厉害；乘$f$是将收缩的2D平面缩放到虚拟成像平面上（与相机距离为$f$且在相机前）
- 理解二：成像平面大小不变，则焦距越大，视野越小。$x',y'$不变，则$f$越大，$X,Y$越小
    
###估测光心位置的方法
1.标定
2.利用以光心为灭点的放射线成像为平行线
    
创建全景地图时，希望相机的旋转总是以光心为中心的，因此要估计光心位置
###滑动变焦（垂直效应）
- 相机的两个因素：光心位置和焦距
- 同时改变两个因素，会营造三维移动的幻觉，例如近处物体远离，远处物体靠近等
- 物体像的大小变化取决于$f\over Z$，若$f\over Z$变大则图像上物体看起来将变大或者向前移动
    
    $$x'={f\over Z}X$$

---
##八、相机投影过程
###第一步：物空间到像空间（相机坐标到图像坐标）
$$Z_c\left[\begin{matrix}x'\\y'\\1\\\end{matrix}\right]=\left[\begin{matrix}fX_c\\fY_c\\Z_c\\\end{matrix}\right]$$

- $\left[\begin{matrix}x'\\y'\\1\\\end{matrix}\right]$为像空间齐次坐标（图像坐标），$\left[\begin{matrix}X_c\\Y_c\\Z_c\\\end{matrix}\right]$为物空间坐标（相机坐标）


- 图像坐标和相机坐标都用齐次坐标形式：

    $$Z_c\left[\begin{matrix}x\\y\\1\\\end{matrix}\right]=\left[\begin{matrix}f,0,0,0\\0,f,0,0\\0,0,1,0\\\end{matrix}\right]\left[\begin{matrix}X_c\\Y_c\\Z_c\\1\\\end{matrix}\right]$$
- 将$Z_c$移到右边得到相机投影矩阵$P$，则实现相机坐标$X_c$到图像坐标$x$的转换：
$$x=PX_c$$
###第二步：像空间到像素空间（图像坐标到像素坐标）
####像素坐标系：
- 以左上角为原点$(0,0)$，向右为$c$轴，向下为$r$轴
- $(u,v)$代表第$u$列、第$v$行的像素点
####像素坐标系的两个要素：
- 原点：左上角$(0,0)$
- 主点：光学中心$(O_c,O_r)$
（主点是图像上与光心对齐的地方，安装精确的情况下主点在图像中心处，不精确时要通过标定确定主点位置）

 
####变换公式：
1. 基本公式
        主点移到像素坐标原点：通过平移实现
    （理解：公式左右分别用像素坐标和图像坐标计算该点到主点的距离是多少像素）

    $$(u-O_c)={x\over s_x}$$$$(v-O_r)={y\over s_y}$$

    - $u,v$为该点的像素坐标，单位是像素
    - $x,y$为该点的图像坐标（前两维），单位是$mm$或者$cm$
    - $s_x，s_y$为单个像素的宽度和高度，单位是$mm$或者$cm$


2. 实际公式（考虑各种因素）
写成矩阵形式：

    $$\left[\begin{matrix}u\\v\\1\\\end{matrix}\right]=
\left[\begin{matrix}\alpha_x,s,P_x\\0,\alpha_y,P_y\\0,0,1\\\end{matrix}\right]
\left[\begin{matrix}x\\y\\1\\\end{matrix}\right]$$

- $\alpha_x,\alpha_y$是尺度因子，描述由图像坐标变到像素坐标的缩放，由像素尺寸决定
- $P_x,P_y$是主点位置，取决于相机光心与成像平面安装的相对位置（相机镜头可能因重力而倾斜导致光心上下偏移）
- $s$是倾斜因子，描述实际成像平面相对于理想成像平面的不平行程度（由于光轴倾斜导致）

---
###总结：相机投影过程
```flow
st=>start: start
op1=>operation: 相机坐标
op2=>operation: 图像坐标
op3=>operation: 像素坐标
e=>end

st->op1->op2->op3->e
```

- 推导：

$$Z_c\left[\begin{matrix}u\\v\\1\\\end{matrix}\right]=
\left[\begin{matrix}\alpha_x,s,P_x\\0,\alpha_y,P_y\\0,0,1\\\end{matrix}\right]PX_c$$

$$=\left[\begin{matrix}{\alpha}_x,s,P_x\\0,\alpha_y,P_y\\0,0,1\\\end{matrix}\right]
\left[\begin{matrix}f,0,0,0\\0,f,0,0\\0,0,1,0\\\end{matrix}\right]
\left[\begin{matrix}X_c\\Y_c\\Z_c\\1\\\end{matrix}\right]$$

$$=\left[\begin{matrix}{\alpha}_xf,sf,P_x\\0,\alpha_yf,P_y\\0,0,1\\\end{matrix}\right]
\left[\begin{matrix}I_{3\times3},0\\\end{matrix}\right]
\left[\begin{matrix}X_c\\Y_c\\Z_c\\1\\\end{matrix}\right]$$

$$=\left[\begin{matrix}f_x,S,P_x\\0,f_y,P_y\\0,0,1\\\end{matrix}\right]
\left[\begin{matrix}I_{3\times3},0\\\end{matrix}\right]
\left[\begin{matrix}X_c\\Y_c\\Z_c\\1\\\end{matrix}\right]$$

- 记作：

$$Z_c\left[\begin{matrix}u\\v\\1\\\end{matrix}\right]=K\left[\begin{matrix}I_{3\times3},0\\\end{matrix}\right]
\left[\begin{matrix}X_c\\Y_c\\Z_c\\1\\\end{matrix}\right]$$

- $\left[\begin{matrix}I_{3\times3},0\\\end{matrix}\right]$的作用是实现向量维度变化（四维变三维），保证矩阵乘法正确
（单视角几何中此矩阵才是一个对角阵，多视角几何中将不再是对角阵）
- $K$为**校准矩阵**，包含三部分：缩放因子（焦距改变和像素尺度的都包括）、主点位置、倾斜因子

---
##九、多视角几何

- 相机坐标系作为第一人称坐标系，世界坐标系作为第三人称坐标系（恒定不变）
- 单视角几何：相机坐标系作为原始坐标系
  多视角几何：世界坐标系作为原始坐标系
###多视角几何下相机投影：
- 第一步：世界坐标->相机坐标

    $$X_c=M_{cw}X_w=
\left[\begin{matrix}R,t\\0,1\\\end{matrix}\right]
\left[\begin{matrix}X_w\\Y_w\\Z_w\\1\\\end{matrix}\right]$$

  第二步：相机坐标->图像坐标->像素坐标（与单视角几何投影过程一样）

- 两步合成：

    $$Z_cX_{uv}=K\left[\begin{matrix}R,T\\\end{matrix}\right]X_w$$

###两种特殊情况
这两种特殊情况起始维度与目标维度相等，称为齐次变换

- 空间中的3D平面
    
    $$Z_c\left[\begin{matrix}u\\v\\1\\\end{matrix}\right]=HX_w=H\left[\begin{matrix}X_w\\Y_w\\1\\\end{matrix}\right]$$
    - $H$为单应矩阵，描述3D平面到2D平面的转换
    - 因为空间中是一个平面，可以设世界坐标$z$轴正好与平面法向量重合，则平面上的点$Z_w=0$ 

- 相机做纯旋转运动

    $$\left[\begin{matrix}u\\v\\1\\\end{matrix}\right]=K[R]\left[\begin{matrix}X_w\\Y_w\\Z_w\\\end{matrix}\right]$$
    - 纯旋转时 $T=0$，即相机只绕光心旋转
    - 多张图像之间均为纯旋转关系时，可以拼接成一张全景图像
    - 纯旋转无法形成视差，图像中所有物体都相对静止，无法3D重建
    
---
##十、相机标定

- 目的是为了获得三个内参：
    - 焦距$f$
    - 主点$(u_O,v_O)$
    - 径向畸变参数$r_1,r_2,r_3$……
        
###方法一：几何标定法
一张图像，包含三组正交的平行线，可在有限平面内得到三个正交的灭点,$A$在左$B$在右$C$在下

- 由$A,B$确定出地平线，由地平线相对图像中线的变化可知相机相对地面的位姿
    - 地平线倾斜，相机绕光轴有旋转
    - 地平线上移，相机俯视；地平线下移，相机仰视
- $\triangle ABC$的垂心为主点$H$，可确定光心位置
- $\triangle ABC$与光心$O$连线组成一个四面体，$OH$的长度即位焦距$f$

    $$\angle COA=90^\circ ,\angle COB=90^\circ$$
    
    $$f=d_1d_2-d_3^2$$  

    $d_1,d_2,d_3$以像素为单位
    
###方法二：使用Matlab的标定包进行标定，选多张不同角度的标定板照片，标定的结果：

- Focal Lenth：$f_c=[3351,3352]$ 
焦距，分别是$x,y$轴向焦距，因为像素不是正方形
- Principal Point：$cc=[2015,1511]$      
主点，不在图像实际中心，可能因为相机感光芯片安装位置不一定精确，也可能由最优化导致（重投影误差最小）
- Skew：$alpha_c=[0.0000]$
倾斜因子$s$
- Distortion：$k_c=[0.070,0.015,-0.023]$
径向畸变参数


