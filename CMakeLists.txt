cmake_minimum_required(VERSION 3.10 FATAL_ERROR)
cmake_policy(VERSION 3.10)

project(libtemplatecpp VERSION 0.0.1)

# A CPP compiler is absolutely needed
enable_language(CXX)

list(APPEND CMAKE_MODULE_PATH
    "${CMAKE_SOURCE_DIR}/cmake/Modules"
    "${CMAKE_SOURCE_DIR}/cmake")

# For windows, this needs to be changed to OFF I believe
option(NON_TRANSITIVE "Option to use non-transitive linking" ON)
option(ENABLE_CLI "Build the client" ON)
option(ENABLE_TESTS "Build the tests" ON)

if(EXISTS ${CMAKE_SOURCE_DIR}/.git)
    find_package(Git)
endif()

# If the user specifies -DCMAKE_BUILD_TYPE on the command line, take their
# definition and dump it in the cache along with proper documentation,
# otherwise set CMAKE_BUILD_TYPE to Release prior to calling PROJECT()
#
# See https://cmake.org/pipermail/cmake/2008-September/023808.html
#     https://cmake.org/pipermail/cmake/2009-June/030311.html
# License: BSD 3-clause
# Author: Philip Lowman
#
if (CMAKE_BUILD_TYPE)
   set(CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE} CACHE
       STRING "Choose the type of build, options are:
- None (CMAKE_CXX_FLAGS)
- Debug
- Release
- RelWithDebInfo
- MinSizeRel.")
else ()
   set(CMAKE_BUILD_TYPE Release CACHE
       STRING "Choose the type of build, options are:
- None (CMAKE_CXX_FLAGS)
- Debug
- Release
- RelWithDebInfo
- MinSizeRel."
       FORCE)
endif ()

add_subdirectory(lib)

if (ENABLE_CLI)
    add_subdirectory(cli)
endif ()

if (MSVC)
    message (STATUS "If using the 'Visual Studio' generator, now run")
    message (STATUS "     cmake --build . [target] -- /maxcpucount[:N]")
    message (STATUS "If using the 'NMake Makefiles' generator, now run")
    message (STATUS "     cmake --build . [target]")
else ()
    message (STATUS "If using the 'Unix Makefiles' generator, now run")
    message (STATUS "     cmake --build . [target] -- -j$(nproc)")
endif ()
