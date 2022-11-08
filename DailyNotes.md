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

  