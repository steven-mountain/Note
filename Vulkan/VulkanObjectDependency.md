<center>Vulkan 各对象创建所需</center>

#### Instance

+ VkApplicationInfo
+ VkInstanceCreateInfo



#### physical device

不需要创建，直接从列举的里面pick，但是要找到合适的物理设备

需要检查符合要求的 VkPhysicalDeviceProperties 以及 VkPhysicalDeviceFeatures 还有 QueueFamily， 还要检查是否支持extension

QueueFamily如何查找呢

+ 先列举出来，然后找到符号要求的



#### logical device

+ 为每一个QueueFamily创建一个VkDeviceQueueCreateInfo

+ 启用GPU的feature在 VkPhysicalDeviceFeatures
+ VkDeviceCreateInfo
+ 还需要在createinfo中指定extension



#### Window Surface

这是里面最简单的了，哭了啥也不需要，这是个好人



#### swapchain

+ 检查物理设备是否支持swapchain
+ 接下来是素质三联
  + format
  + presentmode
  + extent
+ 设置iamge count
+ 有需要queuefamily，杀千刀的
+ 还需要保存三兄贵
  + swapchainImages
  + swapchainimageformat
  + swapchainextent



#### imageview

根据上面保存的swapchainiamges，为每一个swapchainimage创建一个iamgeview

