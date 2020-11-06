cmake_minimum_required(VERSION 3.16)
project(stm32f405rgt_template)

# 编译相关
set(CMAKE_C_COMPILER "arm-linux-gnueabihf-gcc")
set(CMAKE_CXX_COMPILER "arm-linux-gnueabihf-g++")
set(CMAKE_OBJCOPY "arm-linux-gnueabihf-objcopy")
set(CMAKE_SIZE "arm-linux-gnueabihf-size")

# 编译参数
set(MCU_FLAGS "-mcpu=cortex-m4 -mfloat-abi=hard -mfpu=fpv4-sp-d16 -Wl,--build-id=none -O0 -nostdlib")
set(CMAKE_C_FLAGS "${MCU_FLAGS}")
# set(CMAKE_C_FLAGS_DEBUG "-O0 -g2 -ggdb")
# set(CMAKE_C_FLAGS_RELEASE "-O3")

# 指定输出
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

# 添加头文件路径
include_directories(
    ${PROJECT_SOURCE_DIR}/main
    ${PROJECT_SOURCE_DIR}/core
    ${PROJECT_SOURCE_DIR}/inc
    ${PROJECT_SOURCE_DIR}/driver
    ${PROJECT_SOURCE_DIR}/driver/usart/
    ${PROJECT_SOURCE_DIR}/stdlib/inc
)

# 添加全局定义
add_definitions(
    -DSTM32F40_41xxx            # 使用stm32f40xx
    -DUSE_STDPERIPH_DRIVER      # 使用标准库
    -DARM_MATH_CM4              # 使用DSP库
)

# # 添加子目录
# add_subdirectory(main)
# add_subdirectory(core)
# add_subdirectory(stdlib/src)

# linker file
set(LINKER_SCRIPT ${PROJECT_SOURCE_DIR}/core/STM32F405RGT_FLASH.ld)
set(CMAKE_EXE_LINKER_FLAGS
    "-T${LINKER_SCRIPT}"
    # --specs=nano.specs -specs=nosys.specs 
)

ENABLE_LANGUAGE(ASM)

# 创建库
aux_source_directory(./stdlib/src STD_LIB_SRC)
add_library(std ${STD_LIB_SRC})

# 创建elf
add_executable(${PROJECT_NAME}.elf
    # ${PROJECT_SOURCE_DIR}/core/startup_stm32f40_41xxx.s 
    ${PROJECT_SOURCE_DIR}/core/startup_stm32f40xx.s 
    main/main.c 
    main/system_stm32f4xx.c 
    main/stm32f4xx_it.c 
)
target_link_libraries(${PROJECT_NAME}.elf
    std
)

set(ELF_FILE ${PROJECT_SOURCE_DIR}/bin/${PROJECT_NAME}.elf)
set(HEX_FILE ${PROJECT_SOURCE_DIR}/bin/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_SOURCE_DIR}/bin/${PROJECT_NAME}.bin)


add_custom_command(TARGET "${PROJECT_NAME}.elf" POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} -Obinary ${ELF_FILE} ${BIN_FILE}
    COMMAND ${CMAKE_OBJCOPY} -Oihex  ${ELF_FILE} ${HEX_FILE}
)
