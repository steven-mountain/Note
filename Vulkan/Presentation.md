<center><font size="10px">Presentation</font></center>

### Window Surface



### Swap chain

+ Swap chain is an infrastructure which will own the **buffers** that you will render to before you visualize.
+ Swap chain is a queue of images which will be presented to the screen.
+ General purpose of swap chain is to synchronize the presentation of images with refresh rate of the screen



#### Checking for swap chain support (physical device)

+ Image presentation is heavily tied into the window system and the surface associated with windows, it is not part of the Vulkan core.
+ Enable `VK_KHR_swapchain` device extension
+ `VK_KHR_SWAPCHAIN_EXTENSION_NAME`



#### Enabling device extensions (logical device)

```c++
createInfo.enabledExtensionCount = static_cast<uint32_t>(deviceExtensions.size());
createInfo.ppEnabledExtensionNames = deviceExtensions.data();
```



#### More thing

+ To ensure the swap chain is compatible with you window surface

+ Basically three kinds of properties
  + basic surface capabilities (min/max number of images in swap chain, min/max width and height of images)
  + surface formats (pixel format, color space)
  + available presentation modes



#### choose right setting of swap chain

- Surface format (color depth)
- Presentation mode (conditions for "swapping" images to the screen)
- Swap extent (resolution of images in swap chain)



### How to use swapchain

#### Checking for swap chain support（physical）

+ image presentation 不是vulkan核心里的
+ 作为拓展，我们使用 `VK_KHR_SWAPCHAIN_EXTENSION_NAME` 宏
+ swap chain的核心组件是：VkPhysicalDevice， VkSurfaceKHR



#### Enabling device extensions（logical）

```c++
createInfo.enabledExtensionCount = static_cast<uint32_t>(deviceExtensions.size());
createInfo.ppEnabledExtensionNames = deviceExtensions.data();


```

#### Querying details of swap chain support（查询属性）

- Basic surface capabilities (min/max number of images in swap chain, min/max width and height of images)
- Surface formats (pixel format, color space)
- Available presentation modes

对应上面的三个属性

```c++
struct SwapChainSupportDetails {
    VkSurfaceCapabilitiesKHR capabilities;
    std::vector<VkSurfaceFormatKHR> formats; // 是一系列结构体集合，管用的方式是 2Function calls
    std::vector<VkPresentModeKHR> presentModes; // 一系列 mode的集合，也是 2Function calls
};
```



#### Choosing the right settings for the swap chain（从属性中选择合适的）

+ surface format：format、colorspace

+ presentation mode

  + 一般有四种模式
  + 即出
  + 先进先出：最稳定，因为是被保证支持的
  + 先进先出relaxed，que为空就即出
  + mailbox：que满的时候直接用最新的替代

+ swap extent：在capabilities 里

  + GLFW两种单位：pixels 和 screen coordinate
  + vulkan用的是 pixels
  + 但是创建窗口用的是 screen coordinate， 难搞

  ```c++
  // 用到的头文件
  #include <cstdint> // Necessary for uint32_t
  #include <limits> // Necessary for std::numeric_limits
  #include <algorithm> // Necessary for std::clamp
  ```

  + window manager 允许将currentExtent设置为不同的值，当 width 为 uint32_t的最大值时
  + 当width等于最大值时，就需要自己去设置，将结果钳在最大和最小分辨率之间



#### Creating the swap chain

除了以上要做的，还有一件事儿，那就是 ==图像的数量== 

+ capabilities里面有支持的图像数量的最大最小值
+ 最大值如果为 ==0== 说明压根就妹有最大值
+ 最大值默认是==大于等于==最小值
+ 图像数量不能==超过==最大值



sharing mode：

+ 如果支持的queuefamily在一个queuefamily上，那就exclusive
+ 否则，还能咋滴，只能分享呗，concurrent

还要指定transformation，如果不想要任何变换，就直接currentTransformation

透不透明也要指定，一般都是不透明，opaque

看不到的cliped，一般哈，如果要获取之后的颜色做预测可就不能clipped





#### Retrieving the swap chain images

images随着swapchain的创建而生成，随着它的销毁而销毁，将images保存下来，当然presentmode和extent也要保存下来
