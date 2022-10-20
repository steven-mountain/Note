### Instance

+ vulkan命名方式

  + `vk` 前缀：一般是方法
  + `Vk` 前缀：枚举和结构体
  + `VK_` 前缀：枚举值

+ 对象的创建：

  + 直接创建：`vkCreateXXX`
  + 通过其他对象创建：`vkAllocateXXX`

+ 对象销毁：`vkDestroyXXX`、`vkFreeXXX`

+ 在vulkan中其他资源都要在 instance 之前释放

+ 很多信息在vulkan中都是通过structs 传输的而不是参数

+ 创建instance，首先需要将一些关于application的信息传输给驱动，来优化应用。这是可选的

  在`VkApplicationInfo` 中通常需要填写一下信息：`sType`、`pApplicationName`、`applicationVersion`、`pEngineName`、`engineVersion`、`apiVersion`

+ `pNext` 一般指向的是未来的拓展的信息
+ `VkInstanceCreateInfo` 是必须的，用以告诉vulkan驱动，所需使用的全局拓展和验证层。包含一下信息：`sType`、`pApplicationInfo`、`enabledExtensionCount`、`ppEnableExtensionNames`、`enabledLayerCount`
+ vulkan中创捷对象函数，通常包含一下信息
  + Pointer to struct with creation info
  + Pointer to custom allocator callbacks, always nullptr in this tutorial
  + Pointer to the variable that stores the handle to the new object
