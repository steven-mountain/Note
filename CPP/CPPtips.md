### 函数返回类型为 auto

当函数的返回类型被设置为 `auto`，这意味着返回类型是**自动推导**的。编译器会根据函数内部的 `return` 语句来确定函数的实际返回类型。这在一些情况下非常有用，特别是当函数**返回类型复杂**或者依赖于**模板参数**时。



### vector 的 reserve

`vector` 的 `reserve` 函数用于**预留内存空间**，但不会改变 `vector` 的大小。通过调用 `reserve` 函数，可以在插入元素之前提前分配一定大小的内存空间，**避免每次插入元素都需要动态分配内存和复制元素**，从而**提高性能**。



### 使用单引号分割数字

在 C++ 中，单引号 `'` 用于提高数字的可读性，并且在数字常量中使用单引号分割数字并不影响其值。但是，有一些使用限制和注意事项：

1. 单引号**只能用于数字常量**中，不能用于其他类型的常量或表达式。
2. 单引号只是用于提高可读性，**不会改变数字的值**。因此，`6'000'000` 和 `6000000` 在语义上是相同的。
3. 单引号**只能**出现在数字的**中间**，不能出现在开头或结尾。
4. 单引号分割的数字常量通常用于表示较大的数值，以便更清晰地分辨数字的位数。



### uint32取模

```c++
const int MAX_FRAMES_IN_FLIGHT = 2;   
for(uint32_t i = 0; i < MAX_FRAMES_IN_FLIGHT; ++i){   	
    cout<<(i-1)%MAX_FRAMES_IN_FLIGHT<<endl;   
} 
/* 运行结果
1
0
*/
```



```c++
const int MAX_FRAMES_IN_FLIGHT = 2;   
for(int i = 0; i < MAX_FRAMES_IN_FLIGHT; ++i){   	
    cout<<(i-1)%MAX_FRAMES_IN_FLIGHT<<endl;   
}
/* 运行结果
-1
0
*/
```



### 枚举默认值

```c++
enum StageIndices
{
    eRaygen,
    eMiss,
    eClosestHit,
    eShaderGroupCount 
};
```



在 C++ 中，枚举类型的值默认从 0 开始依次递增。给定的枚举 `StageIndices` 的各个成员的值将是：

- `eRaygen` 的值是 0
- `eMiss` 的值是 1
- `eClosestHit` 的值是 2
- `eShaderGroupCount` 的值是 3

