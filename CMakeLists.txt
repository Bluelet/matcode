cmake_minimum_required(VERSION 3.14)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Debug)
endif()

project(matcode LANGUAGES C CXX)

set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

if(MSVC)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /utf-8")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /EHsc")
  set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /O2")
else()
  set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g")
  set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3")
endif()

aux_source_directory(${CMAKE_SOURCE_DIR}/src matcode_src)
list(APPEND all_lib_src ${matcode_src})

add_library(matcode SHARED ${all_lib_src})
add_library(matcode_static STATIC ${all_lib_src})

if(MSVC)
  target_sources(matcode PRIVATE export.def)
  set_target_properties(matcode_static PROPERTIES OUTPUT_NAME "matcode_s")
else()
  set_target_properties(matcode_static PROPERTIES OUTPUT_NAME "matcode")
endif()

#aux_source_directory(${CMAKE_SOURCE_DIR}/src/test onefft_test_src)
list(APPEND matcode_test_src ${CMAKE_SOURCE_DIR}/src/test/matcode_test.c)
add_executable(matcode_test ${matcode_test_src})
target_link_libraries(matcode_test matcode)

aux_source_directory(${CMAKE_SOURCE_DIR}/src/prep matcode_prep_src)
list(APPEND all_prep_src ${matcode_prep_src})

add_executable(matcode_prep ${all_prep_src})

include_directories(${CMAKE_SOURCE_DIR}/inc)
