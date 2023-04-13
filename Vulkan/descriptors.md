<center>Uniform buffers</center>

用途：主要是为了能够更新全局变量的值，而不是每次刷新vertex buffer，用resource descriptors

descriptor：一种shader可以自由获取资源（buffers，images）的方式

使用descriptors的步骤：

+ 指定一个descriptor layout（指定资源的类型） 在 pipeline 构造的时候
+ 从descriptor pool 中分配一个 descriptor set（指定真实的资源）
+ 在rendering的时候绑定 descriptor（绑定到drawing commands）



目前主要是 uniform buffer objects（UBO）



#### 使用descriptor set

+ 首先创建descriptorsetlayout bingding

  + pimmutablesamplers 只有在纹理的时候会用到
  + 所有的descriptor bingdings 都被绑定在 vkdescriptorsetlayout中

+ 关于各对象的理解可以看

  https://zhuanlan.zhihu.com/p/450434645

+ Set 高于Descriptor。

+ 一个Set下有若干个Binding Point，

+ Binding Point下有一个Descriptor Array，

+ Descriptor Array中含有一个或多个Descriptor

+ 最终利用Command绑定的单位是Set

在DescriptorSetLayout的指导下，利用Descriptor Pool提供的Descriptors，组装成一个符合DescriptorSetLayout的Set



#### 和vertex中的description的比较

同样是描述和解释数据块。Description是抽象的描述，真正绑定到Pipeline Vertex Input Stage的是Vertex Buffer本身，而Descriptor是描述器，可以认为他对数据进行了一层封装，连同自己一起参与到了数据解析的全过程中，使得真正绑定到Pipeline的是Descriptor而不再是Buffer。



c++ 的时间保持， chrono头文件