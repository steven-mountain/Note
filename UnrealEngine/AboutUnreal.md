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