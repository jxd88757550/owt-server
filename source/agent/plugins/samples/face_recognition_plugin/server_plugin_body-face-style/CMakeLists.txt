# Copyright (C) 2018 Intel Corporation
# SPDX-License-Identifier: Apache-2.0
#

cmake_minimum_required (VERSION 2.8)

set(CMAKE_SKIP_RPATH true)

set(TARGET_NAME "myplugin2")

set(CMAKE_SKIP_RPATH true)

# Properties->C/C++->General->Additional Include Directories
include_directories (
    ${InferenceEngine_INCLUDE_DIRS}
)

# Find OpenCV components if exist
find_package(OpenCV COMPONENTS highgui imgproc imgcodecs core QUIET)
if(NOT(OpenCV_FOUND))
    message(WARNING "OPENCV is disabled or not found, " ${TARGET_NAME} " skipped")
    return()
endif()

# Properties->C/C++->General->Additional Include Directories
include_directories (
    ${InferenceEngine_INCLUDE_DIRS}
)

file (GLOB MAIN_SRC
        ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp
        )

file (GLOB MAIN_HEADERS
        ${CMAKE_CURRENT_SOURCE_DIR}/*.h
        )

source_group("src" FILES ${MAIN_SRC})
source_group("include" FILES ${MAIN_HEADERS})

aux_source_directory("../src" DIR_SRCS)

#Ana server side 
include_directories ("../../../include")
include_directories ("../../../")
include_directories("../src")

add_library(${TARGET_NAME} SHARED main.cpp ${MAIN_HEADERS} ${DIR_SRCS})


set_target_properties(${TARGET_NAME} PROPERTIES "CMAKE_CXX_FLAGS" "${CMAKE_CXX_FLAGS} -fPIE"
COMPILE_PDB_NAME ${TARGET_NAME})


target_link_libraries(${TARGET_NAME}  ${InferenceEngine_LIBRARIES} IE::ie_cpu_extension)
target_link_libraries(${TARGET_NAME} -ldl -fPIC -std=gnu++11)

if(UNIX)
    target_link_libraries( ${TARGET_NAME} ${LIB_DL} pthread ${OpenCV_LIBRARIES} )
endif()
