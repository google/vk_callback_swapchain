# Copyright (C) 2017 Google Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

project(VkCallbackSwapchain)
cmake_minimum_required(VERSION 3.1)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if (NOT VULKAN_INCLUDE_LOCATION)
    set(VULKAN_INCLUDE_LOCATION "")
endif()

set(VULKAN_INCLUDE_LOCATION ${VULKAN_INCLUDE_LOCATION}
    CACHE STRING "Location of the Vulkan headers")

if (NOT MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-rtti -fno-exceptions")
endif()

add_library(VkLayer_CallbackSwapchain
  SHARED
    layer.h
    layer.cpp
    swapchain.h
    swapchain.cpp
    threading.h
    callback_swapchain.h
    callback_swapchain.cpp)

if(NOT "${VULKAN_INCLUDE_LOCATION}" STREQUAL "")
    target_include_directories(VkLayer_CallbackSwapchain
        PRIVATE
        ${VULKAN_INCLUDE_LOCATION})
endif()

set(SWAPCHAIN_LIBRARY_NAME "${CMAKE_SHARED_LIBRARY_PREFIX}VkLayer_CallbackSwapchain${CMAKE_SHARED_LIBRARY_SUFFIX}")
set(output_dir ${CMAKE_CURRENT_BINARY_DIR})
set(output_file CallbackSwapchainLayer.json)
if (CMAKE_LIBRARY_OUTPUT_DIRECTORY)
    set(output_dir ${CMAKE_LIBRARY_OUTPUT_DIRECTORY})
endif()
configure_file(CallbackSwapchainLayer.json
    ${output_dir}/${output_file})
if (CMAKE_CFG_INTDIR)
    add_custom_command(TARGET VkLayer_CallbackSwapchain
	    POST_BUILD
	    COMMAND ${CMAKE_COMMAND} -E copy ${output_dir}/${output_file}
	        ${output_dir}/${CMAKE_CFG_INTDIR}/${output_file})
endif()
