## Texture mapping

### Image

#### pixel data -> image object

+ pixel data
+ staging resource
+ image object

Vulkan允许将`VkBuffer`中的数据拷贝到image中。这样做在有的硬件上还更快



#### image创建

+ 查询所需内存
+ 分配内存
+ 最后绑定

注意点：

+ image有layout，用以规定内存中像素是如何组织的
+ 传输图片的layout，常用的方式是pipeline barrier



#### pipeline barrier

所要解决的问题：

​	防止资源还没有写完，就被读取。相当于给资源设置状态，防止这种 resource hazard

+ 读取文件得到数据
+ 在cpu端拷贝到中间资源
+ 中间资源
+ 使用图形API命令拷贝
+ 到实际用到的资源--》接收拷贝的状态
+ 转换--》在shader中使用的状态

![image-20230508161519578](C:\Users\BROWNFOX\AppData\Roaming\Typora\typora-user-images\image-20230508161519578.png)

#### 加载图像

stbi加载的图像是一行一行排列的



#### 中间资源

利用staggingbuffer，将加载的数据拷贝到里面



#### texture image

剩下要做的，就是将staging buffer中的数据拷贝到texture image中

+ 首先将创建image的信息放入VkImageCreateInfo中
+ 其中imageinfo的extent表示的是各个轴上像素的个数，width、height、depth分别代表在x, y, z轴上的数量，因此2D图像在Z轴上只有一个像素，所以depth必须是1
+ 图片的格式应该和stagging buffer里的一致，不然会copy失败

+ tiling：应该就是用来指定，像素是如何排列的，并且这个指定了之后，就不能再改变了
+ initialLayout：参数有两个，对GPU都不可用，用于指定渲染流程开始前的图像布局方式
+ usage：为了能让shader获取到图像，需要一个`VK_IMAGE_USAGE_SAMPLED_BIT`
+ imagelayout：图像布局，表示的是使用这种布局的图像子资源所支持的操作
+ layout transition：其实就是对应操作的转换
+ barrier：shader stage要和usage对应，如果指定的是非shader的pipeline stage会报错
+ 