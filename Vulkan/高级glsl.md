opengl内建变量理解

### gl_PrimitiveID

`gl_PrimitiveID` 的值是根据正在处理的输入图元的索引或标识符分配的。在你的情况下，如果有两个模型，分别是模型 A（4个三角形）和模型 B（5个三角形），那么在几何着色器中，`gl_PrimitiveID` 的值将从 0 开始逐渐增加，依次代表正在处理的图元的唯一标识符。

在这个示例中，`gl_PrimitiveID` 的值将如下分配：

- 对于模型 A：
  - 第一个三角形的 `gl_PrimitiveID` 为 0。
  - 第二个三角形的 `gl_PrimitiveID` 为 1。
  - 第三个三角形的 `gl_PrimitiveID` 为 2。
  - 第四个三角形的 `gl_PrimitiveID` 为 3。
- 对于模型 B：
  - 第一个三角形的 `gl_PrimitiveID` 为 4。
  - 第二个三角形的 `gl_PrimitiveID` 为 5。
  - 第三个三角形的 `gl_PrimitiveID` 为 6。
  - 第四个三角形的 `gl_PrimitiveID` 为 7。
  - 第五个三角形的 `gl_PrimitiveID` 为 8。

请注意，`gl_PrimitiveID` 的值是根据每个模型内部的图元索引递增的。这可以帮助你在几何着色器中根据模型或图元的不同执行不同的操作。