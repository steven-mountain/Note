<center><font size="10px">Presentation</font></center>

---

一些个人理解，区域外的都是抄的，没啥意义，看个乐就好。

### 1、什么是framebuffer呢？

framebuffer是一个**内存缓冲区**，用于**存储图像的像素数据**。它通常包含了颜色缓冲区、深度缓冲区、模板缓冲区等。framebuffer是渲染管线的最终输出目标，它的内容将被传输到显示器上进行显示。

shader也可以访问这些渲染目标，在片元着色器中进行深度测试的时候，就需要读取depthimageattachment中的内容。



### 2、为什么attachment与imagView的绑定是通过framebuffer而不是descriptor的呢？

好问题！我之前好像理解过，但又好像没有理解，如理解。这里给出我的看法（通过newbing理解的）。

+ framebuffer是渲染目标的集合，包含了所需要渲染到的附件，每个附件都有一个对应的imageView，用于描述附件的格式和访问方式。我们使用frameBuffer指定渲染目标，并将其绑定到渲染管线上，这样我们就能将渲染结果直接写入到frameBuffer中指定的附件。**渲染目标**是渲染管线的**最终输出目标**，它的**内容将被传输到显示器**上进行显示。

+ descriptor set用于描述**shader中使用的**uniform buffer、storage buffer、sampler等**资源**。它们通常用于在shader中进行**计算和采样**操作。因此，descriptor set与framebuffer不同，它们**描述了shader中使用的资源，而不是渲染目标**。因此，它们不适合用于描述framebuffer中的附件。

+ 为什么不对attachment使用sampler呢？

  这个问题说简单也简单，因为vulkan里就有attachmentDescription呀，都有description了，还需要啥sampler呀。

  往深层次地说，sampler是用于**shader对外部资源的访问**，而attachment是**绑定在renderpass上的，用于配置渲染管道的**。

  Sampler在Vulkan中**主要用于纹理采样**，**通常是只读的**。Sampler定义了如何从纹理资源中获取颜色样本，但它**不提供对资源的写入或修改**功能。



![](./images/attachmentinpipeline.png)



### 3、description与descriptor的区别是什么？

又是一个好问题，怎么有点自卖自夸的感觉。我直接复制chatgpt的回答吧。

在Vulkan中，"description" 和 "descriptor" 是两个不同的概念，它们服务于不同的目的，没有直接的替代关系。下面解释它们的区别以及何时应该使用哪个：

1. **描述符（Descriptor）**:
   
   - Descriptor是一种用于在**着色器**程序中访问**外部资源**的机制。它允许着色器程序访问缓冲区、纹理、采样器和其他资源，以便在渲染或计算中使用这些资源。描述符是用于在着色器程序中声明和绑定资源的一种方式。
   
   示例：使用 Descriptor 来在着色器程序中访问一个纹理，以便将其用作材质。
   
   为什么要使用 Descriptor 而不是 Description ？因为Descriptor是Vulkan中用于在着色器中访问资源的标准方式。描述符提供了访问资源的灵活性，允许在渲染或计算中**动态绑定资源**，而且它们与着色器程序之间的接口清晰明了。
   
2. **描述（Description）**:
   
   - "描述" 并不是 Vulkan 中的一个通用术语。它通常用于描述 Vulkan 中的一些**对象的配置和属性**，如附件描述（Attachment Descriptions）用于描述渲染通道中的附件属性，顶点描述（Vertex Descriptions）用于描述顶点数据布局等。
   
   示例：使用附件描述来配置渲染通道中的颜色和深度附件的格式和处理方式。
   
   这些 "description" 主要用于**配置渲染通道、数据布局和其他对象的属性**。它们不是用于在着色器程序中访问资源的机制。

总之，描述符用于在着色器程序中访问资源，而 "描述" 通常用于配置渲染通道、数据布局等。它们是不同的概念，用途不同，不能互相替代。描述符是与着色器交互的方式，而 "描述" 用于配置渲染通道、数据格式等，以影响渲染过程或数据布局。



### 4、顶点数据应该也算是外部资源，那为什么不使用descriptor呢？

在Vulkan中，使用描述符（Descriptors）来访问顶点数据通常不是最合适的方式，因为描述符主要用于在着色器程序中访问外部资源（如纹理、缓冲区、采样器等），而不是用于表示顶点数据。以下是一些理由为什么不直接使用描述符来表示顶点数据：

1. **不必要的复杂性**：描述符是用于在着色器中访问资源的机制，通常用于访问多个资源，而顶点数据通常是静态的、格式已知的数据。直接使用描述符来表示顶点数据会引入不必要的复杂性，并且可能会导致性能开销。

2. **性能问题**：描述符通常用于在着色器程序中动态访问资源，这可能会引入访问延迟和性能开销。对于顶点数据，通常在渲染过程开始前就已经准备好，并且其格式是已知的，因此直接绑定到着色器输入描述中通常更高效。

3. **不一致性**：使用描述符来表示顶点数据可能会导致在渲染流程中引入不一致性，因为描述符绑定是动态的，而顶点数据通常**在渲染流程中不会改变**。

相反，Vulkan提供了更适合的机制来传递顶点数据，通常是通过顶点缓冲（Vertex Buffer）对象。顶点缓冲是一种专门用于存储和传递顶点数据的缓冲对象，**通常是静态的**，并且可以**在渲染过程中高效地访问**。这样**可以确保高性能和一致性**，而不引入描述符的复杂性和性能开销。

总之，使用描述符来表示顶点数据通常是不必要且不推荐的，因为Vulkan提供了更适合的机制，即顶点缓冲，来处理顶点数据。描述符更适合用于在着色器中访问动态或可变的资源，而不是用于传递静态的顶点数据。



### 5、而外部资源如纹理等，在渲染过程中不会被修改，又为什么不用description

使用描述符而不是描述来访问纹理等外部资源的原因在于描述符提供了**更大的灵活性和通用性**，允许在着色器中**动态绑定和访问这些资源**。这种灵活性对于许多渲染场景是非常有用的，即使在渲染过程中这些资源不会改变。以下是一些理由为什么在渲染过程中使用描述符而不是描述：

1. **动态资源绑定**：描述符允许在渲染过程中**动态绑定和切换不同的资源**。这对于不同渲染通道或不同渲染帧需要不同纹理或缓冲区的情况非常有用。使用描述符，你可以在每个渲染帧中选择不同的资源，而不需要重新配置描述。

2. **通用性**：描述符**不仅限于纹理，还可以用于访问其他资源**，如Uniform Buffer、Storage Buffer等。这使得描述符成为一种通用的资源访问机制，可以适用于多种不同类型的资源。

3. **计算着色器**：描述符也在通用计算着色器中广泛使用。计算着色器通常需要访问大量的数据，描述符提供了一种在计算着色器中访问外部数据的标准方式。

4. **更好的抽象**：使用**描述符提供了一种更好的抽象**，将资源的访问和着色器程序解耦。这使得代码更易维护和扩展。

虽然在某些特定情况下，描述可能足够用于纹理等资源，但使用描述符通常是更灵活和通用的选择，特别是在大型和复杂的渲染管线中。描述允许更大程度的动态性和配置灵活性，而且是Vulkan中的标准做法，因此通常被视为更好的选择。



总结一下就是：

+ description配置的都是与渲染管线相绑定的，应该属于是被渲染管线囊括其中了。

  例如顶点数据和附件(render target)，**顶点数据**作为渲染管线的输入，在vertex shader中处理。而**附件**（render target）则保存的是渲染管线处理的**结果**的。

+ descriptor则是配置那些在渲染管线外部的，也就是在渲染管线处理的过程中需要的一些**外部**数据。



### framebuffer与renderpass

```c++
	void createFramebuffer() {
		swapchainFramebuffer.resize(swapChainImageView.size());
		for (int i = 0; i < swapchainFramebuffer.size(); ++i) {
			std::array<VkImageView, 2> attachments = {
				swapChainImageView[i],
				depthImageView
			};

			VkFramebufferCreateInfo frambufferCreateInfo{};
			frambufferCreateInfo.sType = VK_STRUCTURE_TYPE_FRAMEBUFFER_CREATE_INFO;
			frambufferCreateInfo.attachmentCount = static_cast<uint32_t>(attachments.size());
			frambufferCreateInfo.pAttachments = attachments.data();
			frambufferCreateInfo.renderPass = renderPass;
			frambufferCreateInfo.width = swapChainExtent.width;
			frambufferCreateInfo.height = swapChainExtent.height;
			frambufferCreateInfo.layers = 1;
			if (vkCreateFramebuffer(device, &frambufferCreateInfo, nullptr, &swapchainFramebuffer[i]) != VK_SUCCESS) {
				throw std::runtime_error("failed to create framebuffer");
			}
		}
	}

VkRenderPassBeginInfo renderPassBeginInfo{};
renderPassBeginInfo.sType = VK_STRUCTURE_TYPE_RENDER_PASS_BEGIN_INFO;
renderPassBeginInfo.clearValueCount = static_cast<uint32_t>(clearValues.size());
renderPassBeginInfo.pClearValues = clearValues.data();
// 这里应该是找到当前可用的swapchain image对应的framebuffer
renderPassBeginInfo.framebuffer = swapchainFramebuffer[imageIndex]; 
renderPassBeginInfo.renderArea.offset = {0, 0};
renderPassBeginInfo.renderArea.extent = swapChainExtent;
renderPassBeginInfo.renderPass = renderPass;
```





---



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

