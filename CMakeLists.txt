cmake_minimum_required(VERSION 2.8)

project(Hoard)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/CMake")

# set(Boost_USE_STATIC_LIBS ON)

# include(CTest)

if (UNIX)
  # set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} -std=c++11
elseif (MSVC)
  add_definitions(-DNOMINMAX)
endif ()

set(bitness 32)
if(CMAKE_SIZEOF_VOID_P EQUAL 8)
  set(bitness 64)
endif()

# include_directories(include)

add_subdirectory(src)
# add_subdirectory(benchmarks)
