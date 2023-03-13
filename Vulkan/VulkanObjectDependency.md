<center>Vulkan 各对象创建所需</center>

#### Instance

+ VkApplicationInfo
+ VkInstanceCreateInfo



#### physical device

不需要创建，直接从列举的里面pick，但是要找到合适的物理设备

需要检查符合要求的 VkPhysicalDeviceProperties 以及 VkPhysicalDeviceFeatures 还有 QueueFamily， 还要检查是否支持extension

QueueFamily如何查找呢

+ 先列举出来，然后找到符合要求的



#### logical device

+ 为每一个QueueFamily创建一个VkDeviceQueueCreateInfo

+ 启用GPU的feature在 VkPhysicalDeviceFeatures
+ VkDeviceCreateInfo
+ 还需要在createinfo中指定extension(在 physical device中检查是否支持)



#### Window Surface

这是里面最简单的了，哭了啥也不需要，这是个好人



#### swapchain

+ 检查物理设备是否支持swapchain
+ 接下来是素质三联
  + format
  + presentmode
  + extent
+ 设置iamge count
+ 又需要queuefamily，杀千刀的，用来判断时候所需要的queuefamily是否在一张显卡上
+ 还需要保存三兄贵
  + swapchainImages
  + swapchainimageformat
  + swapchainextent



#### imageview

根据上面保存的swapchainiamges，为每一个swapchainimage创建一个iamgeview



#### pipeline

pipeline的创建需要renderpass，然后分为两个大的阶段，可编程管线和固定管线。



##### 可编程管线

主要是将顶点着色器，片元着色器等获取shader代码，然后编译成spv，并将这些封装在shader module中。



##### 固定管线

固定管线需要显示的指定各个阶段：

+ dynamic state
+ vertexinput
+ inputassamble
+ viewport， scissor
+ rasterizer
+ multisampling
+ depth and stencil testing
+ colorblend
+ pipeline layout



##### render pass

创建renderpass需要

+ 一堆附件
+ 这些附件的引用
+ 一堆 subpass
+ 以及它们之间的关联关系

 创建完以上的这些东西后，就可以创建pipeline了



#### Framebuffer

需要swapchainimage，并为每一个swapchainimage创建一个framebuffer



#### command pool

需要queuefamily，因为我们的command最后是提交到device queue里运行的

每个command pool只能分配为 某一类型的 queue上面



#### command buffer

从command pool中分配command buffer



##### command buffer recording

+ command buffer begin
+ render pass
+ bind pipeline
+ viewport and scissors set
+ draw
+ end render pass
+ end command buffer



#### rendering and presentation

+ 需要semaphore 后者fences来同步
+ 等前一frame
+ 获取swapchain imageview
+ recorder command buffer
+ 提交graphics queue
+ 在 render pass 中配置好 subpass dependencies
+ 提交 present queue(这个拓展的，不在vulkan core内)
+ 然后就可以看到我们的三角形了

