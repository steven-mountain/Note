<center>Character类</center>

character是人形的pawn，最大的特色是可以walk around

### 1、character类创建所带有的东西

```c++
// 这些组件在 ACharacter已经有了，继承就可以使用了
/** The main skeletal mesh associated with this Character (optional sub-object). */
// 这里是网格模型
UPROPERTY(Category=Character, VisibleAnywhere, BlueprintReadOnly, meta=(AllowPrivateAccess = "true"))
TObjectPtr<USkeletalMeshComponent> Mesh;

/** Movement component used for movement logic in various movement modes (walking, falling, etc), containing relevant settings and functions to control movement. */
// 很厉害的内置的功能
UPROPERTY(Category=Character, VisibleAnywhere, BlueprintReadOnly, meta=(AllowPrivateAccess = "true")) // 因为在private下这些组件不能被访问
TObjectPtr<UCharacterMovementComponent> CharacterMovement;  

/** The CapsuleComponent being used for movement collision (by CharacterMovement). Always treated as being vertically aligned in simple collision check functions. */
// 胶囊体组件，提供碰撞
UPROPERTY(Category=Character, VisibleAnywhere, BlueprintReadOnly, meta=(AllowPrivateAccess = "true"))
TObjectPtr<UCapsuleComponent> CapsuleComponent;
```



 ### 2、添加悬臂

组件一般都是visibleanywhere

```cpp
// .h
UPROPERTY(VisibleAnywhere, BlueprintReadOnly)
TObjectPtr<USpringArmComponent> SpringArm;

UPROPERTY(VisibleAnywhere, BlueprintReadOnly)
TObjectPtr<UCameraComponent> FollowCamera;
```

悬臂长度（TargetArmLength）推荐在CPP中硬编码，然后如果不合适，在蓝图中修改 

```cpp
// .cpp
// 创建组件，rootcomponent <--SpringArm <-- FollowCamera 附着
SpringArm = CreateDefaultSubobject<USpringArmComponent>(TEXT("SpringArm"));
SpringArm->SetupAttachment(GetRootComponent());
FollowCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("FollowCamera"));
FollowCamera->SetupAttachment(SpringArm, USpringArmComponent::SocketName); //这里为啥需要SocketName?
```

