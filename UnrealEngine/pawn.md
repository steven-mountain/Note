<center><font size="5px">Pawn类</font></center>

#### pawn类根组件

将自己创建的组件附加到根组件上：

```c++
MyStaticMesh->SetupAttachment(RootComponent); // RootComponent 是默认有的
MyStaticMesh->SetupAttachment(GetRootComponent()); // Actor有的一个方法
```



#### 为pawn添加摄像机

```c++
UCameraComponent *
setRelativeLocation(FVector) 
setRelativeRotation(FRotator)
```



#### GameMode 中设置Pawn

+ 在worldsetting中设置gamemode
+ player 0 本地玩家代号 // 这里很重要，不然不知道是谁在控制player
+ AutoPossesPlayer：posses 拥有
+ EAutoReceiveInput



#### 按键映射和轴事件绑定

project setting -》engine -》input -》bindings

+ Action mappings：瞬间的，比如条约
+ Axis mappings：持续性的，比如按住w键前进



#### DeltaTime



#### Spring arm

类似于摄像机悬臂，这里spring 应该翻译成 弹簧

+ 其附加到组件为弹簧的一端
+ 另一端为被附加其上的组件

作用：

+ 如果pawn被遮挡，会移动到路径（camera到pawn）上最近的不被遮挡的点
+ 会有平滑移动的效果



#### 给actor设置默认模型和材质

+ 首先需要找到资源并将其封装在一个对象中
+ ConstructorHelper，这是个static



#### swap 仅对根组件有效

sweep的致命缺点，

不能直接将staticMesh函数的返回值赋给 RootComponent， 因为rootcomponent的修饰符既没有visible，editanywhere，因此在蓝图中detail是空的，不能修改相关的属性。

如果是将生成函数的返回值赋值rootcomponent，那么就没有staticmesh组件了。

解决办法，通过一个变量保留staticmeshcomponent，然后再将rootcomponent的值修改为staticmeshcomponent。



设置collision为pawn



#### 视野控制

##### 上下旋转

鼠标的x和y

+ x代表的是水平方向
+ y代表垂直方向的

要对俯仰角进行限制



##### 左右旋转

玩家控制controller，controller控制角色，只需要设置启用对应的controller即可

+ getcomponentrotation
+ setworldroation
+ AddControllerYawInput
+ bUseControllerRotationYaw