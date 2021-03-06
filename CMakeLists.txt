cmake_minimum_required(VERSION 3.8)
project(std-fwd)

option(STDFWD_FORCE_INCLUDE "if true, force includes <stdfwd.hh> in every header depending on this library." OFF)


####################################################################
# Library version
get_directory_property(hasParent PARENT_DIRECTORY)
if(NOT ${hasParent})

    add_library(std-fwd INTERFACE)
    target_include_directories(std-fwd INTERFACE "include/")

    if(STDFWD_FORCE_INCLUDE)
        if (MSVC)
            target_compile_options(${PROJECT_NAME} INTERFACE "/FIstdfwd.hh")
        else()
            target_compile_options(${PROJECT_NAME} INTERFACE "-includestdfwd.hh")
        endif()
    endif()


####################################################################
# Standalone version (for testing)
else()

    set(CMAKE_CXX_STANDARD 17)
    set(CMAKE_CXX_STANDARD_REQUIRED ON)

    file(GLOB_RECURSE SOURCES "tests/*.*" "include/*")

    add_executable(${PROJECT_NAME} ${SOURCES})
    target_include_directories(${PROJECT_NAME} PUBLIC "include/")

    if(MSVC)
        target_compile_options(${PROJECT_NAME} PUBLIC
            /MP
            /FC
        )
    else()
        target_compile_options(${PROJECT_NAME} PUBLIC
            -Wall
        )
        target_link_libraries(${PROJECT_NAME} PUBLIC stdc++fs) # filesystem
    endif()

endif()

