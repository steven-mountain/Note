### Layered Architecture of Game Engine

#### Layer

+ Tool Layer: chain of editor
+ Function Layer: Make it visible, movable and playable
+ Resource Layer
+ Core Layer
+ Platform Layer  



#### Resource - How to Access My Data

offline Resource Importing

+ unify file access by defining a meta asset file format
+  Assets are faster to access by importing preprocess
+ build a composite asset file to refer to all resources
+  GUID is an extra protection of reference：Globally Unique Identifier 

现代引擎中最核心的是数据之间的关联



#### Resource - Runtime Asset Manager

+ A virtual file system to load/unload assets by path reference
+ manage asset lifespan and reference by **handle** system

管理资产的生命周期，尽可能减小内存需求，垃圾回收

  

#### Function

tick， 每一帧推进一步游戏

tickmain

+ tickLogic
  + camera
  + motor
  + controller
  + animation
  + physics
+ tickrender
  + rendercamera
  + culling
  + rendering
  + postprocess
  + present

object system

game; game loop update  



### core

从多核去思考游戏引擎架构; SIMD，内存管理; cpu L3缓存

影响游戏引擎性能的主要瓶颈

+ memory pool/Allocator
+ reduce cache miss
+ memory alignment

  

#### Platform - Graphics API

Render Hardware interface (RHI)

+ Transparent different GPU architectures and SDK
+ Automatic optimization of target platforms 



一般功能层只允许上面调下面层的，越上面的越灵活

