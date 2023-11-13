CPU眼里的编程知识

### main 函数

```c++
// 相同两段代码
int main(){
    return 0;
}
int func(){
    return 0;
}
// 这两个函数的汇编指令完全相同

gcc -nostartfile -efunc test.c
```

+ CPU里函数没有主次之分，main函数的实现方法与普通函数完全一样
+ main函数是很多程序的默认起点，任何函数都可以被设置成程序的起点