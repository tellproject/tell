cmake_minimum_required(VERSION 2.8.12)
project(Tell)

# For vim autocompletion
set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

# Declare build options
option(USE_ASAN "Use the address sanitizer" OFF)

# Build with PIC by default
if(NOT CMAKE_POSITION_INDEPENDENT_CODE)
    set(CMAKE_POSITION_INDEPENDENT_CODE ON)
endif()

# Enable warnings
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")

# Enable ASAN
if(USE_ASAN)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-omit-frame-pointer -fno-optimize-sibling-calls -fsanitize=address")
endif()

# Enable colored compiler output when building with Ninja
if(${CMAKE_GENERATOR} STREQUAL "Ninja")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fdiagnostics-color")
endif()

# Build Crossbow
add_subdirectory(crossbow)

# Build CommitManager
add_subdirectory(commitmanager)

# Build TellStore
add_subdirectory(tellstore)

# Build Bd-Tree
add_subdirectory(bdtree)

# Build TellDB
add_subdirectory(telldb)

# Build TellJava
add_subdirectory(telljava)
