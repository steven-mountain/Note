+ VkMemory is just a sequence of N bytes in memory.
+ VkImage object adds to it e.g. information about the format (so you can address by texels, not bytes).
+ VkImageView object helps select only part (array or mip) of the VkImage (like stringView, arrayView or whathaveyou does). Also can help to match to some incompatible interface (by type casting format).
+ VkFramebuffer binds a VkImageView with an attachment.
+ VkRenderpass defines which attachment will be drawn into
Swapchain image is no different from any other image. Except that the driver is the owner of the image. You can’t destroy it directly or allocate it yourself. You just borrow it from the driver for the duration between acquire and present operation.
