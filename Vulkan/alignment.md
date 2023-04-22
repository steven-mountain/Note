<center>Vulkan 内存对齐方式</center>

+ 标量：N对齐 (N = 4bytes)
+ `vec2`： 2N
+ `vec3`、`vec4`：4N
+ `mat4`：必须和`vec4`有同样的对齐方式
+ 嵌套结构体，由成员的基本对齐方式对齐，且为16的整数倍