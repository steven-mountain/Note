+ 图像频域的理解

  + 图像的傅里叶变换，将图像的灰度分布函数变换为图像的频率分布函数

  + 低频对应轮廓，高频对应细节
  + 频谱中，亮度大就证明梯度大
  
+ github代理设置

  ```c
  #只对github.com 
  
  # 找到自己的代理的port的4个数字的端口就行，不一定是1080口的 
  git config --global http.https://github.com.proxy socks5://127.0.0.1:1080 
  
  #上面是别人的，如果你的代理是http类型的，如下设置： 
  git config --global http.https://github.com.proxy 'http://127.0.0.1:代理的port' 
  
  #取消代理 
  git config --global --unset http.https://github.com.proxy
  ```




#### git提交规范

```c++
// commit message 格式
<type>:<subject>
// 空一行
<body>
// 空一行
<footer>
```

类型说明

| 标识符      | 描述                                               |
| ----------- | -------------------------------------------------- |
| feat        | 新功能(feature)                                    |
| fix         | 修复bug                                            |
| docs        | 文档变更、文档注释                                 |
| style       | 不影响代码内容的修改（格式修改、比如空格、分号等） |
| refactor    | 重构（既不是新增功能，也不是修改bug的代码变动）    |
| improvement | 对当前功能的改进                                   |
| perf        | 提高性能的代码修改                                 |
| test        | 添加测试或修正现有的测试                           |
| chore       | 构建过程或辅助工具的变动                           |
| ci          | ci配置文件和脚本的改动                             |
| revert      | 回滚先前提交                                       |
| delete      | 删除（代码、文档等）                               |

+ subject（必须项）

  是commit目的的简短描述、不超过50个字符

+ body

  是对本次commit的详细描述，可分成多行描述

+ footer

  一般用于关闭issue