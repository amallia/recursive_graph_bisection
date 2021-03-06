cmake_minimum_required(VERSION 3.0)

if (NOT CMAKE_BUILD_TYPE)
        message(STATUS "No build type selected, default to Release")
        set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build." FORCE)
endif()
MESSAGE( STATUS "CMAKE_BUILD_TYPE: " ${CMAKE_BUILD_TYPE} )

project(rgb)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    if (CXX_COMPILER_VERSION VERSION_LESS 4.7)
        message(STATUS "GCC version must be at least 4.7!")
    endif()
    set (CMAKE_CXX_FLAGS_RELEASE "-Wall -Wcast-align -fcilkplus -O3 -lm -DNDEBUG -std=c++17 -DHAVE_CXX0X -march=native")
    set (CMAKE_CXX_FLAGS_DEBUG   "-Wall -Wcast-align -fcilkplus -ggdb  -lm -std=c++17 -DHAVE_CXX0X -march=native")
elseif("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
    if (CXX_COMPILER_VERSION VERSION_LESS 4.2.1)
        message(STATUS  "Clang version must be at least 4.2.1!" )
    endif()
    set (CMAKE_CXX_FLAGS_RELEASE "-Wall -Wcast-align -fcilkplus -O3 -DNDEBUG -std=c++17 -DHAVE_CXX0X -msse4.1 -march=native")
    set (CMAKE_CXX_FLAGS_DEBUG   "-Wall -Wcast-align -fcilkplus -ggdb  -std=c++17 -DHAVE_CXX0X -msse4.1 -march=native")
else ()
    message(FATAL_ERROR "Please, use GCC or Clang compiler!")
endif()

find_package(OpenMP)
if(OPENMP_FOUND)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()

include_directories(include)
add_library(rgb INTERFACE)
target_include_directories(rgb INTERFACE
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
)

include(external/external.cmake)

add_subdirectory(src)


enable_testing()
add_subdirectory(test)
