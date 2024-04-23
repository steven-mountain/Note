vulkan同步机制

commands start execution in-order, but complete out-of-order. 这篇笔记抄自[Maister's Graphics Adventures](https://themaister.net/blog/)

链接如下：http://themaister.net/blog/2019/08/14/yet-another-blog-explaining-vulkan-synchronization/

### commands overlap

once you submit，vulkan只会将命令看成线性的指令流（a linear stream of commands）



### pipeline stages

在vulkan中做同步工作，其实是将渲染管线各个阶段的工作作为一个整体进行同步。



### TOP_OF_PIPE、BOTTOM_OF_PIPE

TOP_OF_PIPE：相当于对指令的预处理？GPU上的指令处理器解析命令

BOTTOM_OF_PIPE：在完成所有工作之后，指令退出（retire）



### In-queue execution barriers

#### pipeline Barrier

在vulkan中执行pipeline barrier的函数声明如下：

```c++
void vkCmdPipelineBarrier(
    VkCommandBuffer                             commandBuffer,
    VkPipelineStageFlags                        srcStageMask,
    VkPipelineStageFlags                        dstStageMask,
    VkDependencyFlags                           dependencyFlags,
    uint32_t                                    memoryBarrierCount,
    const VkMemoryBarrier*                      pMemoryBarriers,
    uint32_t                                    bufferMemoryBarrierCount,
    const VkBufferMemoryBarrier*                pBufferMemoryBarriers,
    uint32_t                                    imageMemoryBarrierCount,
    const VkImageMemoryBarrier*                 pImageMemoryBarriers);
```

如果忽略掉`memory barrier`和`flag`，那么那么只剩下`srcStageMask`和`dstStageMask`， vulkan同步机制的核心：利用barrier将command stream一分为二。将这两部分以某种方式进行同步。

+ everything before the barrier
+ everything aftert the barrier



##### srcStageMask

这代表的是我们要等待什么，vulkan并没有提供单个命令之间的依赖控制。相反，这种控制是在渲染管线的某个阶段。

等待的命令包括：之前已经提交到queue中的命令以及记录当前命令的command buffer里已有的命令。srcStageMask限制我们所等待的范围，只有与之对应的阶段的工作才是我们所等待的。

特别的`ALL_COMMANDS_BIT`等待queue里的所有工作，`ALL_GRAPHICS_BIT`等待render pass里的所有工作



##### dstStageMask

这代表着barrier的第二部分。任何工作在barrier之后的都得等待srcStageMask所指的工作完成之后才能执行。这只影响特定阶段的工作。在srcStageMak所限制的范围内的阶段的工作完成之后，当dstStageMask阶段到达之后，工作就可以开始执行。



#### events 也叫 split barriers

在barriers之间overlapping work。The idea of VkEvent is to get some unrelated commands in-between the “before” and “after” set of commands。知乎[Xinzhao](https://zhuanlan.zhihu.com/p/80692115)说这个像CPU中的release-acquire。这里CSDN上的[赱乂](https://blog.csdn.net/u014653854/article/details/80631460?ydreferer=aHR0cHM6Ly93d3cuYmluZy5jb20v)我觉得对这两者之间的关系的解释很清楚：acquire与release之间的关系：在实现中不包含真正的许可对象，并且Semaphore也不会将许可与线程关联起来，因此在一个线程中获得的许可可以在另一个线程中释放。**可以将acquire操作视为是消费一个许可，而release操作是创建一个许可**，Semaphore并不受限于它在创建时的初始许可数量。也就是说acquire与release并没有强制的一对一关系，release一次就相当于新增一个许可，许可的数量可能会由于没有与acquire操作一对一而导致超出初始化时设置的许可个数。



event不能用于不同queue之间同步

#### Execution dependency chain

在srcStageMask中的依赖也会传递到被dstStageMask所阻塞的阶段。这些被阻塞的阶段不仅要等待dstStageMask还要等待dstStageMask所具有的dependencies。相当于是多个barrier构成的链接。

例子：

```c++
1 vkCmdDispatch
2 vkCmdDispatch
3 vkCmdPipelineBarrier(srcStageMask = COMPUTE, dstStageMask = TRANSFER)
4 vkCmdMagicDummyTransferOperation
5 vkCmdPipelineBarrier(srcStageMask = TRANSFER, dstStageMask = COMPUTE)
6 vkCmdDispatch
7 vkCmdDispatch
```

这里便有一个控制链：`COMPUTE` -> `TRANSFER` -> `COMPUTE`，同时{4}必须等待{1、2}完成，{6，7}必须等待{4}完成。



#### Pipeline stages and render passes

renderPass有一点复杂，renderPass中有两大类的pipeline stages：geometry 和 fragment。

pipeline stages（TOP_OF_PIPE, DRAW_INDIRECT, COMPUTE/TRANSFER, BOTTOM_OF_PIPE）



##### Geometry

- DRAW_INDIRECT – Parses indirect buffers
- VERTEX_INPUT – Consumes fixed function VBOs and IBOs
- VERTEX_SHADER – Actual vertex shader
- TESSELLATION_CONTROL_SHADER
- TESSELLATION_EVALUATION_SHADER
- GEOMETRY_SHADER



##### Fragment

- EARLY_FRAGMENT_TESTS，where early depth/stencil tests happen, also loadOp of a depth/stencil attachment
- FRAGMENT_SHADER
- LATE_FRAGMENT_TESTS, late depth/stencil tests happen, also storeOp
- COLOR_ATTACHMENT_OUTPUT, This one is where loadOp, storeOp, MSAA resolves and frame buffer blend stage takes place



### Memory barriers

需要弄清两个概念，momeory available和 visible。



`VkMemoryBarrier` 用于**同步不同管线阶段之间对所有内存的访问**，确保在屏障之前的内存写操作完成后，屏障之后的内存读操作才能开始。它是一种确保内存操作顺序的高层次同步机制。



当L2cache包含所有最新的数据，那么我们就可以说memory是available的。L2与所有的L1相连。L1是不连续的。每一个GPU核心都有一系列的L1缓存。将pipeline stage + access mask结合来使用。当memory可用时（也就是L2中的数据是最新的数据的时候）memory 对于特定 stage + access mask 可见。

这个老哥对available和visible的通俗的理解是：

+ "makeing memory available" is all about flushing caches. 确保cache与memory中的内容一致。将数据更新到最新的状态。
+ "making memory visible" is invalidating caches.  将caches的状态设为invalid，处理器需要memory中的某些数据，这些数据不一定在cache中，将cache设置为invalid，表示需要从memory中加载最新的数据。



#### cache刷新的三种方式

+ clean：检查dirty bit，如果为1，则将cache中的内容写入下一级存储，并将dirty bit置为0
+ invalid：检查对应cache的valid bit，如果valid bit 为1，置为0
+ flush：对每条cache line先clean，再invalid



GPU原理：

GPU的工作方式是SIMD(Single Instruction Multiple Data) ，所有core的计算操作相同，但是输入的数据有所不同。GPU的优势不在于核心的处理能力，而在于大规模并行处理数据。不同的core的信息相互独立比较好办，但是如果在异步问题中，不同的core需要相互交互信息，计算的结构不规则，负载不均衡。



在异步运算中，需要多个core可以在共享的内存空间中完成读/写操作。但是GPU中的core数量数以千计，如果每个core都可以访问共享内存，管理是非常困难的，并且开销也是巨大的。折中方案，将GPU内的core分类为多个组，形成多个流处理器（Streaming Multiprocessors）简称SMs。





##### VkMemoryBarrier

这个使全局的barrier，作用于当前所有的resource，而操纵某个resource的时候，根据resource的类型，分别使用buffer或者image memory barrier。

```c++
typedef struct VkMemoryBarrier {
    VkStructureType sType;
    const void* pNext;
    VkAccessFlags srcAccessMask; // 前一个操作的访问模式
    VkAccessFlags dstAccessMask; // 后一个操作的访问模式
} VkMemoryBarrier;
```

在`vkCmdPipelineBarrier`中，以下四件事情会按序执行：

+ 等待srcStageMask阶段的工作完成
+ 使满足 `srcStageMask` + `srcAccessMask`组合的写入操作（写入cache还是写入memory）可执行？ make writes performed available
+ 使 `available` 的memory 对于所有 `dstStageMask` + `dstAccessMask` visible
+ 解锁在 `dstStageMask` 阶段的所有work

stageMask不能为0，AccessMask可以设置为零，代表不等待或者不锁住某一块block。



### VkBufferMemoryBarrier

啥意思？这个老哥认为，这玩意儿没啥用。GPU 不care



### VkImageMemoryBarrier

VkBufferMemoryBarrier无关痛痒，但是这个非常重要。需要转换image的layout，就需要用到这个。

关于imageLayout？图像的布局方式？翻译过来好像是，但是已经有tiling了，为啥还要layout呢。我看有的blog写的是Layout指的是GPU压缩图像的方式



#### 关于imageLayout概念：

描述的是图像数据在`不同操作之间`的`状态`和`布局`, 是一种管理图像内存访问的方式。

+ 图像布局的作用

  + 控制图像数据的内存访问模式，以便在不同操作中进行优化
  + 确保图像能够在GPU上高效地读取和写入
  + 通过转换图像布局来处理不同类型的操作，例如渲染、传输、存储等。

+ 图像布局的转换

  在执行不同的操作之前，需要将图像的布局从一个类型转换为另一个类型。

Tiling 用于描述图像数据在内存中的物理排列方式，主要影响的是内存访问效率。区别如下：

+ image layout：关注图像数据在不同操作之间的逻辑布局和状态转换。用于控制GPU内部的读写操作
+ Tiling：关注图像数据在内存中的物理排列方式，确保内存访问的效率

个人感觉这个，layout像是创建了一个类型，图像的某一个layout也就说明了将内存中的数据如何解析出来。就好比是我们有二维数组，int** array，二维数组在内存中可以是**行优先**也可以是**列优先**，这种在内存中的组织方式就是tiling。而如何从内存中读取数据，以及如何**解析**这些数据，例如我们知道 **array\[1]\[1]是一个int类型的数字，这就是layout的作用。而transition image layout就有点像 int 转换为float，但这并不会影响其在内存中的布局。



#### 继续imageMemoryBarrier

layout transition happens in-between the make available and make visible stages of a memory barrier.

后面这段话也挺重要的，直接全文抄下来：

The layout transition itself is considered a read/write operation, and the rules are basically that memory for the image must be **available**（有点像是git的rebase，确保write或者做出修改的是最新的数据） before the layout transition takes place. After a layout transition, that memory is automatically made **available** (but not **visible**!). Basically, think of the layout transition as some kind of in-place data munging which happens in L2 cache somehow.



在vulkan里新分配的内存memory，我们总是认为它对所有的stages和access types 都是 available 和 visible的。





### Implicit memory ordering - semaphores and fences

+ Semaphore: GPU<-> GPU, across Vulkan queues

+ fences: GPU->CPU

它们作为`vkQueueSubmit`的一部分被signaled。最重要的是得知道它们如何与memory交互。

激活？signaled 一个semaphore或者fence，**前提是之前提交到queue上的command必须已经完成**，



### Implicit memory guarantees on vkQueueSubmit

激活一个fence或者一个semaphore，就像是在full cache flush。提交commands到vulkanqueue上，会使得所有的被host端可访问的memory 也对all stages and access masks visible。

submitting a batch issues a cache invalidation on host visible memory. 这里的invalidation应该是对GPU而言的，因为后文说当CPU像staging buffer中写入数据的时候，不需要手动invalidation。如果写入是在vkQueueSubmit之前，这（invalidation）也会自动执行。

例如这样的barrier：

```c++
srcStageMask = HOST
srcAccessMask = HOST_WRITE_BIT
dstStageMask = TRANSFER
dstAccessMask = TRANSFER_READ
```



### Implicit memory guarantees when waiting for a semaphore

+ 激活一个semaphore信号量会使得所有的memory都是available。为啥？就相当于是生产者消费者中，给盘子加锁，相当于是在更新盘中的内容至最新。

+ 而等待一个semaphore相当于是使memory visible？为啥？从盘子中读取加载最新的数据。那么所有的memory自然就是visible的。
+ signal/wait 工作方式就像是a full memory barrier

Signalling the semaphore waits for all commands, and all writes in the dispatch are made available to the device **before** the semaphore is actually signaled.



### Execution dependency chain with semaphore

pDstWaitStageMask in vkQueueSubmit blocks certain stages from executing.

If we create a pipeline barrier with **srcStageMask** targeting one of the stages in the **wait stage mask**, we also wait for the semaphore to be signaled. 

This is extremely useful for doing **image layout transitions** on swapchain images。



### Host memory reads

我觉得这个老哥说得挺好的，直接原文抄下来吧

While signalling a fence makes all memory available, it does not make them available to the CPU, **just within the device**. This is where dstStageMask = PIPELINE_STAGE_HOST and dstAccessMask = ACCESS_HOST_READ_BIT flags come in. If you intend to read back data to the CPU, you must issue a pipeline barrier which makes memory available to the HOST as well. In our mental model, we can think of this as flushing the GPU L2 cache out to GPU main memory, so that CPU can access it over some bus interface.



### Safely recycling memory and aliasing memory

+ recycle：In order to recycle memory, we must have observed that the GPU was done using the image with a fence.

+ alias：The rule here is that in order to safely alias, all memory access from the active alias must be made available before a new alias can take its place.（别名内存是指在Vulkan中存在多个内存对象引用相同物理内存区域的情况）



### External subpass dependencies

The main purpose of external subpass dependencies is to deal with **initialLayout** and **finalLayout** of an **attachment reference**.  If initialLayout != layout used in the first subpass, the render pass is forced to perform a layout transition.

**The external subpass dependency is basically just a vkCmdPipelineBarrier injected for you by the driver.** 



If you don’t specify anything else, that layout transition will wait for nothing before it performs the transition. Or rather, the driver will inject a dummy subpass dependency for you with srcStageMask = TOP_OF_PIPE_BIT. 

If finalLayout differs from the last use in a subpass, driver will transition into the final layout **automatically**. Here you get to change dstStageMask/dstAccessMask. If you do nothing here, you get BOTTOM_OF_PIPE/0.



#### External Subpass depencies 的用处

##### Automatically transitioning TRANSIENT_ATTACHMENT images

If you’re on **mobile**, you should be using transient images where possible.

the external subpass dependency writes itself, and we can just use transient attachments without having to think too hard about how to synchronize them

Of course, we could just inject a pipeline barrier for this exact same purpose, but that’s more boilerplate.



##### Automatically transitioning swapchain images

直接全抄下来吧，后面忘了回过头来看看

swapchain images are always just used once per frame, and we can deal with all synchronization using external subpass dependencies. We want initialLayout = UNDEFINED, and finalLayout = PRESENT_SRC_KHR.

srcStageMask is COLOR_ATTACHMENT_OUTPUT which lets us link up with the swapchain acquire semaphore. For this case, we will need an external subpass dependency. For the finalLayout transition after the render pass, we are fine with BOTTOM_OF_PIPE being used. We’re going to use semaphores here anyways.

