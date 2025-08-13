# Three.JS Shaders - GLSL

- [What is Shader](#what-is-shader)
- [Shader Setup](#shader-setup)
- [GLSL Basics](#glsl-basics)

## What is Shader

In 3D Video graphics the main thing we want to do is render objects. 

For example, we want to tell the GPU to draw a sphere. The GPU can only draw triangles. 

What GPU needs to draw the triangle is
- Vertex Shader
  - where the **positions** of the triangle points are located
- Fragment Shader
  - how to fill the triangle with a **color**

We need to use GPU, because it has hunders of threads which is a much higher number compared with CPU for multi tasking

**Vertex Shader**

A program that runs for every single vertex. The programming language is GLSL. 
These vertices have no idea of what position other vertices have

**Fragment Shader**

A program that runs for every single pixel in the triangle, and fills the color of the triangle

---

## Shader Setup

**Example with default shaders**

```
const geometry = new THREE.IcosahedronGeometry(1, 5)
const material = new THREE.ShaderMaterial({
  vertexShader: vertexShader,
  fragmentShader: fragmentShader
})

const ico = new THREE.Mesh(geometry, material)
scene.add(ico)
```

**Default Shaders in three module**

node_modules > three > src > materials > ShaderMaterial.js


`default_certex.glsl.js`

```
export default /* glsl */
void main(){
  gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0)
}
```

VS Code Extension: `glsl-literal`

`default_fragment.glsl.js`

```
export default
void main(){
  gl_FragColor = vec4(1.0, 0.0, 0.0, 1.0)
}
```

Shaders are strings which we compile

We copy those inside the project `./shaders/vertex.glsl`, `./shaders/fragment.glsl`

---

### GLSL Basics

Different types: `float, double, int, uint, bool`

and also GLSL Types: 

x, y, z are `floats`, floating points 
```
// vec3 -> x, y, z
// vec2 -> x, y
// vec4

// ivec3 -> x, y, z (for int)
// uvec3
// bvec2
// dvec3
```

**Vector Variables**

```
vec4 color = vec4(0.0, 1.0, 0.0, 1.0)
// vec4 color = vec4(1.0) -> setting all x, y, z, w to 1
gl_FragColor = color;

vec4 color = vec4(vec2(1, 1), vec2(1, 1));

float greenValue = 1.0;

vec4 color = vec4(vec3(0, greenValue, 0), 1.0);

gl_FragColor = color;


vec3 myVector = vec3(1);
myVector.x = 0.0;
myVector.x = 0.;
myVector.x = 0.f;

```

**Structs**
```
struct vec5 {
  float x;
  float y;
  float z;
  float w;
  float q;
};


void main() {
  vec5 myVector = vec5(1.0, 1.0, 1.0, 1.0, 1.0);
}

```

**Functions**

```
float sum(float a, float b){
  return a + b;
}
```

```
gl_FragColor = vec4(1, sum(0.5, 0.5), 0, 1);
```

---

 
**Swizzle masks**


```
vec3 color = vec3(1, 0, 1);

color.x = 0.0;
//or
color.x = float(0); // casts it to float

color.r = 0.0; // equal to color.x = 0.0;

gl_FragColor = vec4(color, 1);

//Swizzle mask
color.rg = vec2(0, 1);

color.gr = vec2(1,0);

color.grb = vec3(1, 0, 0);

// equal to
color.yxz = vec3(1, 1, 0);

gl_FragColor = vec4(color.xxx, 1); // equal to vec4(vec3(1, 1, 1), 1) 

```

**Operators**

```
vec3 color = vec3(0, 0, 0);
color += vec3(0, 0, 1);
color -= vec3(0, 0, 1);
color *= vec3(0, 0, 1);
color /= vec3(0, 0, 1);

vec2 myVector = color.xx / vec2(2, 2);

gl_FragColor = vec4(color, 1);

```

**Math functions**

```
vec3 color = vec3(0, 0, 0);

// % operator doesn't exist, mod exists
float newFloat = mod(color.x, 2.0);

// abs()
// min, max
// sin, cos, tan, atan
// dot, cross

// clamp(-0.1, 0, 1) // -> clamp -0.1 to fit in a certain range (if below 0, it converts it to 0. If above 1, converts it to 1)

// step, smoothstep, fract,

bvec3 isEqual = equal(vec3(1), vec3(0)); // should be false, using equal function

gl_FragColor = vec4(isEqual, 1); //takes bvec3 and converts it to float
```





