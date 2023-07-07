## shadertoy2

shadertoy1总结：length(uv)计算到某一点的距离，smoothstep做平滑处理

```glsl
// 创建函数，封装带有blur的圆
float circle(vec2 uv, float r, float blur){
    float d = length(uv);
    float c = smoothstep(r, r-blur, d);
    return c;
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    
    // 到中心的距离，使距离从 0-1 》 0.5~0.5
     uv -= 0.5;
    float aspectRatio = iResolution.x/iResolution.y;
    uv.x *= aspectRatio;
    
    float c = circle(uv, 0.4, 0.1); // 调用函数返回其值
    
    // Output to screen
    fragColor = vec4(vec3(c), 1.0);
}
```

```glsl
// 加入坐标变换
float circle(vec2 uv, vec2 p, float r, float blur){
    float d = length(uv-p);
    float c = smoothstep(r, r-blur, d);
    return c;
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    
    // 到中心的距离，使距离从 0-1 》 0.5~0.5
    uv -= 0.5; // 这里得要先调整uv再使用
    float aspectRatio = iResolution.x/iResolution.y;
    uv.x *= aspectRatio;
    vec2 p = vec2(0.2, 0.1);
    float c = circle(uv, p, 0.4, 0.1);
    // 再加一个圆，为什么会这样呢，要好好去理解一下
    // 相加相当于是在求并集
    // * 求交集
    c += circle(uv, vec2(-0.5, 0.2), 0.1, 0.01);
    //end 
    
    // Output to screen
    fragColor = vec4(vec3(c), 1.0);
}
```



```glsl
// 画一个笑脸
float circle(vec2 uv, vec2 p, float r, float blur){
    float d = length(uv-p);
    float c = smoothstep(r, r-blur, d);
    return c;
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    
    // 到中心的距离，使距离从 0-1 》 0.5~0.5
    uv -= 0.5; // 这里得要先调整uv再使用
    float aspectRatio = iResolution.x/iResolution.y;
    uv.x *= aspectRatio;
    float mask = circle(uv, vec2(0.0), 0.4, 0.05);
    mask -= circle(uv, vec2(-0.15, 0.15), 0.08, 0.01);
    mask -= circle(uv, vec2(0.15, 0.15), 0.08, 0.01);
    
    float mouse = circle(uv, vec2(0.0), 0.3, 0.02);
    // 这一会将第一个圆之外的也给减掉成为-1
    mouse -= circle(uv, vec2(0.0, 0.1), 0.3, 0.02);
    
    // bug1修复, 修剪之后的值控制在0-1
    mouse = mouse < 0.0 ? 0.0 mouse;
    
    
    // bug1:这里再减就成为1了
    mask -= mouse;
    vec3 color = vec3(1.0, 1.0, 0.0) * mask;
    // Output to screen
    fragColor = vec4(vec3(color), 1.0);
}
```

