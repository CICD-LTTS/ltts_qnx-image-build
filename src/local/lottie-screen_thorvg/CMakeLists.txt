cmake_minimum_required(VERSION 3.25.1)

option(BARE_METAL "Enable bare metal backend" OFF)

project(lottie-player)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/out")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/out/lib")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/out/bin")

include_directories(screen)
include_directories(bare_metal)
set(BARE_METAL_SRCS bare_metal/frame_buffer.c bare_metal/bare_metal_core.cpp)
set(SCREEN_SRCS screen/screen_core.cpp)

if(BARE_METAL)
  add_definitions(-DBAREMETAL)
  add_executable(${PROJECT_NAME} lottie.cpp ${BARE_METAL_SRCS} ${SCREEN_SRCS})
else()
  add_executable(${PROJECT_NAME} lottie.cpp ${SCREEN_SRCS})
endif()

find_package(PkgConfig REQUIRED)
pkg_search_module(THORVG REQUIRED IMPORTED_TARGET thorvg)

target_include_directories(${PROJECT_NAME} PUBLIC ${THORVG_INCLUDE_DIRS})
target_link_options(${PROJECT_NAME} PUBLIC ${THORVG_LDFLAGS})
target_link_libraries(${PROJECT_NAME} PUBLIC thorvg)
target_link_libraries(${PROJECT_NAME} PUBLIC screen)

install(TARGETS ${PROJECT_NAME} DESTINATION bin)
