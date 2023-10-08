Depth buffering

为什么需要？因为3D mesh就需要多一个轴。

如何实现纵深呢？有两种方式：

+ 将所有的drawcall按照从后往前分类

  这种方式通常用于绘制透明物体，因为 order-independent transparency很难

+ 使用depth buffer进行深度测试

  这是最常用的方法



### 深度测试与模板测试的概念

深度缓冲是在片段着色器运行之后（以及模板测试(Stencil Testing)运行之后）在屏幕空间中运行的。

```wiki
在learn-OpenGL中的一段话
现在大部分的GPU都提供一个叫做提前深度测试(Early Depth Testing)的硬件特性。提前深度测试允许深度测试在片段着色器之前运行。只要我们清楚一个片段永远不会是可见的（它在其他物体之后），我们就能提前丢弃这个片段。

片段着色器通常开销都是很大的，所以我们应该尽可能避免运行它们。当使用提前深度测试时，片段着色器的一个限制是你不能写入片段的深度值。如果一个片段着色器对它的深度值进行了写入，提前深度测试是不可能的。OpenGL不能提前知道深度值。
```

在每个渲染迭代之前，都应该将上一次的深度缓冲重置。将view视角下的z值标准化到0-1（01标准化），但是实践中是不存在这样线性的深度缓冲，有这样的要求（与1/z 成正比）:

+ Z值小的时候提供高精度

+ Z值大的时候提供低精度

$$
F_{depth}=\frac{1/z-1/near}{1/far-1/near}
$$



深度冲突：

一个很常见的视觉错误会在两个平面或者三角形非常紧密地平行排列在一起时会发生，深度缓冲没有足够的精度来决定两个形状哪个在前面。结果就是这两个形状不断地在切换前后顺序，这会导致很奇怪的花纹。这个现象叫做深度冲突(Z-fighting)，因为它看起来像是这两个形状在争夺(Fight)谁该处于顶端。

防止深度冲突的办法：

+ 永远不要把多个物体摆得太近，以至于它们的一些三角形会重叠
+ 尽可能将平面设置远一些
+ 使用更高精度的深度缓冲



模板测试：

片段着色器 --》深度测试 --》 模板测试

- 启用模板缓冲的写入。
- 渲染物体，更新模板缓冲的内容。
- 禁用模板缓冲的写入。
- 渲染（其它）物体，这次根据模板缓冲的内容丢弃特定的片段。

模板缓冲中的模板为通常是8位，通过使用模板缓冲，我们可以根据场景中已绘制的其它物体的片段，来决定是否丢弃特定的片段。

使用模板测试的好的例子就是**物体轮廓**，为物体创建轮廓的步骤如下：

1. 在绘制（需要添加轮廓的）物体之前，将模板函数设置为GL_ALWAYS，每当物体的片段被渲染时，将模板缓冲更新为1。
2. 渲染物体。
3. 禁用模板写入以及深度测试。
4. 将每个物体缩放一点点。
5. 使用一个不同的片段着色器，输出一个单独的（边框）颜色。
6. 再次绘制物体，但只在它们片段的模板值不等于1时才绘制。
7. 再次启用模板写入和深度测试。



### SSBO与UBO

SSBO

关于SSBO的理解，CSDN上的What_can_you_do老哥的理解不错，这里记录一下。链接如下：

https://blog.csdn.net/What_can_you_do/article/details/125620229

UBO：Uniform Buffer Object; SSBO: Shader Storage Buffer Object

+ UBO：与普通uniform变量相比，像是一个可以容纳多个变量的结构体。存储在显存的常量区，速度快，编译时大小是确定的，而且大小是有限制的，在着色器里**可读但不可写**，修饰符uniform，一般用于少量的变量设置，在所有着色器都常用到。
+ SSBO：在着色器中**可读可写**，修饰符buffer，存储在全局变量区，速度比UBO慢些，但是大小在编译时不确定，大小基本没有限制，一般用于两个着色器之间处理后数据的传递，多见于计算着色器。



### 什么是depth buffer

depth buffer是一个 additional attachment存储的是每一个位置的深度信息。 Every time the rasterizer produces a fragment, the depth test will check if the **new** fragment is **closer** than the previous one. 每次存储离屏幕最近的fragment。在vulkan中，depth的范围是`0.0`\~`1.0`，而在OpenGL中`-1.0`\~`1.0`，需要修改投影矩阵的范围，`#define GLM_FORCE_DEPTH_ZERO_TO_ONE`

vulkan和OpenGL的`z`轴都是指向屏幕内，因此离屏幕**越近**z值**越小**



### Depth image and view

depth attachment 是基于image的，就像color attachment一样。不同点在于swapchain不会自动为我们创建depth image。我们只需要一张depth image（保存当前draw的深度信息）就好了，因为一次只调用一次draw operation。

为depth image选取正确的格式很关键。至少需要24位的精度。有一下几种格式。

- `VK_FORMAT_D32_SFLOAT`: 32-bit float for depth
- `VK_FORMAT_D32_SFLOAT_S8_UINT`: 32-bit signed float for depth and 8 bit stencil component
- `VK_FORMAT_D24_UNORM_S8_UINT`: 24-bit float for depth and 8 bit stencil component

但是呢最好是设置一系列的候选类型，然后通过`vkGetPhysicalDeviceFormatProperties`函数找到最适合的。

VkFormatFeatureFlags 与 VkImageLayout的区别：前者描述了**图像资源支持的特性**，而后者描述了**图像资源在内存中的布局和访问方式**。
