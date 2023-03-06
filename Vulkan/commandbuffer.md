<center>Drawing</center>

 #### Framebuffers

在vulkan中一个framebuffer对象引用了所有的表示附件的==vkimageview==对象

在附件中使用的图像取决于swapchain返回给我们的图像

framebuffer实际上是将实际的附件和渲染过程关联起来

为每个==swapchainimageview==创建一个framebuffer即刻



#### Command pool

在vulkan中像绘图，内存转换等操作，并非直接通过函数调用来实现。

得要把所有需要做的指令放在 ==commandbuffer== 中，好处是将所有指令放一块儿可以提升运行速度，同时还可以支持多线程。挺爽的。

command pool用于管理memory，并且command buffer也从中分配。

commandbuffer的执行，是将它们提交给某一个==device queue==中进行的。

每一个command pool 只能分配被提交在single type的queue的commandbuffer



#### Command buffer

command buffer会在与之对应的command pool被销毁的时候销毁。

使用函数 ==vkallocatecommandbuffer==来创建

recoording commandbuffer以 ==vkbegincommandbuffer==开始

drawing 以开始render pass用 ==vkcmdbeginrenderpass==

再绑定我们的graphics pipeline，这里需要指定一下viewport还有scissor