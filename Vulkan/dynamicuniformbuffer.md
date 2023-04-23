<center>Uniform Buffer</center>

+ 求最小对齐（2的倍数）

```c++
(a+b-1)&~(b-1)
```

求出b的倍数中a的最小上界，b一定要是2的幂