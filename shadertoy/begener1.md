## shadertoy

shader是一个处理单个像素颜色的程序，多输入数据进行处理，并输出。

```glsl
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    // uv是需要我们计算的，pixel坐标除以屏幕分辨率，标准化
    
    // Output to screen
    fragColor = vec4(uv.x, 0.0, 0.0, 1.0);
    // 屏幕里，x 从左到右 0-1
    // 屏幕里，y 从下到上 0-1
    
}

// width 600，height 250
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    
    float d = length(uv); // 每一个像素到原点（0，0）的距离
    float c = d; // 有一个阈值，超过0-1这个范围就不再变化
    fragColor = vec4(vec3(c), 1.0);
    
}


// 画一个圆，使用纵横比，使x的变化与画布的纵横比成正比
// 这样 x 增加一个单位，也就代表着增加了w/h个y单位。
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    
    // 到中心的距离，使距离从 0-1 》 0.5~0.5
     uv -= 0.5;
    // 这样画出是圆心在中心的椭圆
    // 需要补偿，aspect ratio，w:h；使用纵横比
    // x *= aspect ration; 
    // 让最终的宽高一致
    float aspectRatio = iResolution.x/iResolution.y;
    uv.x *= aspectRatio;
    float d = length(uv);
    
    // 圆内为1，圆外为0
    float c = d;
    if(d<0.3) c = 1.0;
    else c = 0.0;
    // end
    
    // 如果是要渐变呢，越靠近圆心越亮，越靠近边界越黑
    float r = 0.3;
    float c = r - d;
    // end
    
    // 使用smoothstep做平滑
    // smoothstep(T1, T2, input)，在T1和T2之间做平滑
    // T1 > T2, high -> low  1->0
    // T1 < T2, low -> high  0->1
    float r = 0.3;
    float c = smoothstep(r, r - 0.1, d);
    // end

    // Output to screen
    fragColor = vec4(vec3(c), 1.0);
}
```

