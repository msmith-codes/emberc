cmake_minimum_required(VERSION 4.0)
project(emberc LANGUAGES CXX)

###################
#  Configure C++  #
###################
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_compile_options(-Wall -Wextra -Wpedantic)

#####################
#  Configure CMake  #
#####################
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

##########
#  LLVM  #
##########
find_package(LLVM REQUIRED CONFIG)

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${LLVM_CXXFLAGS}")

file(GLOB_RECURSE SOURCES CONFIGURE_DEPENDS src/*.cpp include/*.hpp)

add_executable(${PROJECT_NAME} ${SOURCES})

# Link against LLVM libraries
llvm_map_components_to_libnames(LLVM_LIBS
    core
    support
    irreader
    passes
    nativecodegen
    orcjit
)

target_link_libraries(${PROJECT_NAME} PRIVATE ${LLVM_LIBS})
