## shadertoy3

画一个笑脸：clamp是内建函数，input是关键字

```glsl
float circle(vec2 uv, vec2 p, float r, float blur){
    float d = length(uv -p);
    float c = smoothstep(r, r-blur, d);
    return c;
}

// clamp(value, min, max)
// 笑脸
float smileFace(vec2 uv, vec2 p, float size){
    uv -= p;
    if(size <= 0.0) size = 0.2;
    uv /= size;
    float face = circle(uv, vec2(0.0), 0.5, 0.05);
    float eye = circle(uv, vec2(0.15, 0.2), 0.1, 0.01);
    eye += circle(uv, vec2(-0.15, 0.2), 0.1, 0.01);
    
    float mouse = circle(uv, vec2(0.0, 0.05), 0.4, 0.025);
    mouse -= circle(uv, vec2(0.0, 0.15), 0.4, 0.025);
    
    float result = (face -eye - clamp(mouse, 0.0, 1.0));
    return result;
}

// 正方形
float rectangle(vec2 widthHeight, float wstart, float wend, float wblur, float hstart, float hend, float hblur){
    float w1 = smoothstep(wstart-wblur, wstart+wblur, widthHeight.x);
    float w2 = smoothstep(wend+wblur, wend-wblur, widthHeight.x);
    float h1 = smoothstep(hstart-hblur, hstart+hblur, widthHeight.y);
    float h2 = smoothstep(hend+hblur, hend-hblur, widthHeight.y);
    return w1*w2*h1*h2;
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    uv -= 0.5;
    uv.x *= iResolution.x/iResolution.y;
    vec3 mask = vec3(1.0, 1.0, 0.0);
    // mask *= smileFace(uv, vec2(0.0, 0.0), 0.8);
    mask *= rectangle(uv, -0.2, 0.2, 0.01, -0.2, 0.2, 0.01);
    fragColor = vec4(vec3(mask),1.0);
}
```

sin函数

```glsl
float rectangle(vec2 widthHeight, float wstart, float wend, float wblur, float hstart, float hend, float hblur){
    float w1 = smoothstep(wstart-wblur, wstart+wblur, widthHeight.x);
    float w2 = smoothstep(wend+wblur, wend-wblur, widthHeight.x);
    float h1 = smoothstep(hstart-hblur, hstart+hblur, widthHeight.y);
    float h2 = smoothstep(hend+hblur, hend-hblur, widthHeight.y);
    return w1*w2*h1*h2;
}

float normalValue(float value, float mina, float maxa, float minb, float maxb){
    return ((value - mina)/(maxa-mina))*(maxb-minb)+minb;
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    uv -= 0.5;
    uv.x *= iResolution.x/iResolution.y;
    float t = iTime;
    // uv *=2.0;
    vec3 mask = vec3(1.0, 1.0, 0.0);
    float x = uv.x;
    float y = uv.y - sin(t + x*8.0) * 0.2;
    float blur = normalValue(x, -0.5, 0.5, 0.1, 0.25);
    blur = pow(blur*4.0, 3.0);
    mask *= rectangle(vec2(x, y), -0.5, 0.5, blur, -0.1, 0.1, blur);
    fragColor = vec4(vec3(mask),1.0);
}
```

