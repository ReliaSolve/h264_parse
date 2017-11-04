cmake_minimum_required(VERSION 2.8.12)
project(mpeg4ip)

#-----------------------------------------------------------------------------
# Local CMake Modules
list(APPEND CMAKE_MODULE_PATH
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake
    ${CMAKE_INSTALL_PREFIX}/cmake
)

#Specify where the find_package stuff may be
list(APPEND CMAKE_PREFIX_PATH
    ${CMAKE_INSTALL_PREFIX}/lib/cmake
)

#-----------------------------------------------------------------------------
# Configuration file recording what capabilities are build into the Fovea
# library installation, so that client applications can behave appropriately.
configure_file("${PROJECT_SOURCE_DIR}/mpeg4ip_config.h.cmake_in"
	"${PROJECT_BINARY_DIR}/mpeg4ip_config.h")
include_directories(${PROJECT_BINARY_DIR})

#-----------------------------------------------------------------------------
# Build the library itself and declare what bits need to be installed
set(my_SOURCES
    getopt.c
)

set(my_HEADER
    "${PROJECT_BINARY_DIR}/mpeg4ip_config.h"
    getopt.h
    mp4av_h264.h
    mpeg4ip.h
    mpeg4ip_bitstream.h
    mpeg4ip_getopt.h
    mpeg4ip_version.h
    mpeg4ip_win32.h
)

add_library(mpeg4ipLib
    ${my_SOURCES} ${my_HEADER})
set_property(TARGET mpeg4ipLib PROPERTY PUBLIC_HEADER ${my_HEADER})
target_include_directories(mpeg4ipLib SYSTEM
    INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>)

#-----------------------------------------------------------------------------
#Build the testapps if required
option(mpeg4ip_BUILD_TESTS "Enable to build mpeg4ip tests" ON)

if(mpeg4ip_BUILD_TESTS)

  add_executable(h264_parse main.cpp)
  target_link_libraries(h264_parse
      mpeg4ipLib
  )

endif()
