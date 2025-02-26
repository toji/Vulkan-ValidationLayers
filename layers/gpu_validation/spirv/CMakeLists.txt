# ~~~
# Copyright (c) 2024 LunarG, Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# ~~~

add_library(gpu_av_spirv STATIC)
target_sources(gpu_av_spirv PRIVATE
    # Passes
    bindless_descriptor_pass.h
    bindless_descriptor_pass.cpp
    buffer_device_address_pass.h
    buffer_device_address_pass.cpp

    # Framework
    instruction.h
    instruction.cpp
    function_basic_block.h
    function_basic_block.cpp
    link.h
    module.h
    module.cpp
    type_manager.h
    type_manager.cpp
    pass.h
    pass.cpp
    ${VVL_SOURCE_DIR}/layers/${API_TYPE}/generated/spirv_grammar_helper.cpp
)

target_include_directories(gpu_av_spirv PRIVATE .
    ${VVL_SOURCE_DIR}/layers
    ${VVL_SOURCE_DIR}/layers/${API_TYPE}
)

target_link_libraries(gpu_av_spirv PRIVATE
    SPIRV-Headers::SPIRV-Headers
    VkLayer_utils # To pull in compile_definitions
)

if(${CMAKE_CXX_COMPILER_ID} MATCHES "Clang")
    target_compile_options(gpu_av_spirv PRIVATE
        -Wno-sign-conversion
        -Wno-implicit-int-conversion
    )
elseif(MSVC)
    target_compile_options(gpu_av_spirv PRIVATE
        /wd4324 # padding
        /wd4458 # hiding class member
        /wd4457 # hiding function parameter
        /wd4702 # unreachable code
        /wd4389 # signed/unsigned mismatch
    )
endif()