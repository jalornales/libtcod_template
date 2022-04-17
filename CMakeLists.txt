cmake_minimum_required(VERSION 3.16)
project(tcodrl C CXX)

# OPTIONS
option(SANITIZE "Compile with address sanitizer" off)
option(TIDY "Clang-tidy" off)

set(CMAKE_BUILD_TYPE RelWithDebInfo)
set(CMAKE_EXPORT_COMPILE_COMMANDS "ON")

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 14)

find_package(SDL2 REQUIRED)

# For Libtcod
set(LIBTCOD_GLAD "vendored")
set(LIBTCOD_LODEPNG "vendored")
set(LIBTCOD_STB "vendored")
set(LIBTCOD_UTF8PROC "vendored")
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/vendor/libtcod)
target_compile_options(libtcod PUBLIC "-w;-Wno-attributes;")

include_directories(${PROJECT_SOURCE_DIR}/lib/vendor/libtcod/src)

# For your project build
file(GLOB_RECURSE TCODRL_SRC ${PROJECT_SOURCE_DIR}/src/*.cpp ${PROJECT_SOURCE_DIR}/src/*/*.cpp)
add_executable(${PROJECT_NAME} ${TCODRL_SRC})
target_compile_options(${PROJECT_NAME} PUBLIC "-Wall;-Wextra;-pedantic;")
target_link_libraries(${PROJECT_NAME} PUBLIC libtcod)

enable_testing()

if(SANITIZE)
  message("Sanitizing Address...")
  set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "-fno-omit-frame-pointer")
  set_target_properties(${PROJECT_NAME} PROPERTIES LINK_FLAGS "-fsanitize=address")
endif()

if(TIDY)
  message("Using clang-tidy...")
  set(CMAKE_CXX_CLANG_TIDY clang-tidy -header-filter=.,-checks=-*,readability-*)
endif()

#file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/assets DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
