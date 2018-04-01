# Pose Estimation



---

## 一、视觉特征

### （一）特征提取

#### 1.图像金字塔（基础见《图像金字塔》）

![visual_features 10](media/15232508742194/visual_features%2010.jpeg)

- 图像金字塔：从原图像开始模糊（卷积），得到一系列同采样水平的图像作为金字塔的一层。
**金字塔层内图像尺寸（大小）相同，尺度（模糊程度）不同，层间图像尺寸不同，尺度为倍增关系**

![visual_features 11](media/15232508742194/visual_features%2011.jpeg)


- 相当于滑动的一个蒙版（mask），将蒙版在图像上移动，则整个图像就被模糊了
    - 二维Gaussian函数： 
   $$G(x,y,\sigma)={1\over 2\pi\sigma^2}e^{-x^2+y^2\over 2\sigma^2}$$
    $\sigma$是协方差，表示Gaussian函数在二维上的传递方向，也就是蒙版的移动方向？？
    - 图像模糊：$$G(x,y,\sigma)*I(x,y)=L(x,y,\sigma)$$一张图像中改变内核中的$x,y$让高斯函数在图像上每一点进行卷积，从而模糊整个图像。不同图像间改变内核中的$\sigma$让模糊程度不同。$\sigma$每增加一倍（一个八度），降采样一次，作为下一层金字塔的第一张图，继续模糊得到这一层的图像。
    
#### 2.LoG高斯函数的拉普拉斯算子

![visual_features 13](media/15232508742194/visual_features%2013.jpeg)

![visual_features 14](media/15232508742194/visual_features%2014.jpeg)


概念|定义|意义
--- | --- |---
本征尺度| 对于每个像素，建立一个图像强度关于尺度$\sigma$的函数，（尺度归一化后）函数最大值所对应的尺度就是该像素位置的本征尺度|该像素在其本征尺度下显示最清晰
Gaussian函数 | 可以进行归一化的函数都是是Gaussian函数的导数？？ |？？？
Hessian矩阵|二维高斯函数的二阶导数|
拉普拉斯算子LoG|两种求法：Hessian矩阵的迹，两个高斯函数（$\sigma$不同）的差（DoG）。其图像是一个二维曲面，类似倒置的墨西哥帽子|描述两个$\sigma$取值不同的高斯函数之间的差异DoG，卷积时用LoG在图像上移动类似做斑点匹配？？

![visual_features 17](media/15232508742194/visual_features%2017.jpeg)

- Why normalization？：一维解释

![visual_features 15](media/15232508742194/visual_features%2015.jpeg)

原始图像波形为矩形波，用多个不同$\sigma$值的拉普拉斯算子进行多次卷积后，波形信号消失了，尺度归一化后就可以为了使信号不会随着图像的渐渐模糊而消失。这样使用不同的$\sigma$进行卷积使就会得到相应的拉普拉斯响应，响应最大时的$\sigma$对应的尺度就是该像素的本征尺度，即:
            **自动得到了每个像素的本征尺度**


---

- 例子：图像上一个半径为$r$的黑色圆形斑点，当一个Laplacian算子的内核$$\sigma={r\over \sqrt2}$$
则算子和斑点匹配成功（也就是斑点正好在算子的0线上），这时该图像的本征尺度就是$r$

![visual_features 16](media/15232508742194/visual_features%2016.jpeg)

**用拉普拉斯算子去跟像素匹配，如果拉普拉斯响应有最大值，则匹配成功，说明该像素的本征尺度就是这个拉普拉斯算子的内核**

---

#### 3.SIFT特征点

- 定义：在图形金字塔中用一个拉普拉斯算子去做卷积，若一个点同它周围的$3\times3\times3$的邻域中的26个点相比有最大的Laplacian响应，则它就是一个SIFT特征点

![visual_features 21](media/15232508742194/visual_features%2021.jpeg)


##### 对特征点的描述：

- 尺度不变性：SIFT自动检测每个特征点的本征尺度$\sigma$，取$$arg(\sigma)$$表示成一个圆，圆越大表示该特征点的本征尺度越接近于当前的尺度，这样使得特征点有了在$\sigma$维度上有了描述，具有尺度不变性

![visual_features 23](media/15232508742194/visual_features%2023.jpeg)

- 旋转不变性：特征点具有周围26点邻域中的Laplacian响应最大值，旋转之后它还是最大值点，所以具有旋转不变性

![visual_features 24](media/15232508742194/visual_features%2024.jpeg)


##### 对邻域的描述：（就是描述子）
    
- 尺度不变性：将本征尺度圆归一化到一个$16\times16$的域内，这个$16\times16$的域是尺度不变的，这样在原始的检测中就携带了一个关于尺度的值

![visual_features 25](media/15232508742194/visual_features%2025.jpeg)
    
- 旋转不变性：给本征尺度圆的所有方向建立梯度直方图，找出最大梯度所对应的方向作为这个圆的方向，这样实现了邻域的旋转不变性

![visual_features 26](media/15232508742194/visual_features%2026.jpeg)
![visual_features 2727](media/15232508742194/visual_features%202727.jpeg)


**实际上，不是在对整个邻域建立梯度直方图，而是先将$16\times16$邻域分成$4\times4$个grid，每个grid建立8bin的直方图，$4\times4\times8=128$，则描述子是$128\times1$维向量**

![visual_features 32](media/15232508742194/visual_features%2032.jpeg)

描述子不包含该邻域在原图里的尺度和方向信息，只由$x,y,\sigma$变量确定

**SIFT特征总结**

![visual_features 44](media/15232508742194/visual_features%2044.jpeg)



  步骤   |对象|性质
 --- |---|---
1.检测特征点|像素|由于选取的是$x,y,\sigma$三个方向上的响应最大值，自动完成了确定本征尺度以及旋转不变（尺度不变、旋转不变）
2.邻域归一化|邻域|尺度归一化：本征尺度圆都放到一个$16\times16$邻域内分析；旋转归一化：$16\times16$邻域分成$4\times4$个grid，每个grid建立8bin的直方图
3.建立描述子|邻域|由于第二步，所以描述子具有尺度不变性和旋转不变性，描述子是$128\times1$维向量描述这个特征点和邻域的特征

### （二）特征匹配

两张图片分别提取特征之后，根据描述子进行匹配，之后要去除外点（误匹配）

- 应用
    - mosaic：两幅图先匹配，然后计算两张图的位姿关系，变换第二张图到第一张图的角度然后拼接，在加入第三张图，重复匹配、变换、拼接步骤

![visual_features 37](media/15232508742194/visual_features%2037.jpeg)
![visual_features 38](media/15232508742194/visual_features%2038.jpeg)
![visual_features 39](media/15232508742194/visual_features%2039.jpeg)

 
    - location：从图像集中找出跟当前图像相似度高的，进行定位

![visual_features 41](media/15232508742194/visual_features%2041.jpeg)
![visual_features 43](media/15232508742194/visual_features%2043.jpeg)


## 二、SVD奇异值分解

被高斯用于计算地球绕太阳旋转的轨迹

### （一）SVD分解

- 定义：
SVD是一种方法，将矩阵$A$分解为三个矩阵$UDV$相乘，则由UDV三个矩阵可重建矩阵$A$

(*图中矩阵维数写错了*）![svd 4](media/15232508742194/svd%204.jpeg)

![svd 5](media/15232508742194/svd%205.jpeg)


矩阵|性质|
|---|---
|$U$|$m\times m$，每个列向量都是单位向量，且两两正交，$U^TU=I_{m\times m}$（即酉矩阵）
|$V$|$n\times n$，每个行向量都是单位向量，且两两正交，$V^TV=I_{n\times n}$
|$D$|$m\times n$，奇异值矩阵，是对角阵形式，奇异值都非负且从大到小排列在对角线上，对角线上不为零的值的个数为$A$的秩$r$，所以有$r<=min(m,n)$


- 数学意义：

![svd 6](media/15232508742194/svd%206.jpeg)
![svd 7](media/15232508742194/svd%207.jpeg)

将$A$分解分析：basis+transformed Address，即$U$提供一组正交基basis，每一列代表一个子变量，$D\times V=Scales*Address$提供所有自变量的权重矩阵

- 奇异值与特征值
    都是为了分析矩阵的，特征值eigen value只能用于方阵，奇异值singular value可用于非方阵，奇异值是特征值的推广
    对于一个方阵来说,特征分解后,从特征值和特征向量我们就可以知道矩阵的大量性质.对于非方阵来说,我们也希望得到一个这样信息量巨大的分解,这就是奇异值分解（SVD）

### （二）SVD应用例子：
    
    
#### 1.分解一幅画

![svd 9](media/15232508742194/svd%209.jpeg)
![svd 10](media/15232508742194/svd%2010.jpeg)


第一个矩阵为一组正交的子色条，由于奇异值矩阵中的奇异值急剧减小，说明后面的子色条占据的权重很小，所以可以省略，由前几个主要的子色条就可以近似地重建出整张画
    
#### 2.特征脸eigenfaces

- 一张脸是$100\times 100$的图，记成一个超长的$10000$维向量，$1000$张脸拼成一个$1000\times 10000$的矩阵，

![svd 14](media/15232508742194/svd%2014.jpeg)


SVD分解后选用一部分特征向量和对应的奇异值重建，则每张脸会首先重建出不同的特征，有的是嘴有的是头发

![svd 15](media/15232508742194/svd%2015.jpeg)

    
- 年龄脸
同一个人的脸分解出同样的特征向量，但是用不同的$Address$会有不同的特征向量凸显，从而有不同的年龄的感觉

![svd 18](media/15232508742194/svd%2018.jpeg)


---

概念|定义|意义
---|---|---
$rank(A)$|秩，是奇异值矩阵$D$中非零元素的个数，所以有$r<=min(m,n)$|要完全恢复$A$只需要前$r$个特征向量（正交基）就够了
$null(A)$|零空间，$V_{r:n}$或$V^T_{r:n}$|在零空间中的维度上，$A$的值为零


- 零空间性质：$$A\times V_{r:n}=0$$
*求解最小二乘时特别有用*，最小二乘形式就是$$Ax=0$$说明$x$就是系数矩阵$A$的零空间！！

![ransac1 3](media/15232508742194/ransac1%203.jpeg)


---

#### 3.用svd清理最小二乘法求出的$F$的秩

由两张不同角度的图可以用最小二乘法计算出$F$矩阵，但不满足$rank(F)=2$，可用svd分解来清理矩阵（分解出奇异值矩阵，将最后一个元素置零，再重建$F$）

#### 4.用svd求逆矩阵

$$A=U\times D \times V^T$$

$$A^{-1}=V\times D^{-1} \times U^T$$
其中$D^{-1}=diag\{{1\over \sigma_1} ,{1\over \sigma_2 }, {1\over \sigma_3},……0,0,0\}(\sigma_i>0)$


---

## 三、Least Square Problem


注意：**$rank(A)$就是约束矩阵$A$能提供的最多的独立约束个数,当其少于未知量个数时就要用最小二乘求一个近似解**

以曲线拟合为例

### (一）两种形式

![svd 23](media/15232508742194/svd%2023.jpeg)

 
#### 1. $min||Ax-b||^2$，其中$||b||\ne0$


![svd 26](media/15232508742194/svd%2026.jpeg)


- 首先要建立约束矩阵$A$：矩阵$A$维度为$m\times n$，秩为$r$，表示有$m$约束（点），$n$个未知量，当约束的维度小于未知量个数$n$时，则少一个补一个$1$列向量
这里$A$为$m\times 2$维，有$m$个点，两个未知量（斜率和截距）

- 不同情况：
    - 一个不共线点（$r=m<n$）有无数个解，过一点有无数条直线
    - 两个不共线点（$r=m=n$）有唯一解，两点确定一条直线
    - 多个不共线点（$m>n$）只有最近似解，用最小二乘法使$y$方向上点到直线的总误差最小

![svd 30](media/15232508742194/svd%2030.jpeg)




由此总结第一种形式的最小二乘解法

![第二种LS总结](media/15232508742194/%E7%AC%AC%E4%BA%8C%E7%A7%8DLS%E6%80%BB%E7%BB%93.jpg)


*第三种情况就是最经典的最小二乘问题，$(A^TA)^{-1}A^T$称为$A$的伪逆*


#### 2. $min||Ax||^2$，限制$||x||=1$（因为零解无意义）

第一种形式是计算$y$方向上的误差，第二种形式是计算垂直于直线方向上点到直线的误差

![svd 35](media/15232508742194/svd%2035.jpeg)


- 约束矩阵$A$为$m\times 3$维，有3个未知量
- 将$A$svd分解，取$V$矩阵最后一列就是$x$的解，**相当于$x$的解就是$A$的零空间！**

![svd 37](media/15232508742194/svd%2037.jpeg)
![svd 39](media/15232508742194/svd%2039.jpeg)


由此总结第二种形式的最小二乘法

![ransac1 2](media/15232508742194/ransac1%202.jpeg)





第一种：$min||Ax-b||^2$，其中$||b||\ne0$|与$A$的非零空间相关，$n<m$时，$x=(A^TA)^{-1}A^Tb$
第二种：$min||Ax||^2$，限制$||x||=1$|与$A$的零空间相关，$n<m$时，$x=V_n$（$V$的最后一列）


- 最小二乘在三角化、相机位姿估计中都会用到


---

## 四、RANSAC

Random Sample Consensus随机样本一致

![ransac1 7](media/15232508742194/ransac1%207.jpeg)


- 意义：Least Square对于噪声点很敏感，所以在LS前要先用RANSAC滤去噪音，才能将数据传到LS中求解
- 原理：所有点（样本）分为内点和外点，内点的特点是互相支持，即由一部分内点算出的解，其他的内点也会符合这个解，而外点互相矛盾。所以只要找出支持度高的一组点作为内点传给LS即可，不用管外点是否支持这个解

### （一）RANSAC步骤

以下四步重复进行，选择inliers最多的model作为筛选标准，不符合这个model的点都作为outliers去除

1. Random sampling
    - 一次随机选多少个样本？
为了使一次选取的点都属于内点，则选的点要尽量少，所以应该一次取能确定一个解所需的最少的点数
2. Model building：假设选取的样本都是内点，根据这些样本用LS求出一个model
3. Thresholding：设定一个阈值，在model周围阈值内的点都认为支持这个model
4. Inlier counting：看有多少个点支持这个model，good model会获得more voting

![ransac1 21](media/15232508742194/ransac1%2021.jpeg)


---
- 重复多少次？
取决与outliers占的比例
    - 选择一个点是inlier的概率：$$w={inliers \over outliers }$$
    - 一次建立一个correct model（一次选择的$n$个点都是inlier）的概率：$$w^n$$
    - 重复$k$次不能建立一个correct model（选择的点都不是inlier）的概率：$$(1-w^n)^k$$
    - 设$p$是跑完$k$次RANSAC后成功的概率：$$1-p=(1-w^n)^k$$
    
    则重复次数$k$为：$$k={log(1-p)\over log(1-w^n)}$$
   

![ransac1 26](media/15232508742194/ransac1%2026.jpeg)



---

## 五、Where am I？

### 复习

#### Case 1

**已知一张图像，包含两组互相垂直的平行线**


![where_am_I1 3](media/15232508742194/where_am_I1%203.jpeg)


- 解法：

   
    1. 根据道路的两条平行线确定$z$方向的灭点$v_z$，由内参$K$将$v_z$从像素空间变到光学空间（相机空间）然后归一化，这样就确定了光心到道路灭点的光射线，也就是道路的方向，也是世界坐标$Z$轴的方向$r_3$
    2. 根据另一条垂直的道路用同样的方法确定世界坐标$X$轴的方向$r_1$
    3. 根据正交关系确定出$Y$轴的方向$r_2=r_3\times r_1$，这样相机的旋转矩阵$R=[r_1,r_2,r_3]$就确定了
    

---

#### Case 2 Epiple

**已知两张图像，其中包含重叠的场景**

![where_am_I1 4](media/15232508742194/where_am_I1%204.jpeg)


- 解法：对极约束
    1. 特征匹配
    2. RANSAC去除outliers，8点法计算$E$
    3. $svd(E)$得到4组$R、t$的解
    4. 三角化选出正确的解（深度为正）


---

#### Case 3

**已知一张图像，其中包含一个已知的$3D$世界中的一个平面**

![where_am_I1 5](media/15232508742194/where_am_I1%205.jpeg)


- 解法：单应矩阵
将已知平面的鸟瞰图作为第二张已知图像，两张图像的匹配点间的关系用单应矩阵描述，已知图中的点为二维像素坐标，鸟瞰图中的点也用二维的世界坐标（因为是平面所以可消去一维）表示，都取齐次坐标后得到关系：$$x=\left[\begin{matrix}u\\v\\1\\\end{matrix}\right],X=\left[\begin{matrix}X\\Y\\1\\\end{matrix}\right]$$$$x=HX$$$H$中包含了$K,R,t$

![where_am_I1 9](media/15232508742194/where_am_I1%209.jpeg)

    
    
1. 找出4对$3D$平面上和已知图像该平面上的匹配点，$3D$中的点取两维世界坐标加1凑成3维齐次坐标，图像上的点取像素坐标加1凑成3维齐次坐标
2. $H$有9个自由度，但是尺度不确定，所以有八个未知量，所以至少要用4对点来算$H$，1对点提供约束矩阵$3\times9$维，4个点提供约束矩阵$12\times9$维，可解出唯一的$H$。
    
![where_am_I1 19](media/15232508742194/where_am_I1%2019.jpeg)


如果点更多，则用LS解，约束矩阵$3\times9$维，svd分解出$V$取最后一列就是$H$？？？   
    3. 由$H$不能直接得到$R$，因为不一定满足$R$3个列向量互相正交，所以用LS求一个最接近于$H$的$R$
    
![where_am_I1 20](media/15232508742194/where_am_I1%2020.jpeg)



![pose_projective_transformations 13](media/15232508742194/pose_projective_transformations%2013.jpeg)
![pose_projective_transformations 17](media/15232508742194/pose_projective_transformations%2017.jpeg)
4. 由$H$可直接得到$T$
    

---

#### Case 4 Perspective-n-Point

##### 解法一：直接线性变换

**已知一张图像，其中包含一些已知的$3D$点云**
 
![where_am_I2 1](media/15232508742194/where_am_I2%201.jpeg)

 
- 解法：PnP（Perspective-n-Point）

![where_am_I2 5](media/15232508742194/where_am_I2%205.jpeg)


图像中的$2D$点和点云中的$3D$点匹配，移动相机直到**所有由光心到$2D$点的射线正好穿过所对应的$3D$点**，这样就得到了相机位姿

    

##### 1. 特征检测，将特征点$x=\left[\begin{matrix}u\\v\\1\\\end{matrix}\right]$和$3D$点$X=\left[\begin{matrix}X\\Y\\Z\\1\\\end{matrix}\right]$匹配，根据相机投影得到
    
![where_am_I2 7](media/15232508742194/where_am_I2%207.jpeg)

    
$\lambda$是尺度因子，表明$3D$点到相机光心的实际深度

##### 2. $P$是$3\times4$维，12个未知量，需要至少6对点来算$P$，1对点提供约束矩阵$3\times12$维，6个点提供约束矩阵$18\times12$维，可解出唯一的$P$

如果点更多，则用LS解，svd分解出$V$取最后一列就是$P$？？？？
    
![where_am_I2 13](media/15232508742194/where_am_I2%2013.jpeg)


##### 3. 由$K,P$解出$R,t$，
    
![where_am_I2 17](media/15232508742194/where_am_I2%2017.jpeg)

 - 求出的$R$要满足SO3约束，所以要用svd清理，将分解出的$D$对角线元素全部为1，再重建$R$
 - 除以$\sigma_1$是为了解决尺度不确定

##### 解法二：P3P

- 问题理解：
    1. $2D$bearing问题：由一幅图像，包含两个位置已知的灯塔，确定方位。
    
![pose_point_correspondences 4](media/15232508742194/pose_point_correspondences%204.jpeg)


两个点不能得到位姿！因为圆周角处处相等
    2. $3D$bearing问题：圆转化为一个曲面，相机在该曲面上的任意位置都能拍到夹角一样的灯塔图像，所以2点同样不能得到位姿！
    
![pose_point_correspondences 5](media/15232508742194/pose_point_correspondences%205.jpeg)
将以上问题推广到$3D$中
    3. Resection Problem：$3D$bearing中增加一点，则3点可以确定有限的可能位姿了，解法P3P
    
![pose_point_correspondences 7](media/15232508742194/pose_point_correspondences%207.jpeg)
![pose_point_correspondences 14](media/15232508742194/pose_point_correspondences%2014.jpeg)
![pose_point_correspondences 15](media/15232508742194/pose_point_correspondences%2015.jpeg)
![pose_point_correspondences 16](media/15232508742194/pose_point_correspondences%2016.jpeg)
![pose_point_correspondences 17](media/15232508742194/pose_point_correspondences%2017.jpeg)

    
- 尺度$\lambda$解法：
三对点组成两个相似三角形，用余弦定理联立三个方程

![pose_point_correspondences 11](media/15232508742194/pose_point_correspondences%2011.jpeg)

其中$d$是世界中的三角形的顶点和边，$u,v$是 三角形边的比例，$\delta$是图像中的三角形的边，

---

#### Case 5 ICP

**已知两个$3D$点云**
一个$3D$世界坐标点$B_i$，另一个是$3D$相机坐标点$A_i$

如图~~

$A,B$已知，$R$三个未知量，$T$三个未知量，尺度因子$s$一个未知量，这里忽略$s$只求解$R,T$
如图~~

- 例子：
    - 已知一个物体的模型（第一组$3D$世界坐标点$B_i$），用一个Kinect（RGBD）去拍它得到一个带深度$s$的图像（第二组$3D$相机坐标点$A_i$）
    如图4![pose_3D_points 4](media/15232508742194/pose_3D_points%204.jpeg)

    这种情况下$R,T,s$都可以确定出来
    
    - 已知一系列的深度图（不是普通相机图像），通过解决Procrustes可以建立一个$3D$点云模型
    如图5![pose_3D_points 5](media/15232508742194/pose_3D_points%205.jpeg)

    
    
- Procrustes Problem解法：ICP

- 至少要用三个点，分别在两个空间中通过两次叉乘建立正交坐标系

![pose_3D_points 6](media/15232508742194/pose_3D_points%206.jpeg)

![pose_3D_points 8](media/15232508742194/pose_3D_points%208.jpeg)

通过两个正交坐标系可以算出唯一的$R$
    

- 当有多对点时，用LS求解
    

![pose_3D_points 9](media/15232508742194/pose_3D_points%209.jpeg)

1. 对$T$求导，发现$T$只跟$A$和$B$的图心以及$R$有关
2. 把$A,B$的所有点变成以其图心为起点的一组同起点向量，带入LS，解出近似的$R$，注意最后要用svd清理$R$使其满足行列式为1的约束
        
![pose_3D_points 11](media/15232508742194/pose_3D_points%2011.jpeg)


![pose_3D_points 15](media/15232508742194/pose_3D_points%2015.jpeg)

        

---

#### Case 6 Pose from Projective Transformation

**已知两张图像，其中包含同一个平面**






