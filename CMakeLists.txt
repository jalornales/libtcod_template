cmake_minimum_required(VERSION 3.16)
project(template C CXX)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_EXPORT_COMPILE_COMMANDS "ON")

# Debug Mode
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(ENABLE_SANITIZER_ADDRESS:BOOL=True)
    set(ENABLE_SANITIZER_UNDEFINED_BEHAVIOR:BOOL=True)
    set(ENABLE_CPPCHECK:BOOL=True)
	set(CMAKE_CXX_CLANG_TIDY clang-tidy -header-filter=.,-checks=-*,readability-*)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wpedantic -Wunused -g3 -fno-omit-frame-pointer -fsanitize=address -fno-exceptions -fno-rtti")
endif()

find_package(SDL2 REQUIRED)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g3 -O2")

# For Libtcod
set(LIBTCOD_GLAD "vendored")
set(LIBTCOD_LODEPNG "vendored")
set(LIBTCOD_STB "vendored")
set(LIBTCOD_UTF8PROC "vendored")
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/vendor/libtcod)
target_compile_options(libtcod PRIVATE "-w;")
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/lib/vendor/libtcod/data DESTINATION ${CMAKE_CURRENT_BINARY_DIR})

# For your project build
file(GLOB_RECURSE TEMPLATE_SRC ${PROJECT_SOURCE_DIR}/src/*.cpp ${PROJECT_SOURCE_DIR}/src/*/*.cpp)
add_executable(${PROJECT_NAME} ${TEMPLATE_SRC})

target_link_libraries(${PROJECT_NAME} PRIVATE libtcod)
