cmake_minimum_required(VERSION 3.16)
project(stm32f405rgt_template)

# 编译相关
set(CMAKE_C_COMPILER "arm-linux-gnueabihf-gcc")
set(CMAKE_CXX_COMPILER "arm-linux-gnueabihf-g++")
set(CMAKE_OBJCOPY "arm-linux-gnueabihf-objcopy")
set(CMAKE_SIZE "arm-linux-gnueabihf-size")

set(MCU_FLAGS "-mcpu=cortex-m4 -mfloat-abi=hard -mfpu=fpv4-sp-d16")
set(CMAKE_C_FLAGS "${MCU_FLAGS} -w -Wno-unknown-pragmas")
set(CMAKE_C_FLAGS_DEBUG "-O0 -g2 -ggdb")
set(CMAKE_C_FLAGS_RELEASE "-O3")

# 指定输出
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

# 添加头文件路径
include_directories(
    ${PROJECT_SOURCE_DIR}/main
    ${PROJECT_SOURCE_DIR}/inc
    ${PROJECT_SOURCE_DIR}/driver
    ${PROJECT_SOURCE_DIR}/driver/usart/
    ${PROJECT_SOURCE_DIR}/stdlib/inc
)

# 添加全局定义
add_definitions(
    -DSTM32F40_41xxx
    -DUSE_STDPERIPH_DRIVER
    -DARM_MATH_CM4
)

# # 添加子目录
# add_subdirectory(main)
# add_subdirectory(core)
# add_subdirectory(stdlib/src)

# linker file
set(LINKER_SCRIPT ${PROJECT_SOURCE_DIR}/core/STM32F405RGT_FLASH.ld)
set(CMAKE_EXE_LINKER_FLAGS
    "-nostartfiles -T${LINKER_SCRIPT}  -Wl,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map,--cref -Wl,--gc-sections"
    # --specs=nano.specs -specs=nosys.specs 
)


set_property(
    SOURCE 
    ${PROJECT_SOURCE_DIR}/core/startup_stm32f40_41xxx.s 
    PROPERTY 
    LANGUAGE 
    C
)
# add_library(startup 
#     ${PROJECT_SOURCE_DIR}/core/startup_stm32f40_41xxx.s 
# )

aux_source_directory(./stdlib/src STD_LIB_SRC)
# add_library(std ${STD_LIB_SRC})

# target_link_libraries(
#     std
# )

add_executable(stm32f405_template.elf 
    main/main.c
    ${PROJECT_SOURCE_DIR}/core/startup_stm32f40_41xxx.s 
    ${STD_LIB_SRC}
)
