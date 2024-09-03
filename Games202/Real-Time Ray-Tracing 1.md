## Real-Time Ray-Tracing 1

light transport

complex material（散射介质）



### Basic idea

”ray tracing is the future and ever will be“

+ 实时光追做了些啥
  + 做出来的效果很好，场景很闪。用在反射上是最简单的。
  + 一种硬件架构，能够去trace 光线。
  + 做光线与场景的求交。
  + 1 SPP Path tracing = 
    + 1 rasterization （primary）(看到最前面的物体，为何不直接光栅化一遍)+
    + 1 ray （primary visibility, shadow ray）+
    + 1 ray（secondary bounce）+
    + 1 ray（secondary vis. shadow ray）
  + path tracing 本身是一种蒙特卡洛的方法
  + RTRT 最关键的内容就是**降噪**
+ 目标
  + 质量，不overbulr 没有瑕疵，保留细节
  + 速度 <2ms
  + 不可能，降噪很多，但是对于实时光追的很少
  + 最重要的tempora
+ tempora
  + $\overline{C}^{(i)}=\alpha\overline{C}^{(i)}+(1-\alpha)C^{(i-1)}$
  + 假设前一帧已降噪，并且复用它。
  + 假设场景是连续的，帧与帧之间有大量的连续性。
  + **当前帧需要滤波，前一帧已经滤波。**
  + 使用 **motion vector**来找到前一帧的位置。（找的是**像素与像素**之间的对应关系，像素的内容）
  + 空间上如何降噪？下一节课说。
+ Geometry buffer，用光栅化代替一遍光追得到的。
  + 在渲染的时候获取这些东西，方便使用。
  + 是**screen space** info
  + back projection
    + 找到当前看到的点的世界坐标，在Gbuffer中直接获取即刻。
    + 否则$s = M^{-1}V^{-1}P^{-1}E^{-1}x$，仍然需要z值。
    + 找到上一帧的世界坐标，找到上一帧的屏幕坐标。
    + 大头主要是上一帧。
    + optical flow（光流，在时域上找像素之间的对应关系）
  + 时域复用失败
    + **切换场景（镜头），场景第一帧，光源突变。**
      +  burn-in period：需要一段时间预热
    + **倒退着走，（边缘会出现新的物体，找不到对应）**
    + disocclusion，从遮挡状态变为非遮挡状态。
    + 会有残影或者拖尾。
    + shading：
      + 相机、场景不变，背景光源发生变化。detach。 
      + 反射，也就是变化的物体在不变的物体上的反射会有滞后现象。
  + 解决办法：选择性复用上一帧信息
    + clamping
      + 将上一帧拉近到足够接近当前帧，然后做线性blending
    + detection
      + 使用object ID来判断temporal failure，上一帧的物体是否与当前帧的物体相同。
      + 调整$\alpha$。
      +  possibly strengthen / enlarge spatial filtering
      + 因为没有用上一帧的信息，会引入新的噪声，原本出现拖影的地方都变成了噪声。
    + temporal reliable motion vectors





## Real-Time Ray-Tracing 2

### 如何实现 空域 滤波（spatial filter）

低通滤波，保留低频信息，去掉高频噪声。

+ 高斯滤波核：**正态分布**。 
  + 做归一化，求权。
  + 结果很糊。



+ 双边滤波
  + 也是高斯函数
  + 保留边界（**颜色变化剧烈的点**）
  + 两像素颜色差异是否过大。
  + 在高斯滤波核上加一点东西（像素差异过大，减小贡献（σ取值范围））。



+ joint bilateral filtering（联合双边滤波）：有点像多模态
  + 高斯滤波：距离
  + 双边滤波：颜色，距离
  + 使用更多的标准（Gbuffer：depth、normal、color）来 guide filtering
  + 特别适合于路径追踪
  + Gbuffer是没有噪声的。
  + 不需要考虑正则化。



大滤波器的问题：

+ 滤波核过大，filtering 会很慢



解法：

+ separate passes
  + 1xN, Nx1；
  + 在水平和竖直上做filtering
  + 因为二维高斯函数是拆开定义的。
  + $G_{2D}(x,y)=G_{1D}(x).G_{1D}(y) $
  + filtering = convolution
  + 但是双边滤波（两高斯相乘）的无法拆分。



+ progressively growing sizes
  + 使用逐步增大的filter
  + a-trous wavelet
  + 多趟滤波，每一趟都是 5X5filter
  + 每一趟样本之间**逐步加间隔**。

 

+ outlier removal（temporal clampling）
  + 画面中很可能出现很多超级亮的点
  + 滤波不太能处理这些点。
  + 很亮的点（outlier）filtering之后会扩散到周围，blocky artifact。
  + idea：在滤波之前处理掉这些outlier
  + 如何检测：
    + 每个像素看周围区域，7X7或者5X5，计算范围内的均值于方差。
    + 据大多数像素在均值+-方差的范围内。[u-kσ, u+kσ]，超过范围就是outlier
  + 如何去除：
    + 将这个值clamp到上诉范围内。
  + 这样能量不守恒。



### 在RTRT中特殊的filtering 实现方式







