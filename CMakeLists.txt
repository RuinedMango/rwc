cmake_minimum_required(VERSION 3.20)
project(rwc C)

set(CMAKE_C_STANDARD 11)

set(SOURCE_FILES src/main.c)

find_package(PkgConfig REQUIRED)
pkg_check_modules(GTK REQUIRED gtk4)
pkg_check_modules(LAYERS REQUIRED gtk4-layer-shell-0)

include(cmake/CPM.cmake)

CPMAddPackage(
  NAME lua
  GIT_REPOSITORY https://github.com/lua/lua.git
  VERSION 5.3.5
  DOWNLOAD_ONLY YES
)

if (lua_ADDED)
  # lua has no CMake support, so we create our own target

  FILE(GLOB lua_sources ${lua_SOURCE_DIR}/*.c)
  list(REMOVE_ITEM lua_sources "${lua_SOURCE_DIR}/lua.c" "${lua_SOURCE_DIR}/luac.c")
  add_library(lua STATIC ${lua_sources})

  target_include_directories(lua
    PUBLIC
      $<BUILD_INTERFACE:${lua_SOURCE_DIR}>
  )
endif()

include_directories(${GTK_INCLUDE_DIRS})
include_directories(${LAYERS_INCLUDE_DIRS})
link_directories(${GTK_LIBRARY_DIRS})
link_directories(${LAYERS_LIBRARY_DIRS})

add_executable(${PROJECT_NAME} ${SOURCE_FILES})
target_link_libraries(${PROJECT_NAME} ${GTK_LIBRARIES} ${LAYERS_LIBRARIES} lua)
