<center>Fixed functions</center>

在vulkan里这所有的阶段都要显示的指定，繁琐啊



#### Dynamic state

有少部分的状态可以bake到pipeline中，但是仍有少数的state可以在 draw time的时候改变而不需要重新创建pipeline，例如viewport、linewidht 和 blend constants

需要将所需要改变的state放在 vkPipelineDynamicStateCreateInfo



#### Vertex input

有点像opengl的attributePointer，描述的vertex的数据格式，并发送给vertexshader。两种描述方式

+ Bindings：数据之间的space（不懂，说是这么说），数据是逐顶点还是逐实例（这里的实例不是前面的实例对象，而是geometry instancing，这玩意儿有啥用？用于在场景中渲染同一个mesh的多个副本，像树、艹、建筑等）
+ Attribute descriptions：传输给顶点着色器的attributes。从哪个绑定中加载他们，以及偏移是多少



#### Input assembly

跟OpenGL里的point、line、lineloop等类似，相当于是如何去解析这些点

`VkPipelineInputAssemblyStateCreateInfo`，



#### viewports and scissors

viewports描述的是framebuffer的大小，通常都是`(0, 0)`到`(width， height)`

scissors：定义了那一region区域的像素会被保存，在这之外的像素都会被rasterizer舍弃

要想花满viewport，那就把scissor的大小设置为和viewports一样大



#### Rasterizer

老朋友了，光栅化嘛，将顶点转换为fragment。还会进行 depth testing，face culling 还有 scissor test。

+ depthclampenable：启用会将远近平面之外的夹掉而不是丢弃它们。麻烦的是用这玩意儿，需要启用==GPU feature==
+ rasterizerdiscardenable：这玩意儿启用了，那不就是禁用rasterizer吗，几何体永远不会通过光栅化这个阶段
+ polygonMode：决定如何为几何体生成fragments，吭啊，要使用fill之外的就需要启用 ==gpu feature==

+ frontFace：指定证明顶点的顺序



#### multisampling

这是执行反走样的一种方式。启用这个又双叒需要启用 ==gpu feature==

离谱



#### Depth and stencil testing

挖坑 后面讲



#### Color blending

将fragment shader返回的颜色和fragmentbuffer里的颜色combined

+ mix，混合两种颜色产生新的颜色
+ combine，将新旧两种颜色 逐位操作



VkPipelineColorBlendAttachmentState：包含每一个attached framebuffer的配置

VkPipelineColorBlendStateCreateInfo：包含 global color blending 设置



#### pipeline layout

可以使用uniform，就跟dynamic variable一样，可以在drawing的时候修改。

uniform需要在pipeline创建的时候去指定，通过创建 vkpipelinelayout。
即使不用，也要创建一个空的 pipeline layout



#### conclusion