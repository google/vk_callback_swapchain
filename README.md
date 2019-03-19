# Vulkan Callback Swapchain

Vulkan Callback Swapchain is a Vulkan
[layer](https://github.com/KhronosGroup/Vulkan-LoaderAndValidationLayers/blob/master/loader/LoaderAndLayerInterface.md).

This is not an official Google product (experimental or otherwise), it is just
code that happens to be owned by Google. See the
[CONTRIBUTING.md](CONTRIBUTING.md) file for more information. See also the
[AUTHORS](AUTHORS) and [CONTRIBUTORS](CONTRIBUTORS) files.

If an application inserts this layer, either by explicitly enabling this
layer, or through environment variables, the default swapchain mechanism is
replaced. All of the existing platform-specific swapchain and surface calls
remain valid. However instead of creating a swapchain that
renders to the given native surface, a virtual swapchain will be created.

The following calls are intercepted.
```
vkGetPhysicalDeviceSurfaceSupportKHR
vkGetPhysicalDeviceSurfaceCapabilitiesKHR
vkGetPhysicalDeviceSurfaceFormatsKHR
vkGetPhysicalDeviceSurfacePresentModesKHR
vkCreateSwapchainKHR
vkDestroySwapchainKHR
vkGetSwapchainImagesKHR
vkAcquireNextImageKHR
vkQueuePresentKHR
vkDestroySurfaceKHR
vkCreateAndroidSurfaceKHR
vkCreateMirSurfaceKHR
vkCreateWaylandSurfaceKHR
vkCreateWin32SurfaceKHR
vkCreateXcbSurfaceKHR
vkCreateXlibSurfaceKHR
```

It follows that a Valid native surface is not required.
Any window/module/connection parameters are ignored. This can be used for
example to allow an application that typically renders to a window to
be run without a window.

The following method is additionally exported by the layer.
```c++
VKAPI_ATTR void VKAPI_CALL
vkSetSwapchainCallback(VkSwapchainKHR swapchain,
                    void callback(void*, uint8_t*, size_t), void* user_data);
```

This function allows the user to set a callback function to be called when a
swapchain image has been scheduled for presentation. The parameters
to the provided callback are the originally supplied user_data, a
pointer to the bytes that would have been output, and the number of bytes
provided.

# Building
This project is a standard CMake project.
Example using [ninja](https://ninja-build.org/):

```
mkdir build
cd build
cmake -GNinja ..
cmake --build .
```

If the Vulkan headers are not located in a standard location, they can be
specified by setting the CMake variable `VULKAN_INCLUDE_LOCATION`.
