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

设置图形管道来接收数据



#### vertex buffer

+ 在vulkan中buffer是一块内存区域用于存储共显卡随机读取的数据

+ 和vulkan对象不同，buffer不会自动分配内存

+ 需要vkbuffercreateinfo结构体

创建buffer对象之后，还需要查询其内存需求，然后为其分配内存

+ VkMemoryRequirements 

+ VkPhysicalDeviceMemoryProperties ->memorytype

+ 内存分配
+ bindbuffermemory
+ 当buffer不可用的时候，我们的memory也就可以清除了