+ isual studio 下方的就绪会显示当前的进度

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
+ playerposses还有资源加载等，即使在c++中保存在unreal中重新编译，在unreal中依旧不能正常加载，要退出引擎，在vs中重新生成后打开才行。