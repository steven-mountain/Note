<center>collision</center>

#### 宏补充

+ 一般对于指针不能用edit，一般只能用visible

+ 限制输入范围：meta = (ClampMin = -5.0f, ClampMax = 5.0f, UIMin = -5.0f, UIMax = 5.0f)

  Clamp限制的是输入的范围，UI限制的是拖动的范围



#### 碰撞

##### 简单碰撞

一般在引擎中，默认采用简单碰撞

创建简单碰撞：在模型详情页面，点击collison选择对应的碰撞体即可

##### 复杂碰撞

费性能，并且不能对物体进行物理模拟



#### 物理模拟与重力

勾选 simulate physic就行



#### 在代码里增加力和力矩

给staticmesh增加力矩，但是直接使用 `->` 会报找不到头文件的错误。需要引用相应组件的头文件，从classes之后的开始引用

+ 添加力：AddForce()
+ 添加力矩：AddTorque，在UE5中改成了AddTorqueInRadians

这个问题在 4.27 之后就解决了，不用再添加头文件

+ 默认是根据==质量==来计算，函数的第三个参数为bool值，可以理解为是否忽略质量

要施加力，需要启动物理模拟

Sweep：扫描，如果一般就是启用这个，给物体添加偏移，将AddActorLocalOffset的第二个参数设置为true



#### 碰撞通道与击中信息

+ 碰撞的三种模式
  + ignore，忽略
  + overlap，重叠，仅仅感知物体时候发生重叠，而不阻挡物体
  + block，阻挡物体

+ 碰撞是相互的，想要穿过物体，可以设置一个对另一个的ignore，反之亦然
+ TEXT()，格式化输出，TEXT("x:%f, y:%f..."), location.x, location.y
+ 