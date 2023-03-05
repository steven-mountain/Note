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



