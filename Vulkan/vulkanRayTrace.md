### 启用扩展

主要是raytracing pipeline以及accelerate structure



### 创建加速结构

创建加速结构的目的：减少渲染时候的 ray-triangle相交测试。

通常是在硬件中实现的一个层次化的结构。但是只有两种加速结构暴露给了开发者：

+ TLAS：包含对多个底层加速结构的引用，其上限由`VkPhysicalDeviceAccelerationStructurePropertiesKHR::maxInstanceCount`决定
+ BLAS：与场景中每个单独的三维模型对应。

TLAS通过一个3x4的变换矩阵来定位的BLAS构建的整个场景。

3x4 矩阵不能表示投影变换，因为它缺少了用于齐次坐标的第四行。这意味着它不适用于需要投影变换的场景，如透视投影或正交投影。



#### BLAS

将OBJ模型的几何数据分解为多个AS builder。其实现依赖于将数据传输给`vkCmdBuildAccelerationStructuresKHR`来构建顶层和底层加速结构。

- `VkAccelerationStructureGeometryTrianglesDataKHR`: device pointer to the buffers holding triangle vertex/index data, along with information for interpreting it as an array (stride, data type, etc.) 结构体封装了构建 **BLAS** 时需要的**三角形几何信息**，包括顶点数据、索引数据、变换数据等。**BLAS 通常代表场景中的单个物体或网格**，包含静态或动态的几何形状。

- `VkAccelerationStructureGeometryKHR`

  : wrapper around the above with the geometry type enum (triangles in this case) plus flags for the AS builder. This is needed because`VkAccelerationStructureGeometryTrianglesDataKHR` is passed as part of the union `VkAccelerationStructureGeometryDataKHR` (the geometry could also be instances, for the TLAS builder, or AABBs, not covered here). 结构体提供了构建加速结构所需的**关键几何体信息**，包括三角形网格、实例化的几何体或者更高级的几何体类型（如曲面等）。这个结构体用于描述加速结构中**单个几何体的类型、数据和布局**。

- `VkAccelerationStructureBuildRangeInfoKHR`: the indices within the vertex arrays to source input geometry for the BLAS。当你构建加速结构（无论是底层加速结构 BLAS 还是顶层加速结构 TLAS）时，`VkAccelerationStructureBuildRangeInfoKHR` 结构体告诉 Vulkan **具体要从几何体数据中使用哪些部分**来构建加速结构。对于每个 `VkAccelerationStructureGeometryKHR` 结构体（它定义了加速结构中的单个几何体），都会有一个对应的 `VkAccelerationStructureBuildRangeInfoKHR` 结构体来指定具体的构建范围。

即时通过上面的描述，还是感觉有点晦涩难懂，其实VkAccelerationStructureGeometryTrianglesDataKHR与VkAccelerationStructureGeometryKHR之间的关系，后者应该是前者的一个**抽象**。 geometry的数据不只有三角网格，还可以有其他的类型。这样在创建加速结构时候，通过geometry这个中间层来抹除底层的差异。

有了geometry为什么还要有rangeinfo呢？这里可以这么理解，就是给了的数据不一定都会被用到，就像教程里说的，OpenGL里给的数据，你最后draw的时候不一定都会用到，调用draw的时候也指定了范围的。