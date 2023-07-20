## Shadertoy5

`mix`函数，OpenGL内置的函数，原型如下：

```glsl
// 需要将两种颜色融合，就需要mix
// 需要过渡渐变就用 smoothstep
// 需要0-1标准化就用
genType mix(genType x, genType y, float a){
    return x*(1-a)+y*a;
}

// 获取全局时间现在改为iTime了，iGlobalTime现在不可用了
```

```glsl
#define s(a, b, t) smoothstep(a, b, t)

float remap01(float low, float high, float value){
    float result = (value - low)/(high - low);
    return clamp(result, 0.0, 1.0);
}

// remap a to b
float remap(float alow, float ahigh, float blow, float bhigh, float value){
    float result = ((value-alow)/(ahigh-alow) * (bhigh - blow)) + blow;
    return clamp(result, 0.0, 1.0);
}

vec4 Mouth(vec2 uv, vec2 pos){
    vec4 recta = vec4(-0.3, -0.18, 0.3, 0.18);
    uv = (uv-recta.xy)/(recta.zw - recta.xy);
    uv -= vec2(0.5, -0.1);
    vec4 color = vec4(0.5, 0.18, 0.05, 1.0);
    
    uv.y *= 1.5;
    uv.y -= uv.x*uv.x*2.0;
    
    float d = length(uv);
    
    float td = length(uv - vec2(0.0, 0.6));
    vec3 toothcolor = vec3(1.0)*s(0.6, 0.35, d);
    color.xyz = mix(color.xyz, toothcolor, s(0.48, 0.45, td));
    
    float tongue = length(uv - vec2(0.0, -0.6));
    vec3 tonguecolor = vec3(1.0, 0.3, 0.3)*s(0.6, 0.35, d);
    color.xyz = mix(color.xyz, tonguecolor, s(0.48, 0.45, tongue));
    color.w = s(0.45, 0.43, d);
    return color;
}

vec4 Eye(vec2 uv, vec2 pos){
    uv -= pos;
    float d = length(uv);
    vec4 color = vec4(1.0);
    vec4 pupil = vec4(0.3, 0.5, 1.0, 1.0);
    
    color.xyz = mix(color.xyz, pupil.xyz, s(0.0, 0.15, d)*0.4); // 外部蓝色
    color.xyz *= 1.0 - s(0.15, 0.13, d) * clamp(-uv.y-uv.x, 0.0, 1.0);
    color.xyz = mix(color.xyz, vec3(0.0), s(0.09, 0.08, d)); // 外框黑色
    pupil.xyz *= (1.0 + s(0.08, 0.0, d)); // 外圈颜色深，内圈颜色浅 
    color.xyz = mix(color.xyz, pupil.xyz, s(0.08, 0.075, d)); // 内部蓝色
    color.xyz = mix(color.xyz, vec3(0.0), s(0.05, 0.04, d)); // 黑色瞳孔
    
    float hightlight = s(0.03, 0.02, length(uv-vec2(0.02, 0.04)));
    color.xyz = mix(color.xyz, vec3(1.0), hightlight);
    
    hightlight = s(0.02, 0.01, length(uv-vec2(-0.02, -0.03)));
    color.xyz = mix(color.xyz, vec3(1.0), hightlight);
    
    // color.xyz = mix(color.xyz, vec3(0.0), s(0.16, 0.15, d));
    float ramp = s(0.15, 0.13, d);
    color.w = ramp;
    return color;
}

float cheek(vec2 uv, vec2 pos){
    uv -= pos;
    float d = length(uv);
    float cheek = s(0.2, 0.0, d);
    return cheek;
}

vec4 smile(vec2 uv){
    vec4 color = vec4(0.9, 0.65, 0.1, 1.0);
    float d = length(uv);
    color.w = s(0.5, 0.49, d);
    
    float faceEdge = remap01(0.35, 0.5, d);
    faceEdge = pow(faceEdge, 3.0) * 0.5;
    color.xyz *= (1.0 - faceEdge);
    
    // out orange edge
    color.xyz = mix(color.xyz, vec3(0.6, 0.3, 0.1), s(0.47, 0.48, d));
    
    // highlight
    float highlight = s(0.41, 0.40, d);
    float ramp = remap01(0.0, 0.41, d);
    ramp *= remap(-0.41, 0.41, 0.0, 0.4, uv.y);
    highlight *= ramp;
    color.xyz = mix(color.xyz, vec3(1.0), highlight);
    
    float cheekValue = cheek(uv, vec2(0.2, -0.2));
    cheekValue += cheek(uv, vec2(-0.2, -0.2));
    color.xyz = mix(color.xyz, vec3(1., 0.0, 0.0), cheekValue*0.4);
    
    vec4 eye = Eye(uv, vec2(0.17, 0.1));
    // eye += Eye(uv, vec2(-0.17, 0.1)); // 这里不能单纯的两个eye相加
    color.xyz = mix(color.xyz, eye.xyz, eye.w);
    eye = Eye(uv, vec2(-0.17, 0.1));
    
    color.xyz = mix(color.xyz, eye.xyz, eye.w);
    vec4 mouth = Mouth(uv, vec2(0.0, -0.2));
    color.xyz = mix(color.xyz, mouth.xyz, mouth.w);
    return color;
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    uv -= 0.5;
    uv.x *= iResolution.x/iResolution.y;
    
    vec3 mask = vec3(0.0);
    vec4 smilevalue  = smile(uv);
    vec3 result = mix(mask, smilevalue.xyz, smilevalue.w);
    fragColor = vec4(result, 1.0);
}
```

