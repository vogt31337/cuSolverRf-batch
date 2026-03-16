cmake_minimum_required(VERSION 3.10)

if (UNIX)
    set(CMAKE_CUDA_COMPILER /usr/local/cuda/bin/nvcc)
endif (UNIX)

set(PROJECT_NAME cuSolverRfBatch)

project(cuSolverRfBatch LANGUAGES C CXX CUDA)

# CUDA architecture configuration
set(CMAKE_CUDA_ARCHITECTURES 75 80 86 90)
find_package(CUDAToolkit REQUIRED)

set(SHARED_SOURCES
    mmio_wrapper.cpp
)

add_library(mmio mmio.c)

add_executable(cuSolverRf 
    cuSolverRf.cpp
    ${SHARED_SOURCES}
)

add_executable(cuSolverRfBatch 
    cuSolverRfBatch.cpp
    ${SHARED_SOURCES}
)

# CUDA
#set(CUDA_USE_STATIC_CUDA_RUNTIME OFF)

#find_package(CUDA REQUIRED)
#include_directories("${CUDA_INCLUDE_DIRS}")

find_library(CUDA_LIBRARY_CUDART cudart ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
find_library(CUDA_LIBRARY_CUSOLVER cusolver ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
find_library(CUDA_LIBRARY_CUSPARSE cusparse ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})

target_include_directories(
    cuSolverRf
    PRIVATE
	${CUDAToolkit_INCLUDE_DIRS}
	${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES}
    "./inc/"
)

target_include_directories(
    cuSolverRfBatch
    PRIVATE
    ${CUDAToolkit_INCLUDE_DIRS}
    ${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES}
    "./inc/"
)

target_link_libraries(
    cuSolverRfBatch
    ${CUDA_LIBRARY_CUDART}
    ${CUDA_LIBRARY_CUSOLVER}
    ${CUDA_LIBRARY_CUSPARSE}
    mmio
)

target_link_libraries(
    cuSolverRf
    ${CUDA_LIBRARY_CUDART}
    ${CUDA_LIBRARY_CUSOLVER}
    ${CUDA_LIBRARY_CUSPARSE}
    mmio
)

if (MSVC)
    add_compile_options("$<$<CONFIG:RELEASE>:/O2>")
endif (MSVC)
if (UNIX)
endif (UNIX)
