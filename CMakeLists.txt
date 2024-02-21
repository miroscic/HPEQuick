cmake_minimum_required(VERSION 3.20)
project(small VERSION 0.1.1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

set(OPENVINO_INCLUDE_DIR "" CACHE PATH "Path to OpenVINO include directory")

set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

find_package(OpenCV REQUIRED COMPONENTS core highgui videoio imgproc imgcodecs)
find_package(OpenVINO REQUIRED COMPONENTS Runtime)

set(COMMON ${SOURCE_DIR}/common)

file(GLOB COMMON_SOURCES
  ${COMMON}/models/src/*.cpp
  ${COMMON}/monitors/src/*.cpp
  ${COMMON}/pipelines/src/*.cpp
  ${COMMON}/utils/src/*.cpp
)

file(GLOB COMMON_HEADERS
  ${COMMON}/models/include/models/*.h
  ${COMMON}/monitors/include/monitors/*.h
  ${COMMON}/pipelines/include/pipelines/*.h
  ${COMMON}/utils/include/utils/*.h*
)

list(REMOVE_ITEM COMMON_SOURCES ${COMMON}/monitors/src/query_wrapper.cpp)
list(REMOVE_ITEM COMMON_HEADERS ${COMMON}/monitors/src/query_wrapper.h)
list(REMOVE_ITEM COMMON_SOURCES ${COMMON}/utils/src/args_helper.cpp)
list(REMOVE_ITEM COMMON_HEADERS ${COMMON}/utils/include/utils/default_flags.hpp)
list(APPEND COMMON_SOURCES ${SOURCE_DIR}/args_helper.cpp)
list(APPEND COMMON_SOURCES ${SOURCE_DIR}/args_helper.cpp)

include_directories(
  ${OpenCV_INCLUDE_DIRS}
  ${COMMON}/models/include
  ${COMMON}/monitors/include
  ${COMMON}/pipelines/include
  ${COMMON}/utils/include
  ${OPENVINO_INCLUDE_DIR}
)

add_library(common STATIC ${COMMON_SOURCES} ${COMMON_HEADERS})

add_executable(quick ${SOURCE_DIR}/quick.cpp)
target_link_libraries(quick PRIVATE common ${OpenCV_LIBS} openvino::runtime)
