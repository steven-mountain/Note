<center>Vertex buffers</center>

#### vertex shader

+ 顶点着色器通过 `in` 关键字来从顶点缓存中获取数据
+ 用`in`修饰的变量是 指定每一个顶点的属性
+ 有的类型 如 `dvec3` 需要使用多个 slots，意味着后面的location至少要比它高2



#### vertex data

使用glm，将所需要用到的顶点数据封装



#### Binding description

目的是告诉vulkan如果将这一数据格式传输给顶点着色器



#### Attribute descriptions

+ 如何处理input vertex

+ array 容器的大小是固定的，无法动态的扩展或收缩
+ attribute description 描述的是如何从binding description中提取顶点属性



#### pipeline vertex input

设置图形管道来接收数据，前面两个description绑定在pipeline中



====description主要是给pipeline用的，告诉pipeline如何解析这些数据 这两者其实是分开的

而后面的vertexbuffer主要是告诉commandbuffer如何获取数据，以及数据存储的位置

vertex的用法就好像是，

+ 你需要一块硬盘，vertex buffer 
+ 然后别人根据你的需求选取了一块合适的给你， VkMemoryRequirements
+ 然后你再检查一遍这块盘是否能满足你的所有需求， VkPhysicalDeviceMemoryProperties
+ 将硬盘拿到手，这个硬盘的使用权就归你了 bind
+ 向硬盘里写数据 memcpy

#### vertex buffer

+ 在vulkan中buffer是一块内存区域用于存储供显卡随机读取的数据

+ 和vulkan对象不同，buffer不会自动分配内存

+ 需要vkbuffercreateinfo结构体

创建buffer对象之后，还需要查询其内存需求，然后为其分配内存

+ VkMemoryRequirements 
+ VkPhysicalDeviceMemoryProperties ->memorytype
+ 内存分配
+ bindbuffermemory
+ 传输数据
+ 当buffer不可用的时候，我们的memory也就可以清除了

向vertex buffer中填充数据（通过映射内存）

+ vkmapmemory
+ memcpy
+ vkunmapmemory

在recordcommandbuffer中bindvertexbuffer



#### Staging buffer

为什么要用stagingbuffer，因为目前的方法也就是前面设置的memory type CPU也可以访问，这就不太好了，不太专一，会影响性能。

需要`VK_MEMORY_PROPERTY_DEVICE_LOCAL_BIT` 来指定由专一的gpu访问，而cpu不能访问

关于staging buffer 我觉得这个老哥（戏命尸）理解的很好，地址如下

https://blog.csdn.net/weixin_44200074/article/details/107814898

```c
GPU有自己的内存，访问自己的显存要比从CPU那边内存传来的快

临时缓冲区和顶点缓冲区没有区别，只是在创建缓冲区中设置的属性不一样而已。临时缓冲区充当辅助作用，**可以方便在缓冲区之间传递数据**，前提是需要传输队列，通常来说有图形队列的都有传输队列。
**同时让显卡可以在显存中读取数据，而不是在CPU读取数据。**
    
    缓冲区内存属性
HOST_VISIBLE: 主机可见可访问，即内存
DEVICE_LOCAL：设备内存，即显存
```

缓冲区之间传输数据需要传输队列

+ 在usegae里设置 ==源（staging buffer）== 和 ==目的（vertexbuffer）==
+ 使用command buffer来执行传输
+ vkCmdCopyBuffer（创建一个局域的commandbuffer？创建之后还要submit才算执行，完成后free掉）



#### index buffer

