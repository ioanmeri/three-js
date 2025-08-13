# Three.JS Shaders - GLSL

- [What is Shader](#what-is-shader)
- [Shader Setup](#shader-setup)
- [GLSL Basics](#glsl-basics)
- [Attributes And Uniforms](#attributes-and-uniforms)
- [UVs And Normals](#uvs-and-normals)
- [Varying](#varyings)

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

---

## Attributes And Uniforms

`vertex.glsl`

```
void main() {
  gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
}
```


**RawShaderMaterial**

It doesn't have the boilerplate code, which provides values for the vertexShader
- projectionMatrix
- modelViewMatrix
- position

```
const material = new THREE.RawShaderMateril({
  vertexShader: vertexShader,
  fragmentShader: fragmentShader
})
```

```
attribute vec3 position;

uniform mat4 projectionMatrix;
uniform mat4 modelViewMatrix;


void main() {
  gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
}
```

**Attributes**

```
console.log(geometry)
```

Javascript and three.js needs to communicate with Shader. We need to send data to Shader

For example, `IcosahedronGeometry.attributes.position.array` contains `6480` points to construct the sphere.

We are sending this array to the vertexShader, and the vertexShader draws the triangles

Attributes are vertex specific data, that we are storing in an array

**Uniforms**

```
mat4 -> 4x4 matrix
mat2
[0, 0
 0, 0]

max2x2
max2x3

```

```
console.log(material)
```

`RawShaderMaterial.uniforms // empty object`

```
material.uniforms.uTime = {
  value: 0
}
```

**Example**

Three.js is passing the uniforms through the material. The uniform here is the same for every single vertex
- modelViewMatrix is actually two vertices
  - modelMatrix provides the transfrom of our model
  - viewMatrix provides the transform of our camera

**Uniforms Key Characteristics**
- Same for all vertices or fragments: It doesn’t change from pixel to pixel or vertex to vertex during one draw call.
- Read-only: You can use it in your shader, but you can’t change it inside the shader.
- Set by the CPU side: You update it from your main application code, not from within GLSL.


```
attribute vec3 position;

uniform mat4 projectionMatrix;
uniform mat4 modelViewMatrix;

uniform mat4 modelMatrix;
uniform mat4 viewMatrix;

uniform float uTime

void main() {
  // Transform -> position, scale, rotation
  // modelMatrix -> position, scale, rotation of our model
  // viewMatrix -> position, orientation of camera
  // projectionMatrix -> projects our object onto the screen (aspect ratio & the perspective)

  // MVP
  gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(position, 1.0);
}
```

A uniform can be used also in the FragmentShader


```
precision mediump float;
```

---

## UVs And Normals

```
attribute vec3 position;

uniform mat4 projectionMatrix;
uniform mat4 modelViewMatrix;

uniform mat4 modelMatrix;
uniform mat4 viewMatrix;

uniform float uTime

void main() {
  // MVP
  vec4 modelViewPosition = modelViewMatrix * vec4(position, 1.0);
  vec4 projectedPosition = projectionMatrix * modelViewPosition;

  gl_Position = projectedPosition;
}
```

```
console.log(geometry.attributes)
```
- normal
- position
- uv

**Normals**

For each vertex we have a normal, it is the orientation of the vertex at a certain point, on the surface of the object
- It's used for lighting and shading in shaders.
- GLSL uses it as an attribute in the vertex shader and often passes it to the fragment shader for per-pixel lighting.

**UVs**

We use UV coordinates when we want to project a texture on the object
- UVs are coordinates in the 2-D space
- We can map a texture to our object

---

## Varyings

Javascript
- attributes
  - Vertex Specific
  - Only vertex shader can access them
- Uniforms
  - global
  - Both for Vertex and Fragment Shader

The **communication between the vertex shader and the fragment shader** is the key part

Varyings are used to send vertex data from the vertex shader to the fragment shader

**Vertex Shader**

```
uniform float uTime;

varying vec3 vPosition;
varying vec3 vNormal;
varying vec2 vUv;

void main(){
  vPosition = position;
  vNormal = normal;
  vUv = uv;

  //MVP
  vec4 modelViewPosition = modelViewMatrix * vec4(position, 1.0);
  vec4 projectedPosition = projectionMatrix * modelViewPosition;
  gl_Position = projectedPosition;
}

```

**Fragment Shader**
```
uniform float uTime;

varying vec3 vPosition;
varying vec3 vNormal;
varying vec2 vUv;

void main(){
  // gl_FragColor = vec4(vPosition, 1);
  // gl_FragColor = vec4(vUv, 0, 1);

 gl_FragColor = vec4(mix(vec3(0), vec3(1), vUv.x), 1); // interpolation with mix function
}
```

**Flat**

```
flat varying vec2 vUv
```

When you add flat you disable interpolation for that varying

---



