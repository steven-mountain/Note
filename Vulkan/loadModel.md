Loading Models

### obj属性

+ attrib：包含所有的`positions`,  `normals`, `texture coordinates`
  + attrib.vertices
  + attrib.normals
  + attrib.texcoords

+ shapes：包含`separate objects` 和 `their faces`
  + separate objects
  + their **faces**
    + an array of **vertices**
      + each **vertex** contains indices of **position**, **normal** and texture **coordinate**
+ attrib包含的是实际的数据，而shapes里包含的是对应信息的索引。





### 去除重复的顶点

可以使用hash table，但是对自定义类型使用hash table，需要自己实现 相等判断运算符和 hash值得计算

+ equality test

  ```c++
  bool operator==(const Vertex& other) const {
      return pos == other.pos && color == other.color && texCoord == other.texCoord;
  }
  ```

+ hash calculation

  ```c++
  namespace std {
      template<> struct hash<Vertex> {
          size_t operator()(Vertex const& vertex) const {
              return ((hash<glm::vec3>()(vertex.pos) ^
                     (hash<glm::vec3>()(vertex.color) << 1)) >> 1) ^
                     (hash<glm::vec2>()(vertex.texCoord) << 1);
          }
      };
  }
  ```

  对GLM类型进行hash计算的函数包含在 `gtx` 文件夹中。这意味着，他们能使实验性的扩展，因此需要定义