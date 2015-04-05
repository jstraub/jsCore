# Copyright (c) 2015, Julian Straub <jstraub@csail.mit.edu>
# Licensed under the MIT license. See the license file LICENSE.

project(jsCore)
cmake_minimum_required(VERSION 2.8.1)
set(POD_NAME jsCore)
include(cmake/pods.cmake)

if(COMMAND cmake_policy)
  cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)


set(CMAKE_COLOR_MAKEFILE ON)
IF(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Debug) # Release; debug; ...
  set(CMAKE_BUILD_TYPE Release) # Release; debug; ...
ENDIF()

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set (CMAKE_INSTALL_PREFIX "${CMAKE_BINARY_DIR}/install" CACHE PATH "default install path" FORCE )
endif()

find_package (Eigen3 REQUIRED)
find_package (CUDA REQUIRED)

# add flags depending on build type
set(CMAKE_CXX_FLAGS_RELEASE "-g -Wall -Wno-deprecated -O2 -finline-functions -DNDEBUG")
#set(CMAKE_CXX_FLAGS_RELEASE "-g -Wall -Wno-deprecated -O2 -finline-functions")
set(CMAKE_CXX_FLAGS_DEBUG "-g -Wno-deprecated")
set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};-gencode arch=compute_20,code=sm_20 -gencode arch=compute_30,code=sm_30 -gencode arch=compute_35,code=\"sm_35,compute_35\")

include_directories(
  include
  #./common/inc
  ${EIGEN3_INCLUDE_DIR}
  )

cuda_add_library(jsCore SHARED
  ./cuda/vectorSum_kernel.cu
  ./cuda/labelMap_kernel.cu
  ./cuda/copy_kernel.cu
  ./cuda/choiceMult_kernel.cu
  )

cuda_add_executable(testReduction
  ./src/testReduction.cpp
  ./cuda/testReduction.cu
#  ./cuda/cuda_pcl_helpers.cu
  )

pods_install_libraries(jsCore)
pods_install_headers(
  ./include/jsCore/cuda_global.h
  ./include/jsCore/gpuMatrix.hpp
  ./include/jsCore/clData.hpp
  ./include/jsCore/clDataGpu.hpp
  ./include/jsCore/global.hpp
  ./include/jsCore/timer.hpp
  ./include/jsCore/timerLog.hpp
  DESTINATION jsCore)
pods_install_headers(
  ./include/nvidia/helper_string.h
  ./include/nvidia/helper_cuda.h
  DESTINATION nvidia)

#install(DIRECTORY include/jsCore/ DESTINATION include/jsCore/
#  FILES_MATCHING PATTERN "*.hpp")
#install(DIRECTORY include/jsCore/ DESTINATION include/jsCore/
#  FILES_MATCHING PATTERN "*.h")
#install(DIRECTORY include/nvidia/ DESTINATION include/nvidia/
#  FILES_MATCHING PATTERN "*.h")

