cmake_minimum_required(VERSION 3.3)
project(SimITL)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

option(GEN_COVERAGE "Generate coverage profile" OFF)

# Linker options for betaflight and windows
if (NOT APPLE)
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -T${CMAKE_SOURCE_DIR}/extern/betaflight/src/main/target/SITL/pg.ld")
endif()

if (WIN32)
    message("-- Win32 --")
    #set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wa,-mbig-obj -g -O0")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wa,-mbig-obj -O2")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static-libgcc -static-libstdc++")
endif ()

if (GEN_COVERAGE)
  message("Building with coverage")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O0 -fprofile-arcs -ftest-coverage")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -O0 -fprofile-arcs -ftest-coverage")
endif ()

#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2")

# simitl lib 
#  used by the SimITL executable which contains the betaflight sitl target and extensions
add_subdirectory(extern/)

# SimITL executable
add_subdirectory(src/)

# SimITL shared memory lib for game clients to interface with a running SimITL process
add_subdirectory(lib/)

# SimITL-Tester
# enables testing of a SimITL process without a gameclient
add_subdirectory(tools/simitl-tester)