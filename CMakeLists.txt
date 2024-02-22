#   _   _ ____  _____ ___        _      _    
#  | | | |  _ \| ____/ _ \ _   _(_) ___| | __
#  | |_| | |_) |  _|| | | | | | | |/ __| |/ /
#  |  _  |  __/| |__| |_| | |_| | | (__|   < 
#  |_| |_|_|   |_____\__\_\\__,_|_|\___|_|\_\
                                           
cmake_minimum_required(VERSION 3.20)
project(small VERSION 0.1.1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build." FORCE)


# Dependencies #################################################################
find_package(OpenCV REQUIRED COMPONENTS core highgui videoio imgproc imgcodecs)
find_package(OpenVINO REQUIRED COMPONENTS Runtime)

find_path(OPENVINO_INCLUDE_DIR 
  NAMES "openvino.hpp"
  PATHS 
    "/opt/homebrew/include/"
    "/usr/local/include/"
    "C:/Program Files (x86)/Intel/openvino_2023.3/deployment_tools/inference_engine/include"
  PATH_SUFFIXES openvino
)
cmake_path(GET OPENVINO_INCLUDE_DIR PARENT_PATH OPENVINO_INCLUDE_DIR)

if (NOT OPENVINO_INCLUDE_DIR)
  message(FATAL_ERROR "OpenVINO not found")
else()
  message(STATUS "OpenVINO headers found at ${OPENVINO_INCLUDE_DIR}")
endif()

# nlohmann::json
include(FetchContent)
set(FETCHCONTENT_QUIET OFF)
set(BUILD_TESTING OFF CACHE INTERNAL "")
set(JSON_BuildTests OFF CACHE INTERNAL "")
FetchContent_Declare(json
  GIT_REPOSITORY https://github.com/nlohmann/json.git
  GIT_TAG        v3.11.3
  GIT_SHALLOW    TRUE
)
FetchContent_MakeAvailable(json)


# Static library with common source files ######################################
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

# Remove items that depend on gflags (which we don't want)
list(REMOVE_ITEM COMMON_SOURCES ${COMMON}/monitors/src/query_wrapper.cpp)
list(REMOVE_ITEM COMMON_HEADERS ${COMMON}/monitors/src/query_wrapper.h)
list(REMOVE_ITEM COMMON_SOURCES ${COMMON}/utils/src/args_helper.cpp)
list(REMOVE_ITEM COMMON_HEADERS ${COMMON}/utils/include/utils/default_flags.hpp)
# locally modified files
list(APPEND COMMON_SOURCES ${SOURCE_DIR}/args_helper.cpp)
list(APPEND COMMON_SOURCES ${SOURCE_DIR}/args_helper.cpp)


# Add the include directories ##################################################
include_directories(
  ${OpenCV_INCLUDE_DIRS}
  ${COMMON}/models/include
  ${COMMON}/monitors/include
  ${COMMON}/pipelines/include
  ${COMMON}/utils/include
  ${OPENVINO_INCLUDE_DIR}
  ${json_SOURCE_DIR}/include
)


# Targets ######################################################################
add_library(common STATIC ${COMMON_SOURCES} ${COMMON_HEADERS})

add_executable(quick ${SOURCE_DIR}/quick.cpp)
target_link_libraries(quick PRIVATE common ${OpenCV_LIBS} openvino::runtime)


# Download OpenPOSE models #####################################################
set(MODEL_NAME "human-pose-estimation-0001" CACHE STRING "Model name (no extension)")
if(NOT EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/models/${MODEL_NAME}.xml)
  message(STATUS "Downloading OpenPOSE model")
  file(DOWNLOAD https://storage.openvinotoolkit.org/repositories/open_model_zoo/2023.0/models_bin/1/${MODEL_NAME}/FP32/${MODEL_NAME}.xml models/${MODEL_NAME}.xml)
  file(DOWNLOAD https://storage.openvinotoolkit.org/repositories/open_model_zoo/2023.0/models_bin/1/${MODEL_NAME}/FP32/${MODEL_NAME}.bin models/${MODEL_NAME}.bin)
endif()