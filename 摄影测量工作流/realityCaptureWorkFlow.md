## 1、基础

### 相机选择

摄影对最终结果影响的主要属性有：

+ 分辨率；resolution
  + 分辨率越高（其他属性不变），对其的点就越多
  + 产生的网格越好
  + 生成的纹理也更加尖锐
+ 空间保真率：spatial fidelity
  + 也叫有效分辨率
  + 分辨率如果是数量，那这就是质量，就是图像中我们可以利用的像素有多少
  + 影响因素：
    + 传感器：sensor（ISO noise）
    + 光学设备：主要的
      + distortion
      + corner sharpness
      + vignetting
      + chromatic aberration
  + 常识：传感器底越大，对光的敏感度越高
  + 一些变焦镜头会产生大量色差
+ 存储传感器数据：raw sensor data
  + 压缩会导致信号衰减
  + 最好用raw



### 哪些物体适合摄影测量

不适合：

+ 透明/反光（glass）；用尘埃覆盖（喷漫反射材料）
+ 高光glossy（car paint）
+ 动态的 dynamic（water）
+ aggregates，porous volumes（bushes，trees）

ps：pixellight

也是不适合的：

+ complex objects with lots of thin parts
+ featureless （minimalistic modern assets）



真正适合的：

+ 固体
+ 不透明，坚固
+ 第一无二的
+ 静态的
+ 丰富的纹理与细节



### 如何扫描

摄影测量目标物体的类型：

+ 小物件
  + 圆形环绕物体
  + 还可以翻转物体
  + 洋葱模式

+ facades and walls；外立面与墙壁
  + 保持与物体距离不变，沿着墙面移动
  + 照片应该重叠，重叠可以达到90%
  + 墙面如果比较复杂，可以倾斜相机
  + 景深，焦点离相机越近，景深就会越浅
  + 无限景深，可以了解
+ corridors 走廊
  + 超广角或者鱼眼
+ interiors；内部
  + 不是简单的绕轴旋转
  + 绕着内壁朝内拍照
+ surfaces；表面，或者叫平面
  + 可以用各种模式，主要是要保证全覆盖，足够重复
+ larger buildings；大型建筑
  + 洋葱模式
  + 围绕物体多画几个圆圈



### 预先计划

+ 开一拍摄一些样张，预览以下，将物体分为好几个部分
+ 天气最好是多云，没有阳光直射
+ 阴影会导致斑点状和波浪状的几何形状
+ 不要在晚上使用，因为光源不足，曝光不够，噪点巨多



### 相机设置

+ 拍摄raw
+ 模式手动
+ 设置白平衡; EV在0附近
+ 可以开启包围曝光
+ ISO - 100-200
+ f/stop - 8-11
  + 光圈越小，色散越大。越模糊
+ shutter speed - as fast as passible
  + 如果场景实在是太暗了，几乎没有光，那就需要用到闪光灯了

相机驱动模式：通常应保持为单次拍摄



### 镜头设置

定焦镜头通常比变焦镜头好一些，就产生伪影来说。

不同的变焦镜头，最清晰的焦距也不太相同。

摄影测量软件，需要所有源照片在同一个焦距下拍摄。

如何取景。



### 摄影测量中用到的设备

遮光罩，反光板，18%灰卡后期校色，卷尺最为参考

交叉偏振拍摄法。



### 色彩管理

JPEGS：aka the dispaly-referred disater；这些图像只会用来做参考不能用来作为实际的纹理。

只需对原照片进行两个操作：色温以及曝光

两种子工作流程：

+ PBR (面向游戏，三维渲染)

  + albedo
  + 基础色，用于漫反射
  + 这些类型的纹理不是用于直接观看的
  + PBR Maps
    + albedo
    + height
    + roughness
    + normals
  + **filmicworlds**.com/blog/how-to-split-specular-and-diffuse-in-real-images/
  + youtube, Grzegorz Baran

+ EMISSIVE WORKFLOW （面向效果）自发光

  + material emission (light)
  + 保证环境中原始照明质量
  + 一般只用HDR作为真实照片源
  + 没有移动整个场景的光影

  + 纹理贴图格式：

    + sRGB Curve（曲线）技术上不能存储太明亮的光
      + 8-bit .tiff
      + 16-bit .tiff
      + 8-bit .png
      + 8-bit .jpg

    + Linear（线性）
      + 32-bit .exr

### 所使用的软件

+ developing raw photos： Darktable
+ batch processing images：XnView
+ Reconstructing 3d model (photogrammetry)：RealityCapture， meshroom
+ Fixing the mesh，retopology：blender
+ sculpting， adding details：blender
+ baking texture maps：xNomal，RC，blender
+ de-lighting：Agisoft De-Light
+ Enhancing textures：Gimp, Cupscale；纹理贴图编辑 
+ PBR Materials：Quixel Mixer, Blender
+ Fianl clean-up：Blender



## 2、实践

### darktable

选择workflow：scene-reffered

取消 storage --》 write sidecar file for each image

关闭basecurve曲线

调整锐度（sharpen）；避免使用jpeg格式，可以8-bit tiff格式。



### 使用RC生成网格

+ 导入
+ alignment，settings
  + （image downscale factor）减少图片大小
  + advanced（preselector features）4w-5w适合摄像头
  + 对齐图片，生成稀疏点云
+ 调整重建区域，设置地平面（通常正交视角的top适合调整）
+ 将稀疏点云转换成稠密点云，并进行网格化
+ 对模型进行简化（例如使用 Lasso套索工具），在进行这些操作的时候需要清除重建区域
  + 按住ctrl增选，shift反选
  + 选择完之后，点击（filter selection）按钮，过滤选择
  + 导出成fbx



### 在blender中处理高模

全选，选择select boundary loop，选择face fill

进入雕刻模式（sculpt mode）

使用运动拓扑

为了防止意外，（物体模式）复制（shift + d）一份

有很多笔刷，视频里选择的是clay strips。

关闭动态拓扑，使用scrape笔刷继续修复





### 创建低模

添加精简修改器（decimate）  

像外围资产不是很重要，两三千个面就够了

还需要检查，在视口中启用面朝向

去除密集点（select --》select all by trait --》 loose geometry）



### 另一种精简模型的方法

decimate存在，如果某些面密度不同，那么精简的时候面密度低的会受到的影响更大。

在雕刻模式（sculpt mode）中有精简笔刷（simplify），和动态拓扑（dyntopo）配合使用，通常设定为恒定细节（constant detail）

总结：就是给密度低的面进行填充，之后再使用decimate精简。

UV：unwrap

UVeditor，editmode，a，u（smart uv project）



知识补充：什么是UV孤岛

贴图大多是正方形，并且是2的N次幂的像素比。

**UV孤岛**：对于复杂的模型，可能会被剪成好几块，这些分离的块就是UV孤岛。合理排布：让每一块UV都要拉开一定的距离



导出的时候，在RC中相关设定是，朝向-x，z是up



### 重投影纹理

**在RC中导入，高低模**

在 tools --》texture reprojection中。

其实这个结果已经相当好了，再需要的就是给重映射的时候确实纹理的地方补齐纹理（视频里是在blender中补的）。



**blender中的操作：**

导入：导出里生成一次texture，会相比于colorizing有更多的细节。

检查：在shader editor中检查纹理是否被正确加载

重绘：重新绘制没有纹理的区域：

+ 利用已有的部分进行部分采样，复制，填充
+ 或者使用重映射+gimp