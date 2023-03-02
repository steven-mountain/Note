<center>Actor</center>

### Actor

#### 一些默认创建的

```c++
// called when game starts or when spawned, object construct
virtual void BeginPlay() override; 

// called every frame
virtual void tick(float DeltaTime) override;

PrimaryActorTick.bCanEverTick = true; //从父类继承过来，控制该Actor是否每一帧都调用
```



#### 一些命名规则

+ A：派生自 Actor
+ U：派生自Object
+ E：Enums的前缀
+ I：Interface的前缀
+ T：Template
+ S：派生自SWidget类（slate ui）
+ F：其他类



#### UCLASS 也会继承

派生自AActor类的子类，不需要再在UCLASS中添加Blueprintable，因为AActor类已经有Blueprintable



#### C++派生蓝图类

+ 如果C++类啥也没有，会添加DefaultSceneRoot组件

+ 标识Actor在场景中的位置
+ Static Mesh：在世界中渲染静态网格
+ 相对于局部坐标的位移
+ 相对于根组件位移



### 组件

+ 在c++中添加组件

  ```c++
  // 在构造函数底下
  UPROPERTY(VisibleAnywhere) // 对于组件一般给的限定词是VisibleAnywhere
  UStaticMeshComponent* MyStaticMesh;// 不是一个基本类型
  MyStaticMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("MyStaticMesh")); //TEXT里的不是名字，是该组件在系统中的标识符，组件是Actor下的一个subobject
  ```

  会在系统不指定根组件的时候随机指定一个组件