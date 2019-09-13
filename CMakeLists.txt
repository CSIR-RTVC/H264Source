# CMakeLists.txt for <H264Source> 
cmake_minimum_required(VERSION 3.11)

# hints from https://github.com/robotology/how-to-export-cpp-library/blob/master/CMakeLists.txt
project(H264Source
        LANGUAGES CXX C
        VERSION 0.0.0.0)

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_CRT_SECURE_NO_WARNINGS")

include(FetchContent)

FetchContent_Declare(
  H264v2
  GIT_REPOSITORY https://github.com/CSIR-RTVC/H264v2
)
# Declare dependencies
find_package(H264v2 1.0.0 REQUIRED)

FetchContent_Declare(
  DirectShowExt
  GIT_REPOSITORY https://github.com/CSIR-RTVC/DirectShowExt
)
# Declare dependencies
find_package(DirectShowExt 1.0.0 REQUIRED)

SET(FLT_HDRS
H264OutputPin.h
H264Source.h
H264Properties.h
resource.h
stdafx.h
VersionInfo.h
)

SET(FLT_SRCS 
DLLSetup.cpp
H264OutputPin.cpp
H264Source.cpp
H264Source.def
H264Source.rc
stdafx.cpp
)

ADD_LIBRARY(
H264Source SHARED ${FLT_SRCS} ${FLT_HDRS})

target_include_directories(H264Source
    PUBLIC 
        $<INSTALL_INTERFACE:install>    
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}
)

LINK_DIRECTORIES(
    $ENV{MSSDK}/Lib
)

TARGET_LINK_LIBRARIES (
H264Source
DirectShowExt::DirectShowExt
H264v2::H264v2
) 

INSTALL(
  TARGETS H264Source
  ARCHIVE DESTINATION bin
  LIBRARY DESTINATION bin
)

IF (REGISTER_DS_FILTERS)
ADD_CUSTOM_COMMAND(
TARGET H264Source 
POST_BUILD COMMAND 
regsvr32 /s \"$(TargetPath)\"
)
ENDIF(REGISTER_DS_FILTERS)
