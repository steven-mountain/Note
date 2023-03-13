<center>Rendering and presentation</center>

#### outline of a frame

+ 等待前一帧完成
+ 从swapchain中获取一张image
+ 记录 command buffer，这个buffer将scene draw to 获取到的image中
+ 提交command buffer
+ present swapchain image



#### 同步

semaphore工作在：

+ 同一个queue的内部
+ 不同queue之间

vulkan中恰好有两个不同的信号量，binary和timeline，通常指的是binary

binary有两个状态，signaled和unsignaled，初始是unsignaled



fences

和semaphore类似，但是适用于同步CPU上的工作。一般不用，因为如果host被blocked那么，它就停在那儿，啥也不干，并且fences还必须手动归零，用于CPU和GPU之间的同步



#### subpass 依赖

renderpass的==开始==和==结尾==有两个内建的passdependencies，但是呢==前者==假定transitions在pipeline开始的时候就开始了，这是不对的。

```c++
typedef struct VkSubpassDependency {
    uint32_t                srcSubpass; // who start my source subpasses who i am dependent on
    uint32_t                dstSubpass; // who i am 
    VkPipelineStageFlags    srcStageMask; //tell you what stage everything is dependent on 
    // Bitmask specifying pipeline stages
    VkPipelineStageFlags    dstStageMask;
    VkAccessFlags           srcAccessMask; // where you want things on in this pass  memory access types
    VkAccessFlags           dstAccessMask;
    VkDependencyFlags       dependencyFlags;
} VkSubpassDependency;
```



#### 然后便是presentation

将相关的配置写入对应的createinfo就可以了

