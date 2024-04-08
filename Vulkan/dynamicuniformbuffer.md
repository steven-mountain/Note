因为要跟着vkt做纹理，故将之前写的dynamicUniformBuffer的内容记录在此

+ 首先是声明变量

```c++
// uniform buffer and dynamic uniform buffer
	// buffer map data
	struct UniformBuffer {
		VkBuffer view; //  因为 view 基本是固定的所以不变
		VkBuffer dynamic;
	};
	std::vector<UniformBuffer> dynamicUniformbufffers;
	// memory
	struct UniformMemory {
		VkDeviceMemory viewMemory;
		VkDeviceMemory dyamiceMemory;
	};
	std::vector<UniformMemory> dynamicUniformMemorys;
	// data
	struct UniformMapped {
		void* viewMemoryMaped;
		void* dynamicMemoryMaped;
	};
	std::vector<UniformMapped> dynamicUniformMappeds;

	struct uboVS {
		alignas(16) glm::mat4 view;
		alignas(16) glm::mat4 projection;
	};

	struct UboDataDynamic {
		alignas(8) glm::mat4* model = nullptr;
	};

	std::vector<UboDataDynamic> ubodataDynamics;
	std::vector<glm::vec3> perObjectXY;
	size_t dynamicAlignment;
	VkDescriptorSetLayout dynamicUniformBufferDescriptorSetLayout;
	VkDescriptorPool dynamicUniformBufferDescriptorPool;
	std::vector<VkDescriptorSet> dynamicUniformBufferDescriptorsets;
	VkPipelineLayout dynamicUniformBufferPipelineLayout;
	VkPipeline dynamicUniformBufferPipeline;
```



+ 创建descriptorsetLayout

```c++
void createDynamicUniformBufferDescriptorSetLayout() {
		VkDescriptorSetLayoutBinding uboStaticLayoutBinding{};
		uboStaticLayoutBinding.binding = 0;
		uboStaticLayoutBinding.descriptorCount = 1;
		uboStaticLayoutBinding.descriptorType = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER;
		uboStaticLayoutBinding.pImmutableSamplers = nullptr;
		uboStaticLayoutBinding.stageFlags = VK_SHADER_STAGE_VERTEX_BIT;

		VkDescriptorSetLayoutBinding uboDynamicLayoutBinding{};
		uboDynamicLayoutBinding.binding = 1;
		uboDynamicLayoutBinding.descriptorCount = 1;
		uboDynamicLayoutBinding.descriptorType = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER_DYNAMIC;
		uboDynamicLayoutBinding.pImmutableSamplers = nullptr;
		uboDynamicLayoutBinding.stageFlags = VK_SHADER_STAGE_VERTEX_BIT;

		VkDescriptorSetLayoutBinding layoutBingding[] = { uboStaticLayoutBinding , uboDynamicLayoutBinding };

		VkDescriptorSetLayoutCreateInfo dynamicUniformBufferDescriptorSetLayoutCreateInfo{};
		dynamicUniformBufferDescriptorSetLayoutCreateInfo.sType = VK_STRUCTURE_TYPE_DESCRIPTOR_SET_LAYOUT_CREATE_INFO;
		dynamicUniformBufferDescriptorSetLayoutCreateInfo.bindingCount = 2;
		dynamicUniformBufferDescriptorSetLayoutCreateInfo.pBindings = layoutBingding;
		if (vkCreateDescriptorSetLayout(device, &dynamicUniformBufferDescriptorSetLayoutCreateInfo, nullptr, &dynamicUniformBufferDescriptorSetLayout) != VK_SUCCESS) {
			throw std::runtime_error("failed to create dynamic uniform buffer descriptorsetlayouts");
		}
	}
```

+ 创建pipeline，在pipeline中创建layout，并与descriptorsetLayout绑定

```c++
void createDynamicUniformBufferGraphicsPipeline() {
		// fix stage
		auto vertShaderCode = readFile("circlevert.spv");
		auto fragShaderCode = readFile("circlefrag.spv");

		VkShaderModule vertShaderModule = createShaderModule(vertShaderCode);
		VkShaderModule fragShaderModule = createShaderModule(fragShaderCode);

		VkPipelineShaderStageCreateInfo vertShaderStageInfo{};
		vertShaderStageInfo.sType = VK_STRUCTURE_TYPE_PIPELINE_SHADER_STAGE_CREATE_INFO;
		vertShaderStageInfo.stage = VK_SHADER_STAGE_VERTEX_BIT;
		vertShaderStageInfo.module = vertShaderModule;
		vertShaderStageInfo.pName = "main";

		VkPipelineShaderStageCreateInfo fragShaderStageInfo{};
		fragShaderStageInfo.sType = VK_STRUCTURE_TYPE_PIPELINE_SHADER_STAGE_CREATE_INFO;
		fragShaderStageInfo.stage = VK_SHADER_STAGE_FRAGMENT_BIT;
		fragShaderStageInfo.module = fragShaderModule;
		fragShaderStageInfo.pName = "main";

		VkPipelineShaderStageCreateInfo shaderStages[] = { vertShaderStageInfo, fragShaderStageInfo };

		// fix function
		std::vector<VkDynamicState> dynamicStates = {
			VK_DYNAMIC_STATE_VIEWPORT,
			VK_DYNAMIC_STATE_SCISSOR
		};
		VkPipelineDynamicStateCreateInfo dynamicSateCreateInfo{};
		dynamicSateCreateInfo.sType = VK_STRUCTURE_TYPE_PIPELINE_DYNAMIC_STATE_CREATE_INFO;
		dynamicSateCreateInfo.dynamicStateCount = static_cast<uint32_t>(dynamicStates.size());
		dynamicSateCreateInfo.pDynamicStates = dynamicStates.data();

		VkPipelineVertexInputStateCreateInfo vertexInputCreateInfo{};
		vertexInputCreateInfo.sType = VK_STRUCTURE_TYPE_PIPELINE_VERTEX_INPUT_STATE_CREATE_INFO;
		auto bingDingDescription = CirclePoint::getBindingDescription();
		auto attributeDescription = CirclePoint::getAttributeDescriptions();

		vertexInputCreateInfo.vertexBindingDescriptionCount = 1;
		vertexInputCreateInfo.pVertexBindingDescriptions = &bingDingDescription;
		vertexInputCreateInfo.vertexAttributeDescriptionCount = static_cast<uint32_t>(attributeDescription.size());
		vertexInputCreateInfo.pVertexAttributeDescriptions = attributeDescription.data();

		VkPipelineInputAssemblyStateCreateInfo inputAssembly{};
		inputAssembly.sType = VK_STRUCTURE_TYPE_PIPELINE_INPUT_ASSEMBLY_STATE_CREATE_INFO;
		inputAssembly.topology = VK_PRIMITIVE_TOPOLOGY_TRIANGLE_FAN;
		inputAssembly.primitiveRestartEnable = VK_FALSE;

		VkViewport viewPort{};
		viewPort.x = 0.0f;
		viewPort.y = 0.0f;
		viewPort.width = (float)swapChainExtent.width;
		viewPort.height = (float)swapChainExtent.height;
		viewPort.minDepth = 0.0f;
		viewPort.maxDepth = 1.0f;

		VkRect2D scissor{};
		scissor.extent = swapChainExtent;
		scissor.offset = { 0, 0 };

		VkPipelineViewportStateCreateInfo viewPortState{};
		viewPortState.sType = VK_STRUCTURE_TYPE_PIPELINE_VIEWPORT_STATE_CREATE_INFO;
		viewPortState.viewportCount = 1;
		viewPortState.scissorCount = 1;

		VkPipelineRasterizationStateCreateInfo rasterizer{};
		rasterizer.sType = VK_STRUCTURE_TYPE_PIPELINE_RASTERIZATION_STATE_CREATE_INFO;
		rasterizer.polygonMode = VK_POLYGON_MODE_FILL;
		rasterizer.depthClampEnable = VK_FALSE;
		rasterizer.rasterizerDiscardEnable = VK_FALSE;
		rasterizer.lineWidth = 1.0f;

		rasterizer.cullMode = VK_CULL_MODE_NONE;
		rasterizer.frontFace = VK_FRONT_FACE_CLOCKWISE;

		rasterizer.depthBiasEnable = VK_FALSE;
		rasterizer.depthBiasClamp = 0.0f;
		rasterizer.depthBiasConstantFactor = 0.0f;
		rasterizer.depthBiasSlopeFactor = 0.0f;

		VkPipelineMultisampleStateCreateInfo multisampling{};
		multisampling.sType = VK_STRUCTURE_TYPE_PIPELINE_MULTISAMPLE_STATE_CREATE_INFO;
		multisampling.sampleShadingEnable = VK_FALSE;
		multisampling.rasterizationSamples = VK_SAMPLE_COUNT_1_BIT;
		multisampling.minSampleShading = 1.0f;
		multisampling.pSampleMask = nullptr;
		multisampling.alphaToCoverageEnable = VK_FALSE;
		multisampling.alphaToOneEnable = VK_FALSE;

		VkPipelineColorBlendAttachmentState colorBlendAttachment{};
		colorBlendAttachment.colorWriteMask = VK_COLOR_COMPONENT_R_BIT |
			VK_COLOR_COMPONENT_G_BIT |
			VK_COLOR_COMPONENT_B_BIT |
			VK_COLOR_COMPONENT_A_BIT;
		colorBlendAttachment.blendEnable = VK_FALSE;
		colorBlendAttachment.colorBlendOp = VK_BLEND_OP_ADD;
		colorBlendAttachment.alphaBlendOp = VK_BLEND_OP_ADD;
		colorBlendAttachment.srcColorBlendFactor = VK_BLEND_FACTOR_ONE;
		colorBlendAttachment.dstColorBlendFactor = VK_BLEND_FACTOR_ZERO;
		colorBlendAttachment.srcAlphaBlendFactor = VK_BLEND_FACTOR_ONE;
		colorBlendAttachment.dstAlphaBlendFactor = VK_BLEND_FACTOR_ZERO;

		VkPipelineColorBlendStateCreateInfo colorBlend{};
		colorBlend.sType = VK_STRUCTURE_TYPE_PIPELINE_COLOR_BLEND_STATE_CREATE_INFO;
		colorBlend.logicOpEnable = VK_FALSE;
		colorBlend.logicOp = VK_LOGIC_OP_COPY;
		colorBlend.attachmentCount = 1;
		colorBlend.pAttachments = &colorBlendAttachment;
		colorBlend.blendConstants[0] = 0.0f; // rgba
		colorBlend.blendConstants[1] = 0.0f;
		colorBlend.blendConstants[2] = 0.0f;
		colorBlend.blendConstants[3] = 0.0f;

		VkPipelineLayoutCreateInfo layoutCreateInfo{}; // 主要是这一段
		layoutCreateInfo.sType = VK_STRUCTURE_TYPE_PIPELINE_LAYOUT_CREATE_INFO;
		layoutCreateInfo.setLayoutCount = 1; 
		layoutCreateInfo.pSetLayouts = &dynamicUniformBufferDescriptorSetLayout;
		layoutCreateInfo.pushConstantRangeCount = 0;
		layoutCreateInfo.pPushConstantRanges = nullptr;
		if (vkCreatePipelineLayout(device, &layoutCreateInfo, nullptr, &dynamicUniformBufferPipelineLayout) != VK_SUCCESS) {
			throw std::runtime_error("failed to create dynamic uniform buffer layout");
		}

		VkGraphicsPipelineCreateInfo graphicsPipelineCreateInfo{};
		graphicsPipelineCreateInfo.sType = VK_STRUCTURE_TYPE_GRAPHICS_PIPELINE_CREATE_INFO;
		graphicsPipelineCreateInfo.stageCount = 2;
		graphicsPipelineCreateInfo.pStages = shaderStages;

		graphicsPipelineCreateInfo.pDynamicState = &dynamicSateCreateInfo;
		graphicsPipelineCreateInfo.pVertexInputState = &vertexInputCreateInfo;
		graphicsPipelineCreateInfo.pInputAssemblyState = &inputAssembly;
		graphicsPipelineCreateInfo.pViewportState = &viewPortState;
		graphicsPipelineCreateInfo.pRasterizationState = &rasterizer;
		graphicsPipelineCreateInfo.pMultisampleState = &multisampling;
		graphicsPipelineCreateInfo.pDepthStencilState = nullptr;
		graphicsPipelineCreateInfo.pColorBlendState = &colorBlend;
		graphicsPipelineCreateInfo.layout = dynamicUniformBufferPipelineLayout;

		graphicsPipelineCreateInfo.renderPass = renderPass;
		graphicsPipelineCreateInfo.subpass = 0;

		graphicsPipelineCreateInfo.basePipelineHandle = VK_NULL_HANDLE;
		graphicsPipelineCreateInfo.basePipelineIndex = -1;

		if (vkCreateGraphicsPipelines(device, VK_NULL_HANDLE, 1, &graphicsPipelineCreateInfo, nullptr, &dynamicUniformBufferPipeline) != VK_SUCCESS) {
			throw std::runtime_error("failed to create graphics pipeline");
		}
		vkDestroyShaderModule(device, vertShaderModule, nullptr);
		vkDestroyShaderModule(device, fragShaderModule, nullptr);
	}
```





+ 创建descriptorpool

```c++
void createDynamicUniformBufferDescriptorPool() {
		VkDescriptorPoolSize staticPoolSize{};
		staticPoolSize.type = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER;
		staticPoolSize.descriptorCount = static_cast<uint32_t>(MAX_FRAMES_IN_FLIGHT);
		VkDescriptorPoolSize dynamicPoolSize{};
		dynamicPoolSize.type = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER_DYNAMIC; // 这里poolsize的类型也不一样，是dynamic
		dynamicPoolSize.descriptorCount = static_cast<uint32_t>(MAX_FRAMES_IN_FLIGHT);

		std::vector<VkDescriptorPoolSize> poolSizes = { staticPoolSize , dynamicPoolSize };

		VkDescriptorPoolCreateInfo descriptorPoolCreateInfo{};
		descriptorPoolCreateInfo.sType = VK_STRUCTURE_TYPE_DESCRIPTOR_POOL_CREATE_INFO;
		descriptorPoolCreateInfo.poolSizeCount = poolSizes.size();
		descriptorPoolCreateInfo.pPoolSizes = poolSizes.data();
		descriptorPoolCreateInfo.maxSets = 2 * static_cast<uint32_t>(MAX_FRAMES_IN_FLIGHT); // 这是池子里最大的数？
		if (vkCreateDescriptorPool(device, &descriptorPoolCreateInfo, nullptr, &dynamicUniformBufferDescriptorPool) != VK_SUCCESS) {
			throw std::runtime_error("failed to create descriptorpool");
		}
	}
```



+ 创建buffer，和memory并将两者绑定，再将memory与cpu端相映射

```c++
	void createDynamicAndStaticUniformBuffers() {
		//dynamic 获取偏移量
		VkPhysicalDeviceProperties properties;
		vkGetPhysicalDeviceProperties(physicalDevice, &properties);
		size_t minUboAlignment = properties.limits.minUniformBufferOffsetAlignment;
		dynamicAlignment = sizeof(glm::mat4);
		if (minUboAlignment > 0) {
			dynamicAlignment = (dynamicAlignment + minUboAlignment - 1) & ~(minUboAlignment - 1); // (a+b-1)&~(b-1)
		}
		VkDeviceSize bufferSizeDynamic = OBJECT_INSTANCES * dynamicAlignment;
		VkDeviceSize bufferSizeStatic = sizeof(uboVS);

		dynamicUniformbufffers.resize(MAX_FRAMES_IN_FLIGHT);
		dynamicUniformMappeds.resize(MAX_FRAMES_IN_FLIGHT);
		dynamicUniformMemorys.resize(MAX_FRAMES_IN_FLIGHT);
		ubodataDynamics.resize(MAX_FRAMES_IN_FLIGHT);

		for (uint32_t i = 0; i < MAX_FRAMES_IN_FLIGHT; ++i) {
			ubodataDynamics[i].model = (glm::mat4*)alignedAlloc(bufferSizeDynamic, dynamicAlignment);
			createBuffer(dynamicUniformbufffers[i].view, dynamicUniformMemorys[i].viewMemory, bufferSizeStatic, VK_BUFFER_USAGE_UNIFORM_BUFFER_BIT, VK_MEMORY_PROPERTY_HOST_VISIBLE_BIT | VK_MEMORY_PROPERTY_HOST_COHERENT_BIT);
			vkMapMemory(device, dynamicUniformMemorys[i].viewMemory, 0, bufferSizeStatic, 0, &dynamicUniformMappeds[i].viewMemoryMaped);
			createBuffer(dynamicUniformbufffers[i].dynamic, dynamicUniformMemorys[i].dyamiceMemory, bufferSizeDynamic, VK_BUFFER_USAGE_UNIFORM_BUFFER_BIT, VK_MEMORY_PROPERTY_HOST_VISIBLE_BIT | VK_MEMORY_PROPERTY_HOST_COHERENT_BIT);
			vkMapMemory(device, dynamicUniformMemorys[i].dyamiceMemory, 0, bufferSizeDynamic, 0, &dynamicUniformMappeds[i].dynamicMemoryMaped); // 这儿有问题提
		}
		updateStaticUniformBuffersData(VK_TRUE);
		updateDynamicUniformBuffersData(VK_TRUE);
	}
```

求最小对齐（2的倍数）

```c++
(a+b-1)&~(b-1)
```

求出b的倍数中a的最小上界，b一定要是2的幂

+ 从pool中组装descriptorset，每一framebuffer都有一个descriptorSetLayout

```c++
void createDynamicUniformBufferDescriptorsets() {
    // 分配descriptorsets, 有多少个set就有多少个setLayout，两者要一一对应
		std::vector<VkDescriptorSetLayout> layouts(MAX_FRAMES_IN_FLIGHT, dynamicUniformBufferDescriptorSetLayout);
		VkDescriptorSetAllocateInfo setAllocateInfo{};
		setAllocateInfo.sType = VK_STRUCTURE_TYPE_DESCRIPTOR_SET_ALLOCATE_INFO;
		setAllocateInfo.descriptorPool = dynamicUniformBufferDescriptorPool;
		setAllocateInfo.descriptorSetCount = static_cast<uint32_t>(MAX_FRAMES_IN_FLIGHT);
		setAllocateInfo.pSetLayouts = layouts.data();
		dynamicUniformBufferDescriptorsets.resize(MAX_FRAMES_IN_FLIGHT);
		if (vkAllocateDescriptorSets(device, &setAllocateInfo, dynamicUniformBufferDescriptorsets.data()) != VK_SUCCESS) {
			throw std::runtime_error("failed to create dynamic uniform buffer descriptor sets");
		}

    // 根据WriteDescriptorSet，向descriptorset中写入信息，其实就是指针，让descriptorset的某一binding上的descriptor指向我们的uniformBuffer（dynamicUniformBuffer）
		for (uint32_t i = 0; i < MAX_FRAMES_IN_FLIGHT; ++i) {
			VkDescriptorBufferInfo bufferInfo = {};
			bufferInfo.buffer = dynamicUniformbufffers[i].view;
			bufferInfo.offset = 0;
			bufferInfo.range = sizeof(uboVS);

			VkDescriptorBufferInfo dynamicBufferInfo = {};
			dynamicBufferInfo.buffer = dynamicUniformbufffers[i].dynamic;
			dynamicBufferInfo.offset = 0;
			dynamicBufferInfo.range = dynamicAlignment;

			VkWriteDescriptorSet staticWriteDescriptor{};
			staticWriteDescriptor.sType = VK_STRUCTURE_TYPE_WRITE_DESCRIPTOR_SET;
			staticWriteDescriptor.dstSet = dynamicUniformBufferDescriptorsets[i];
			staticWriteDescriptor.dstBinding = 0;
			staticWriteDescriptor.dstArrayElement = 0;
			staticWriteDescriptor.descriptorType = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER;
			staticWriteDescriptor.descriptorCount = 1;
			staticWriteDescriptor.pBufferInfo = &bufferInfo;
			staticWriteDescriptor.pImageInfo = nullptr;
			staticWriteDescriptor.pTexelBufferView = nullptr;

			VkWriteDescriptorSet dynamicWriteDescriptor{};
			dynamicWriteDescriptor.sType = VK_STRUCTURE_TYPE_WRITE_DESCRIPTOR_SET;
			dynamicWriteDescriptor.dstSet = dynamicUniformBufferDescriptorsets[i];
			dynamicWriteDescriptor.dstBinding = 1;
			dynamicWriteDescriptor.dstArrayElement = 0;
			dynamicWriteDescriptor.descriptorType = VK_DESCRIPTOR_TYPE_UNIFORM_BUFFER_DYNAMIC;
			dynamicWriteDescriptor.descriptorCount = 1;
			dynamicWriteDescriptor.pBufferInfo = &dynamicBufferInfo;
			dynamicWriteDescriptor.pTexelBufferView = nullptr;
			std::vector<VkWriteDescriptorSet> writeDescriptorsets = { staticWriteDescriptor , dynamicWriteDescriptor };
			vkUpdateDescriptorSets(device, 2, writeDescriptorsets.data(), 0, nullptr); // 填充buffer内容 我是傻逼 这儿肯定是2啊
		}
	}
```

+ 使用，在recordcommand里，绑定我们的descriptor

```c++
void recordCommandBuffer(VkCommandBuffer commandBuffer, uint32_t imageIndex) {
		VkCommandBufferBeginInfo commandBufferBeginInfo{};
		commandBufferBeginInfo.sType = VK_STRUCTURE_TYPE_COMMAND_BUFFER_BEGIN_INFO;
		commandBufferBeginInfo.flags = 0;
		commandBufferBeginInfo.pInheritanceInfo = nullptr;
		if (vkBeginCommandBuffer(commandBuffer, &commandBufferBeginInfo) != VK_SUCCESS) {
			throw std::runtime_error("failed to begin command buffer");
		}

		VkClearValue clearColor = { {{0.0f, 0.0f, 1.0f, 1.0f}} };
		VkRenderPassBeginInfo renderPassBeginInfo{};
		renderPassBeginInfo.sType = VK_STRUCTURE_TYPE_RENDER_PASS_BEGIN_INFO;
		renderPassBeginInfo.clearValueCount = 1;
		renderPassBeginInfo.pClearValues = &clearColor;
		renderPassBeginInfo.framebuffer = swapchainFramebuffer[imageIndex];
		renderPassBeginInfo.renderArea.offset = {0, 0};
		renderPassBeginInfo.renderArea.extent = swapChainExtent;
		renderPassBeginInfo.renderPass = renderPass;
		vkCmdBeginRenderPass(commandBuffer, &renderPassBeginInfo, VK_SUBPASS_CONTENTS_INLINE);
    
		vkCmdBindPipeline(commandBuffer, VK_PIPELINE_BIND_POINT_GRAPHICS, dynamicUniformBufferPipeline);

		VkViewport viewPort{};
		viewPort.x = 0.0f;
		viewPort.y = 0.0f;
		viewPort.width = static_cast<uint32_t>(swapChainExtent.width);
		viewPort.height = static_cast<uint32_t>(swapChainExtent.height);
		viewPort.minDepth = 0.0f;
		viewPort.maxDepth = 1.0f;
		vkCmdSetViewport(commandBuffer, 0, 1, &viewPort);

		VkRect2D scissor{};
		scissor.offset = {0, 0}; // int32_t
		scissor.extent = swapChainExtent;
		vkCmdSetScissor(commandBuffer, 0, 1, &scissor);
    
		VkBuffer vertexBuffers[] = { circleBuffer };
		VkDeviceSize offsets[] = { 0 };
		vkCmdBindVertexBuffers(commandBuffer, 0, 1, vertexBuffers, offsets);
		for (uint32_t j = 0; j < OBJECT_INSTANCES; ++j) {
			uint32_t dynamicOffset = j * static_cast<uint32_t>(dynamicAlignment);
			vkCmdBindDescriptorSets(commandBuffer, VK_PIPELINE_BIND_POINT_GRAPHICS, dynamicUniformBufferPipelineLayout, 0, 1, &dynamicUniformBufferDescriptorsets[currentFrame], 1, &dynamicOffset); //主要是这一段
			vkCmdDraw(commandBuffer, static_cast<uint32_t>(circlePointSet.size()), 1, 0, 0);
		}
		
		vkCmdEndRenderPass(commandBuffer);
		if (vkEndCommandBuffer(commandBuffer) != VK_SUCCESS) {
			throw std::runtime_error("failed to record command buffer");
		}
	}
```

+ **重点（敲黑板！！！）**

一个bindingpoint上可以是一个descriptor Array，所以在我们的shader中应该是这样的

```glsl
#version 450
layout(location = 0) in vec2 inPosition;
layout(binding = 0) uniform UniformBufferObject{
    mat4 view;
    mat4 projection;
}ubo[]; // 一个binding point上可以是一个 descriptor array

layout(binding = 1) uniform UboInstance{ // 当你在 Uniform 缓冲区定义中使用 [] 时，这表示你正在定义一个数组。
    mat4 model;
}uboInstance[];

void main() {
    gl_Position = ubo[0].projection * ubo[0].view * uboInstance[0].model * vec4(inPosition, 0.0, 1.0);
//    gl_Position = vec4(inPosition, 0.0, 1.0);
}
```



+ 自此我们可以得出以下的对应关系

创建descriptorSetLayout相当声明了一个类型，例如int*

创建descriptorPool相当于创建了一个内存池

创建descriptorSets相当于创建了一个变量 int* a

调用vkUpdateDescriptorSets相当于将指针指向所需要的资源， int* a = b(资源)

这样就建立了对资源的连接。在使用的时候绑定就行了。

