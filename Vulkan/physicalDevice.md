### Physical devices and queue families

+ `VkInstance`：initializing the Vulkan library
+ look for and select a graphics card in the system that supports the features we need
+ `VkPhysicalDevice` stored the graphics card, will be destroyed when the `VkInstance` is destroyed
+ Base device suitability checks
+ properties: what you have; features: what you support
+ score, pick highest one



### Queue families

+ Every operation in vulkan should be submitted to a queue
+ Different types of queues from different queue families which allows only a subset of commands


### other 

+ c++函数返回多个值   

  ```c++
  // c++ 11 tuple数据结构
  #include <tuple>
  using namespace std;
  tuple<bool, string, string> func(const string& in){
      blah...
      return make_tuple(ture, "hello", "world");
  }
   auto[status, out1, out2]=func("test");
  // c++17 的structured bindings 的新特性
  // 效果：将返回值按照顺序绑定到方括号中的变量中
  // tuple 的缺点，需要记忆返回值的顺序
  // 将返回值定义成一个结构体
  pair<bool, out> func(){
      out o;
      return {ture, o};
  }
  // optional 的常见使用情况是，处理可能失败的函数返回值
  // 相比于pair，可以更好地处理开销高昂的对象，且更加可读
  optional<Out> func(const string& in) {
      Out o;
      if (in.size() == 0)
          return nullopt; //  nullopt c++17 中返回没有值的optional
      o.out1 = "hello";
      o.out2 = "world";
      return { o };
  }
  ```

  
