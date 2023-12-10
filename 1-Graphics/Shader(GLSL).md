# Shader(GLSL)
本章主要记录在Shader(GLSL)内的一些关键字和数据的输入和输出:</br>

### Q1 Shader内的Binding 和 Location 这两个传入数据的方式，分别是传入什么类型的数据?

Binding：</br>
Binding 主要传入Buffer(分成Uniform Buffer和 storage buffer)和Sample Image这两类，传入的资源参与shader的颜色值计算。

```cpp
layout(set = 0, binding = 1) readonly buffer _unused_name_per_drawcall
{
    VulkanMeshInstance mesh_instances[m_mesh_per_drawcall_max_instance_count];
};
```

Location: </br>
Location 主要传入顶点的信息和数据，如位置、颜色、法线等。

```cpp
layout(location = 0) in vec3 in_position; // for some types as dvec3 takes 2 locations
layout(location = 1) in vec3 in_normal;
layout(location = 2) in vec3 in_tangent;
layout(location = 3) in vec2 in_texcoord;
```

### Q2 在openGL和vulkan内， Binding和Location的数据分别是怎么传入shader的?

在 OpenGL 中：

Binding 的数据传递方式：

1-对于 Uniform Buffer Objects (UBO) 或 Shader Storage Buffer Objects (SSBO)：可以使用 glUniformBlockBinding 函数将 Uniform Buffer 或 Shader Storage Buffer 绑定到指定的 Binding 点。</br>
2-对于纹理和图像：可以使用 glBindTextureUnit 或 glBindImageTexture 函数将纹理或图像绑定到指定的纹理单元或图像单元。</br>

```cpp
// 在 OpenGL 中设置 Uniform Buffer 的 Binding 点
GLuint bindingPoint = 0; // 绑定点的整数值
GLuint uniformBlockIndex = glGetUniformBlockIndex(shaderProgram, "uniformBuffer");
glUniformBlockBinding(shaderProgram, uniformBlockIndex, bindingPoint);
```

示例代码（纹理）：
```cpp
// 在 OpenGL 中设置纹理的 Binding 点
GLuint bindingPoint = 0; // 绑定点的整数值
glBindTextureUnit(bindingPoint, texture);
```

Location 的数据传递方式：

对于顶点着色器的顶点属性：可以使用 glVertexAttribPointer 函数指定顶点属性的位置和数据格式，然后使用 glEnableVertexAttribArray 函数启用顶点属性数组。

```cpp
// 在 OpenGL 中设置顶点属性的 Location
GLuint positionLocation = glGetAttribLocation(shaderProgram, "position");
glEnableVertexAttribArray(positionLocation);
glVertexAttribPointer(positionLocation, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)0);
```
或者使用VAO;

```cpp
// 创建和绑定VAO
GLuint vao;
glGenVertexArrays(1, &vao);
glBindVertexArray(vao);

// 创建和绑定VBO（顶点缓冲对象）
GLuint vbo;
glGenBuffers(1, &vbo);
glBindBuffer(GL_ARRAY_BUFFER, vbo);

// 设置顶点数据
// ...

// 设置顶点属性指针
// 例如，设置位置属性的Location为0，偏移量为0
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)0);
glEnableVertexAttribArray(0);

// 解绑VBO和VAO
glBindBuffer(GL_ARRAY_BUFFER, 0);
glBindVertexArray(0);
```

在 Vulkan 中：

Binding 的数据传递方式：

1-对于 Uniform Buffer Objects (UBO) 或 Storage Buffer Objects (SSBO)：在 Vulkan 的描述符集布局中，可以为 Uniform Buffer 或 Storage Buffer 分配一个 Binding 点，然后在描述符集中绑定对应的缓冲。
2-对于纹理和图像：在 Vulkan 的描述符集布局中，可以为纹理或图像分配一个 Binding 点，然后在描述符集中绑定对应的图像视图或采样器。

示例代码（Uniform Buffer Object）
```cpp
// 在 Vulkan 中设置 Uniform Buffer 的 Binding 点
VkDescriptorSetLayoutBinding layoutBinding = {};
layoutBinding.binding = 0; // 绑定点的整数值
layoutBinding.descriptorType = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER;
layoutBinding.descriptorCount = 1;
layoutBinding.stageFlags = VK_SHADER_STAGE_VERTEX_BIT;

// 然后在描述符集中绑定对应的缓冲
VkWriteDescriptorSet descriptorWrite = {};
descriptorWrite.dstSet = descriptorSet;
descriptorWrite.dstBinding = 0; // 绑定点的整数值
descriptorWrite.descriptorType = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER;
descriptorWrite.descriptorCount = 1;
descriptorWrite.pBufferInfo = &bufferInfo;
vkUpdateDescriptorSets(device, 1, &descriptorWrite, 0, nullptr);
```

示例代码（图像）：
```cpp
// 在 Vulkan 中设置图像的 Binding 点
VkDescriptorSetLayoutBinding layoutBinding = {};
layoutBinding.binding = 0; // 绑定点的整数值
layoutBinding.descriptorType = VK_DESCRIPTOR_TYPE_SAMPLED_IMAGE;
layoutBinding.descriptorCount = 1;
layoutBinding.stageFlags = VK_SHADER_STAGE_FRAGMENT_BIT;

// 然后在描述符集中绑定对应的图像视图或采样器
VkWriteDescriptorSet descriptorWrite = {};
descriptorWrite.dstSet = descriptorSet;
descriptorWrite.dstBinding = 0; // 绑定点的整数值
descriptorWrite.descriptorType = VK_DESCRIPTOR_TYPE_SAMPLED_IMAGE;
descriptorWrite.descriptorCount = 1;
descriptorWrite.pImageInfo = &imageInfo;
vkUpdateDescriptorSets(device, 1, &descriptorWrite, 0, nullptr);
```

Location 的数据传递方式：

在 Vulkan 中，顶点属性的位置是在顶点着色器的 SPIR-V 代码中定义的。在加载和创建管线布局时，可以查询顶点着色器中的顶点属性定义，并将其与 Vulkan 的管线布局相关联。</br>
主要是在Vertex Input State

```cpp
// 创建顶点输入绑定描述符
VkVertexInputBindingDescription bindingDescription = {};
bindingDescription.binding = 0; // Binding 点的整数值
bindingDescription.stride = sizeof(Vertex);
bindingDescription.inputRate = VK_VERTEX_INPUT_RATE_VERTEX;

// 创建顶点输入属性描述符
VkVertexInputAttributeDescription attributeDescription = {};
attributeDescription.location = 0; // 顶点属性的位置
attributeDescription.binding = 0; // Binding 点的整数值
attributeDescription.format = VK_FORMAT_R32G32B32_SFLOAT;
attributeDescription.offset = offsetof(Vertex, position);

// 将顶点输入绑定描述符和顶点输入属性描述符添加到管线布局中
VkPipelineVertexInputStateCreateInfo vertexInputInfo = {};
vertexInputInfo.sType = VK_STRUCTURE_TYPE_PIPELINE_VERTEX_INPUT_STATE_CREATE_INFO;
vertexInputInfo.vertexBindingDescriptionCount = 1;
vertexInputInfo.pVertexBindingDescriptions = &bindingDescription;
vertexInputInfo.vertexAttributeDescriptionCount = 1;
vertexInputInfo.pVertexAttributeDescriptions = &attributeDescription;
```

### Q3 Layout关键字
layout 关键字用于指定一些与输入和输出相关的属性。这些属性可以控制数据在着色器之间的传递方式、内存布局等。

1-输入和输出变量位置的指定：</br>
在着色器中，可以使用 layout(location = n) 来指定输入和输出变量的位置，其中 n 是一个整数值。

```cpp
layout(location = 0) in vec3 position;  // 输入位置属性的位置为 0
layout(location = 1) in vec3 normal;    // 输入法线属性的位置为 1

// ...
```

2-片段着色器中的颜色输出：</br>
在片段着色器中，可以使用 layout(location = n) out 来指定输出的颜色缓冲区的位置。

```cpp
layout(location = 0) out vec4 fragColor;  // 输出颜色缓冲区的位置为 0

void main() {
    // 设置输出颜色
    fragColor = vec4(1.0, 0.0, 0.0, 1.0);
}
```

3-顶点属性的分量顺序：</br>
在顶点着色器中，可以使用 layout(location = n, component = c) 来指定顶点属性的分量顺序。

示例代码（顶点着色器）：

```cpp
layout(location = 0) in vec3 position;                    // 输入位置属性的位置为 0，默认分量顺序为 xyz
layout(location = 1, component = yzw) in vec4 color;       // 输入颜色属性的位置为 1，分量顺序为 yzw

// ...
```

4-Uniform 块的绑定点：
在顶点或片段着色器中，可以使用 layout(binding = n) uniform 来指定 Uniform 块的绑定点。

示例代码（顶点着色器）：

```cpp
layout(binding = 0) uniform MVPBlock {
    mat4 model;
    mat4 view;
    mat4 projection;
} MVP;

// ...
```


### Q4 在Vertex Shader内in和out的的location都是需要相互对应的

如下:
```cpp
layout(location = 0) in_position; // for some types as dvec3 takes 2 locations
layout(location = 1) in vec3 in_normal;
layout(location = 2) in vec3 in_tangent;
layout(location = 3) in vec2 in_texcoord;

layout(location = 0) out vec3 out_world_position; // output in framebuffer 0 for fragment shader
layout(location = 1) out vec3 out_normal;
layout(location = 2) out vec3 out_tangent;
layout(location = 3) out vec2 out_texcoord;
```

in 和 out 变量的位置（location）需要相互对应。在顶点着色器中，你可以使用 layout(location = n) 来指定输入（in）和输出（out）变量的位置，其中 n 是一个整数值。

在你提供的示例代码中，输入变量和输出变量都使用了 layout(location) 来指定它们的位置。这意味着输入变量和输出变量需要按照相同的位置进行对应。

例如，输入变量 in_position 使用了 layout(location = 0)，那么对应的输出变量 out_world_position 也需要使用 layout(location = 0)。这样，顶点着色器中的位置数据就能正确地传递给片段着色器，并存储在帧缓冲区的相应位置。

这种位置对应的方式确保了输入和输出变量之间的数据传递是正确的，以便进行后续的渲染操作。

要是数据的Location不对应，就会出现数据传递错误或渲染结果不正确。

### Q5 Varying

在着色器程序中，varying 关键字用于指定在顶点着色器和片段着色器之间进行数据传递的变量。它允许在顶点着色器中计算出的数据在片段着色器中进行插值或者传递给片段着色器使用。

以下是 varying 的一般用法：

在顶点着色器中声明 varying 变量：
在顶点着色器中，你可以使用 varying 关键字来声明一个变量，该变量的值将会在顶点着色器和片段着色器之间进行插值或传递。

示例代码（顶点着色器）：

```cpp
varying vec3 interpolatedColor;

void main() {
    // 计算顶点颜色
    vec3 vertexColor = vec3(1.0, 0.0, 0.0);

    // 传递颜色给片段着色器
    interpolatedColor = vertexColor;

    // ...
}
```

在片段着色器中使用 varying 变量：
在片段着色器中，你可以使用与顶点着色器相同名称的 varying 变量来接收从顶点着色器传递过来的插值或数据。

示例代码（片段着色器）：

```cpp
varying vec3 interpolatedColor;

void main() {
    // 使用插值后的颜色进行片段着色
    vec3 fragmentColor = interpolatedColor;

    // ...
}
```
通过使用 varying 变量，你可以在顶点着色器和片段着色器之间传递数据，并在片段着色器中进行插值或使用。这对于在不同阶段之间共享数据以进行渲染非常有用，例如从顶点着色器传递顶点颜色或法线数据到片段着色器进行光照计算。

需要注意的是，varying 关键字在较新版本的着色器语言中已经被弃用，取而代之的是 in 和 out 关键字的使用。在较新的 GLSL 版本中，可以使用 in 和 out 关键字在顶点着色器和片段着色器之间传递数据。

要是使用in和out的话, 需要搭配location来使用；

