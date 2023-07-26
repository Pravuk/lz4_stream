cmake_minimum_required(VERSION 3.1)

project(lz4_stream LANGUAGES CXX)



set(CPM_DOWNLOAD_VERSION 0.27.2)
set(CPM_DOWNLOAD_LOCATION "${CMAKE_BINARY_DIR}/cmake/CPM_${CPM_DOWNLOAD_VERSION}.cmake")

if(NOT (EXISTS ${CPM_DOWNLOAD_LOCATION}))
  message(STATUS "Downloading CPM.cmake")
  file(DOWNLOAD https://github.com/TheLartians/CPM.cmake/releases/download/v${CPM_DOWNLOAD_VERSION}/CPM.cmake ${CPM_DOWNLOAD_LOCATION})
endif()
include(${CPM_DOWNLOAD_LOCATION})

CPMAddPackage(NAME lz4
              GITHUB_REPOSITORY lz4/lz4
              VERSION 1.9.4)

include(ExternalProject)

option(LZ4_STREAM_UseBundledLz4 "Use bundled static LZ4 library" OFF)
option(LZ4_STREAM_BuildTests "Build unittests" ON)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(LZ4_STREAM_LIBRARY_NAME ${PROJECT_NAME})
set(LZ4_STREAM_LIBRARY_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/include/")

# TODO: Probably shouldn't be set like this
set(CMAKE_CXX_STANDARD 11)

# TODO: Probably shouldn't be set like this
set(CMAKE_CXX_FLAGS_COVERAGE
  "${GCC_DEBUG_FLAGS} -fprofile-arcs -ftest-coverage"
  CACHE STRING "Flags used by the C++ compiler during coverage builds."
  FORCE)
set(CMAKE_EXE_LINKER_FLAGS_COVERAGE
  "${CMAKE_EXE_LINKER_FLAGS} --coverage"
  CACHE STRING "Flags used for linking binaries during coverage builds."
  FORCE)
mark_as_advanced(
  CMAKE_CXX_FLAGS_COVERAGE
  CMAKE_EXE_LINKER_FLAGS_COVERAGE)

# Create lz4_stream library
add_library(${LZ4_STREAM_LIBRARY_NAME} INTERFACE)
target_include_directories(${LZ4_STREAM_LIBRARY_NAME} INTERFACE ${LZ4_STREAM_LIBRARY_INCLUDE_DIR})

message(STATUS "Using bundled LZ4 library")
add_subdirectory("thirdparty/lz4")
target_link_libraries(${LZ4_STREAM_LIBRARY_NAME} INTERFACE lz4)
target_include_directories(${LZ4_STREAM_LIBRARY_NAME} INTERFACE lz4)

if(LZ4_STREAM_BuildTests)
  enable_testing()
  add_subdirectory("thirdparty/catch2")
  add_subdirectory("test")
endif()

# Create example executables
# TODO: Move to own directory
add_executable(lz4_compress lz4_compress.cpp)
target_link_libraries(lz4_compress ${LZ4_STREAM_LIBRARY_NAME})

add_executable(lz4_decompress lz4_decompress.cpp)
target_link_libraries(lz4_decompress ${LZ4_STREAM_LIBRARY_NAME})
