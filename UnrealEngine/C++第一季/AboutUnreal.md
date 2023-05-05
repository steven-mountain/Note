<center>Unreal Engine</center>

#### 一些tip

+ visual studio 下方的就绪会显示当前的进度

+ uproject 是个文本文档，里面可以查看项目的信息

+ 将项目打包给别人至少包含config、content、uproject
+ 原型使用Geometry
  + 不会拉伸材质
  + 可以进行布尔运算
  + 费性能：create static mesh转换为静态网格
  + 有专门的geometry编辑模式
+ content browser里控制显示，使用view options
+ 在UE5中，错误 实时编码处于活动状态时无法构建。退出编辑器 和游戏，或者在编辑器中迭代代码时按 Ctrl+Alt+F11 或 游戏
+ 此功能需要从 Unreal 而不是从 IDE 构建。
+ 如果不指定根组件，那么会自动生成一个默认的根组件（scene component）
+ Actor 会有默认的 RootComponent 组件
+ 在声明中，如果编译器无法识别类，可以在声明前面加上 class 关键字
+ ==end== 键将选中的物体吸附到地面





#### 三级设置

+ Editor preference
+ Project settings
  + Maps & models
+ World settings



#### 常见光源

+ 平行光
  + 跟位置无关
  + 只跟方向有关
+ Sky light
  + 捕捉摄像机范围外的光线，以环境光的方式添加到范围内的物品上面

+ 纯静态光源
  + 选择static
  + 不投射阴影和其他操作
  + 仅仅只是照亮物体
  + 所有的结果都是计算好的
  + build lighting only



#### 蓝图傻傻分不清

+ 蓝图系统(类)，内容里下方带蓝条的的就是蓝图类，包裹游戏数据
+ 蓝图脚本，用于实现游戏逻辑（也可用c++实现）