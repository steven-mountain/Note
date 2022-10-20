### Logical device and queues

+ logical device to interface with physical device
+ `specify queues` ->`device feature` ->`create logical device`
+ queues priorities: influence the scheduling of command buffer execution using float point numbers `[0.0, 1.0]` 
+ `VkDeviceCreateInfo` up-to-date implementations ignored `enabledLayerCount` and`ppEnableLayerNames`
+ logical device don't interact directly with instances



#### queue handles

+ queues are automatically created along with the logical device

+ as a class member
+ device queues are implicitly cleaned up when the device is destroyed
+ `vkGetDeviceQueue` retrieve  queue handles for each queue family

