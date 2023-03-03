<center>Image Views</center>

#### VKImageView

在VKImage之后，而VKImage在swapchain之后

描述了如何获取iamge以及iamge的哪一部分可以被获取



#### 创建VKImageView

对每个swapchainimage都要创建与之相对应的iamgeview

一般创建一个结构体都要先创造相对应的createinfo结构体，然后需要viewtype，format

混合方法，components.rgba，默认就是swizzle

subsourcerange，欸这就是用来描述image的怎么用，如何获取了，对应的参数看vulkan tutorial



