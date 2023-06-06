+ 软连接问题

如果有temp就是

```bash
ldconfig
```



+ 查找路径

```bash
find  -name libnvidia-ml*
```



+ 建立软链接

```bash
cd /usr/lib/x86_64-linux-gnu

# 建立软链接 libcuda.so > libcuda.so.1
cp libcuda.so libcuda.so.backup
rm libcuda.so
ln -s libcuda.so.1 libcuda.so

# 450.80.02是宿主机的驱动版本
# 建立软链接 libcuda.so.1 > libcuda.so.450.80.02
cp libcuda.so.1 libcuda.so.1.backup
rm libcuda.so.1
cp libcuda.so.450.80.02 libcuda.so.1

# 建立软链接 libnvidia-ml.so.1 > libnvidia-ml.so.450.80.02
cp libnvidia-ml.so.1 libnvidia-ml.so.1.backup
rm libnvidia-ml.so.1
ln -s libnvidia-ml.so.450.80.02 libnvidia-ml.so.1

————————————————
版权声明：本文为CSDN博主「qc1752」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_45266796/article/details/126583151
```



### 查看驱动版本信息

```bash
ls /usr/src | grep nvidia

# 或者
 ls | grep libnvidia-ml
```

