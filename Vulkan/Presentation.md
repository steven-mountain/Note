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
+ Basically three kinds of properities
  + basic surface capabilities (min/max number of images in swap chain, min/max width and height of images)
  + surface formats (pixel format, color space)
  + available presentation modes
+ 

