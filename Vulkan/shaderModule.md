<center>piple line</center>

+ vertex shader：我们的老朋友了，将顶点从model space转换到 screen space
+ tesselation shader：说的是可以进行曲面细分，在opengl里没有接触过
+ geometry shader：好家伙和tesselation有点傻傻分不清，说的是比tesselation更灵活，但是不常用了，因为性能不好，除非实在intel的集显上
+ fragment shader：同样 老朋友了，决定最终的颜色和深度



#### 离谱的地方来了

vulkan里的图像管线几乎不可变，想要改变shader、混合模式还得自己从头创建一个新的管线。



#### Shader modules

vulkan里的shader code 要==bytecode format== 这个模式又叫 SPIR-V，被用在vulkan和opencl，这降低了GPU将shader code转换为native code的复杂性

吓我一跳，以为要自己手动写SPIR-V，原来Khronos有独立于供应商的编译器，可以将GLSL编译为SPIR-V。

可以将这个编译器包含进来作为一个库，以此来实时地产生SPIR-V，这个要用`glslangValidator.exe`，欸但是我们用`glslc`因为这玩意儿和GCC，clang用的一样的参数。离谱 vulkan sdk 竟然包含了这两个。

顶点坐标：y是反着的，向下增。z的范围是0-1



#### 加载shader

````c++
#include <fstream>

static std::vector<char> readFile(const std::string& filename) {
    std::ifstream file(filename, std::ios::ate | std::ios::binary);

    if (!file.is_open()) {
        throw std::runtime_error("failed to open file!");
    }
}

size_t fileSize = (size_t) file.tellg();
std::vector<char> buffer(fileSize);

file.seekg(0);
file.read(buffer.data(), fileSize);

file.close();
return buffer;
````



#### 创建 shader modules

将我们的shader代码封装起来，然后传入pipeline

这里有个问题，构造信息里的pcode需要的是uint32_t类型，但是我们上面的函数给的是vector的char类型，需要用 reinterpret_cast进行类型转换。

在pipeline创建之后，就可以把shadermodule删掉了

将shadermodule放在pipeline stage里，感觉像是将各个shader链接起来生成shaderprogram，在opengl里

obligatory，必须的呀



