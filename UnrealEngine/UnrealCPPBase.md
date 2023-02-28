<center>Unreal C++</center>

#### 虚幻C++基础

+ C++ 类层级结构
  + Object：存放数据，不能直接放置在场景中
  + Actor：能放置在场景中，可以有视觉表现/可以被看到
  + Pawn：可以被控制器持有
  + Character：有角色移动组件，具有适合角色封装好的一系列功能
+ is-a 和 has-a
  + 父类不是子类，子类是父类
  + Package $\rightarrow$ World $\rightarrow$ Level$\rightarrow$Actor$\rightarrow$Actor Component
  + Package还包含 Meshs 和 Textures，它们是嵌套在Package内部，不能称之为Subobject
+ 反射和垃圾回收
  + 在代码之前加上 `SPECIAL_MACRO()` 虚幻会使用UHT来实现反射和垃圾回收
  + UHT包含`#include "*.generated.h"`中，并且此包含之后不能再包含其他文件
  + 蓝图读写：BlueprintReadWrite
  + 蓝图调用：BlueprintCall
  + 分类：Category = “类别名称”
  + 一个问题：每次重新生成，反射代码即使不修改也会重新生成，改问题在4.26已经得到修复
  
+ C++类转蓝图类
  + 需要在UCLASS()中加入 Blueprintable 
  + 重新生成的方式
    + vs中在GAME中使用build
    + unrea中使用compile
  + U打头的都是继承于UObject
+ UE_LOG()
  + LOG
  + WARNNING
  + ERROR

+ 从类实例化
  + construct
+ 如何删除c++类
  + 先将由该类派生出来的蓝图类全清空
  + 关掉vs和虚幻引擎
  + 在项目文件中
    + 删除source中的.h和.cpp文件
    + 然后再打开引擎