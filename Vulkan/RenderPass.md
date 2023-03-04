<center>Render passses</center>

为什么我们需要renderpasses呢，这里我就引用一下知乎上justin的文章。下面很多都是他的原话

地址：https://zhuanlan.zhihu.com/p/404118061

当一个或多个像素变得昂贵的时候，例如对于多光源

+ 可以用一个着色器来处理多光源，这么做就会很复杂，而要是不同类型的光，那就真的是寄了，难度直接上天
+ 为每个光源创建一个单独的着色器，对每个光源渲染一次，分别进行计算，再将每个结果添加到framebuffer中。这样做更系统，更简单。



对于renderpass的理解参考了math philosohpy的文章

地址：https://zhuanlan.zhihu.com/p/418277173

renderPass里存了些啥：

1，一堆附件（两种类型，颜色，深度）

2， 一堆附件引用（和上面的附件基本是1对1）

3，若干SubPass ,每个SubPass对应一堆附件引用

4，若干SubPassDependency, 每个子通道依赖对应一个源SubPass,一个目标SubPass

5, 最后1个RenderPass对应上面所有。



附件引用的作用仅仅只是表达一个附件可以同时被两个subpass引用



pipeline相当于是铺设的管道，这个管道里可以是石油也可以是天然气（attachment），而renderpass就相当于是组织管道内流通的物质的



#### set up

让vulkan在渲染的时候知道 哪些framebuffer attachments会被使用

需要指定有多少 ==颜色==和==深度==缓存等信息，这些信息都封装在==render pass==

对象中

附件是我们在绘图命令期间渲染的图像，换句话说它们是渲染目标。可用于片段着色器内部的像素局部加载操作，在后续子通道中的同一像素处读取在一个子通道中写入的帧缓冲附件。



#### Attachment description

`loadOp`和`storeOp`决定attachment里的数据在渲染前后如何处理,应用于颜色和深度

还有个`stencilLoadOp`和`stencilStoreOp`，则应用于stencil 数据



Textures 和 framebuffers 被 带有具体像素格式的`VkImage`所表示。



#### Subpasses and attachment references

一个渲染过程可以由多个子过程组成。

每个子过程可以引用多个附件

reference里的attachment指的是 该引用对应的attachment在attachment description里的索引。



#### Render pass

