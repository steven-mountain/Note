Ubuntu 显卡驱动的坑

### gcc版本切换

要在已安装的 GCC 版本之间切换，你可以按照以下步骤操作：

1. 首先，打开终端并执行以下命令，将需要的版本添加到 `update-alternatives` 中：

   ```bash
   sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 70
   ```

   

   这里我们将 GCC 11 添加到优先级为 70。你可以根据需要调整优先级。

2. 然后，手动切换默认的 GCC 版本：

   ```bash
   sudo update-alternatives --config gcc
   ```

   

   根据提示选择你想要设置为默认的 GCC 版本。

3. 查看版本是否切换成功：

   ```bash
   gcc -v
   ```



### 在docker中使用optix

https://github.com/NVIDIA/nvidia-docker/issues/990

I am adding this for those wondering why OptiX is not working with `NVIDIA_DRIVER_CAPABILITIES=graphics`

To break it down, you have the "old" option -- mounting the needed drivers/libs:

```
# Assuming the SDK is installed in /tmp/NVIDIA and the `build` directory is within that for the `optixHello` sample

# In this case:
# Ubuntu 20.04
# Cuda 11.4
# NVIDIA 470
# This means we can only go up to Optix7.3 (due to R470)

docker run -v /tmp/NVIDIA:/tmp/NVIDIA \
-v /usr/lib/x86_64-linux-gnu/libnvoptix.so.1:/usr/lib/x86_64-linux-gnu/libnvoptix.so.1 \
-v /usr/lib/x86_64-linux-gnu/libnvidia-rtcore.so.470.82.01:/usr/lib/x86_64-linux-gnu/libnvidia-rtcore.so.470.82.01 \
--gpus all -it --rm nvidia/cuda:11.4.3-runtime-ubuntu20.04
```



If you run `optixHello` -- this will work.

And here is the "new" option:

```
docker run -v /tmp/NVIDIA:/tmp/NVIDIA \
-e NVIDIA_DRIVER_CAPABILITIES=graphics,compute,utility \
--gpus all -it --rm nvidia/cuda:11.4.3-runtime-ubuntu20.04
```



^ At first attempt this will not work -- and most folks that are complaining about it not working are probably running int this -- you will see that you have both libs within the container:

```
/usr/lib/x86_64-linux-gnu/libnvoptix.so.470.82.01
/usr/lib/x86_64-linux-gnu/libnvoptix.so.1
```



However, looking more closely will show you that `/usr/lib/x86_64-linux-gnu/libnvoptix.so.1` is 0 bytes.

The reason is because from the HOST running Docker, you have:

```
# cd /usr/lib/x86_64-linux-gnu/
# ls -alh | grep libnvoptix
lrwxrwxrwx  1 root root    23 Nov 16  2021 libnvoptix.so.1 -> libnvoptix.so.470.82.01
-rw-r--r--  1 root root  161M Oct 27  2021 libnvoptix.so.470.82.01
```



The symlink is translating into an empty map.

The easy way to fix this within the container is:

```
ln -sf /usr/lib/x86_64-linux-gnu/libnvoptix.so.470.82.01 /usr/lib/x86_64-linux-gnu/libnvoptix.so.1
```



As soon as you do that it works:

```
root@0eddf5bed7c2:/tmp/NVIDIA/build/bin# ./optixHello 
Caught exception: OPTIX_ERROR_LIBRARY_NOT_FOUND: Optix call 'optixInit()' failed: /tmp/NVIDIA/SDK/optixHello/optixHello.cpp:124)


root@0eddf5bed7c2:/tmp/NVIDIA/build/bin# ln -sf /usr/lib/x86_64-linux-gnu/libnvoptix.so.470.82.01 /usr/lib/x86_64-linux-gnu/libnvoptix.so.1


root@0eddf5bed7c2:/tmp/NVIDIA/build/bin# ./optixHello 
[ 4][       KNOBS]: All knobs on default.

[ 4][  DISK CACHE]: Opened database: "/var/tmp/OptixCache_root/cache7.db"
[ 4][  DISK CACHE]:     Cache data size: "15.9 KiB"
[ 4][   DISKCACHE]: Cache hit for key: ptx-6766-keydeb0e13958c7dc89fbcbe36c70c7e95d-sm_80-rtc0-drv470.82.01
[ 4][COMPILE FEEDBACK]: 
[ 4][COMPILE FEEDBACK]: Info: Pipeline has 1 module(s), 1 entry function(s), 0 trace call(s), 0 continuation callable call(s), 0 direct callable call(s), 1 basic block(s) in entry functions, 79 instruction(s) in entry functions, 0 non-entry function(s), 0 basic block(s) in non-entry functions, 0 instruction(s) in non-entry functions

GLFW Error 65544: X11: The DISPLAY environment variable is missing
Caught exception: Failed to initialize GLFW
```

Hope this helps others.