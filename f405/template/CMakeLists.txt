cmake_minimum_required(VERSION 3.16)
project(stm32f405rgt_template)

# 编译相关
set(CMAKE_C_COMPILER "arm-linux-gnueabihf-gcc")
set(CMAKE_CXX_COMPILER "arm-linux-gnueabihf-g++")

# 指定输出
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

# 添加头文件路径
include_directories(
    ${PROJECT_SOURCE_DIR}/main
    ${PROJECT_SOURCE_DIR}/driver
    ${PROJECT_SOURCE_DIR}/driver/usart/
    ${PROJECT_SOURCE_DIR}/stdlib/inc
)


# 添加子目录
add_subdirectory(main)
add_subdirectory(stdlib/src)
