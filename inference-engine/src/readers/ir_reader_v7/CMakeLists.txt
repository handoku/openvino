# Copyright (C) 2018-2019 Intel Corporation
# SPDX-License-Identifier: Apache-2.0
#

set(TARGET_NAME "inference_engine_ir_v7_reader")

if(ENABLE_LTO)
    ie_enable_lto()
endif()

set(PUBLIC_HEADERS_DIR "${CMAKE_CURRENT_SOURCE_DIR}/")

file(GLOB_RECURSE LIBRARY_SRC ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)
list(APPEND LIBRARY_SRC ${IE_MAIN_SOURCE_DIR}/src/readers/ir_reader/ie_ir_reader.cpp)
file(GLOB_RECURSE PUBLIC_HEADERS ${PUBLIC_HEADERS_DIR}/*.h ${PUBLIC_HEADERS_DIR}/*.hpp)

# Create named folders for the sources within the .vcproj
# Empty name lists them directly under the .vcproj

source_group("src" FILES ${LIBRARY_SRC})
source_group("include" FILES ${PUBLIC_HEADERS})

# Create shared library

add_library(${TARGET_NAME} SHARED ${LIBRARY_SRC} ${PUBLIC_HEADERS})

target_compile_definitions(${TARGET_NAME} PRIVATE IMPLEMENT_INFERENCE_ENGINE_API
                                                  IMPLEMENT_INFERENCE_ENGINE_PLUGIN)

target_include_directories(${TARGET_NAME} PUBLIC ${PUBLIC_HEADERS_DIR})
target_include_directories(${TARGET_NAME} PRIVATE "${IE_MAIN_SOURCE_DIR}/src/inference_engine"
                                                  "${IE_MAIN_SOURCE_DIR}/src/readers/ir_reader")

target_link_libraries(${TARGET_NAME} PUBLIC inference_engine_reader_api inference_engine_plugin_api ${NGRAPH_LIBRARIES} inference_engine)
target_link_libraries(${TARGET_NAME} PRIVATE pugixml)

# code style

add_cpplint_target(${TARGET_NAME}_cpplint FOR_TARGETS ${TARGET_NAME})
