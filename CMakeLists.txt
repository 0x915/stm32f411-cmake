cmake_minimum_required(VERSION 3.22)
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
set(CMAKE_C_COMPILER_WORKS TRUE)
set(CMAKE_CXX_COMPILER_WORKS TRUE)

##################################################
#                 CONFIG PROJECT                 #
##################################################

project(template C ASM)
set(ENABLE_BUILD_HEX TRUE)
set(ENABLE_BUILD_ASM FALSE)
set(ENABLE_BUILD_BIN FALSE)

##################################################
#                   CONFIG PATH                  #
##################################################

set(KEIL_PATH C:/Keil_v5)
set(ARMCC_PATH ${KEIL_PATH}/ARM/ARMCC/bin)
set(ARMCLANG_PATH ${KEIL_PATH}/ARM/ARMCLANG/bin)


##################################################
#                 CONFIG MCU FILE                #
##################################################

set(CMAKE_SYSTEM_PROCESSOR Cortex-M4)
add_compile_definitions(USE_HAL_DRIVER STM32F411xE)

include_directories(
        Core/Inc
        Drivers/STM32F4xx_HAL_Driver/Inc
        Drivers/STM32F4xx_HAL_Driver/Inc/Legacy
        Drivers/CMSIS/Device/ST/STM32F4xx/Include
        Drivers/CMSIS/Include
)

file(GLOB SOURCES
        MDK-ARM/startup_stm32f411xe.s
        Core/Src/*.c
        Drivers/STM32F4xx_HAL_Driver/Src/*.c
        Drivers/CMSIS/**/*.c
        )


##################################################
#                  CONFIG ARMCC                  #
##################################################

if ("${CMAKE_C_COMPILER_ID}" STREQUAL "ARMCC")

    MESSAGE("\n========== ARMCC COMPILER ==========\n")

    set(CMAKE_C_STANDARD 99)
    set(CMAKE_CXX_STANDARD 03)

    set(CMAKE_C_COMPILER ${ARMCC_PATH}/armcc)
    set(CMAKE_CXX_COMPILER ${ARMCC_PATH}/armcc)
    set(CMAKE_ASM_COMPILER ${ARMCC_PATH}/armasm)
    set(CMAKE_OBJCOPY ${ARMCC_PATH}/fromelf)

    set(CMAKE_C_COMPILER_AR ${ARMCC_PATH}/armar)
    set(CMAKE_CXX_COMPILER_AR ${ARMCC_PATH}/armar)
    set(CMAKE_ASM_COMPILER_AR ${ARMCC_PATH}/armar)


    set(C_CXX_COMPILE_OPTIONS --c99 --cpu Cortex-M4.fp.sp -O3 --apcs=interwork --split_sections)
    set(ASM_COMPILE_OPTIONS --cpu Cortex-M4.fp.sp --apcs=interwork -I ${KEIL_PATH}/ARM/RV31/INC --pd "__UVISION_VERSION SETA 534")

    add_compile_options(
            "$<$<COMPILE_LANGUAGE:C>:${C_CXX_COMPILE_OPTIONS}>"
            "$<$<COMPILE_LANGUAGE:ASM>:${ASM_COMPILE_OPTIONS}>"
    )

    include_directories(${ARMCC_PATH}/../include)

    set(LINK_EXE ${ARMCC_PATH}/armlink)
    set(LINK_SCT_FILE ../templete.sct)

    set(CMAKE_C_LINK_EXECUTABLE "${LINK_EXE} \
        --cpu=Cortex-M4.fp.sp \
        <OBJECTS> \
        <CMAKE_C_LINK_FLAGS> \
        --strict --scatter ${LINK_SCT_FILE} \
        --summary_stderr --info summarysizes --map --load_addr_map_info --xref --callgraph --symbols \
        --info sizes --info totals --info unused --info veneers \
        <LINK_LIBRARIES> \
        --list <TARGET_BASE>.map \
        -o <TARGET> \
    ")

endif ()


##################################################
#                CONFIG ARMClang                 #
##################################################

if ("${CMAKE_C_COMPILER_ID}" STREQUAL "ARMClang")

    MESSAGE("\n========== ARMClang COMPILER ==========\n")

    set(CMAKE_C_STANDARD 11)
    set(CMAKE_CXX_STANDARD 14)

    set(CMAKE_C_COMPILER ${ARMCLANG_PATH}/armclang)
    set(CMAKE_CXX_COMPILER ${ARMCLANG_PATH}/armclang)
    set(CMAKE_ASM_COMPILER ${ARMCLANG_PATH}/armclang)
    set(CMAKE_OBJCOPY ${ARMCLANG_PATH}/fromelf)

    set(CMAKE_C_COMPILER_AR ${ARMCLANG_PATH}/armar)
    set(CMAKE_CXX_COMPILER_AR ${ARMCLANG_PATH}/armar)
    set(CMAKE_ASM_COMPILER_AR ${ARMCLANG_PATH}/armar)

    set(C_CXX_COMPILE_OPTIONS -xc -std=c11 --target=arm-arm-none-eabi -mcpu=cortex-m4 -mfpu=fpv4-sp-d16 -mfloat-abi=hard -c
            -fno-rtti -funsigned-char -fshort-enums -fshort-wchar
            -gdwarf-3 -O1 -ffunction-sections
            -Wno-packed -Wno-missing-variable-declarations -Wno-missing-prototypes
            -Wno-missing-noreturn -Wno-sign-conversion -Wno-nonportable-include-path
            -Wno-reserved-id-macro -Wno-unused-macros -Wno-documentation-unknown-command
            -Wno-documentation -Wno-license-management -Wno-parentheses-equality
            )

    set(ASM_COMPILE_OPTIONS --target=arm-arm-none-eabi -mcpu=cortex-m4 -mfpu=fpv4-sp-d16 -mfloat-abi=hard -masm=auto -c)

    add_compile_options(
            "$<$<COMPILE_LANGUAGE:C>:${C_CXX_COMPILE_OPTIONS}>"
            "$<$<COMPILE_LANGUAGE:ASM>:${ASM_COMPILE_OPTIONS}>"
    )

    include_directories(${ARMCLANG_PATH}/../include)

    set(LINK_EXE ${ARMCLANG_PATH}/armlink)
    set(LINK_SCT_FILE ../templete.sct)

    set(CMAKE_C_LINK_EXECUTABLE "${LINK_EXE} \
        --cpu=Cortex-M4.fp.sp \
        <OBJECTS> \
        <CMAKE_C_LINK_FLAGS> \
        --strict --scatter ${LINK_SCT_FILE} \
        --summary_stderr --info summarysizes --map --load_addr_map_info --xref --callgraph --symbols \
        --info sizes --info totals --info unused --info veneers \
        <LINK_LIBRARIES> \
        --list <TARGET_BASE>.map \
        -o <TARGET> \
    ")

endif ()


##################################################
#                  CONFIG BUILD                  #
##################################################

add_executable(${PROJECT_NAME}.axf ${SOURCES})

if (ENABLE_BUILD_HEX)
    set(FW_hex ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
    add_custom_command(TARGET ${PROJECT_NAME}.axf POST_BUILD
            COMMENT "Building ${FW_hex}"
            COMMAND ${CMAKE_OBJCOPY} --i32 $<TARGET_FILE:${PROJECT_NAME}.axf> --output=${FW_hex}
            )
endif ()

if (ENABLE_BUILD_ASM)
    set(FW_asm ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.asm)
    add_custom_command(TARGET ${PROJECT_NAME}.axf POST_BUILD
            COMMENT "Building ${FW_asm}"
            COMMAND ${CMAKE_OBJCOPY} --text -c --cpu=Cortex-M4 $<TARGET_FILE:${PROJECT_NAME}.axf> --output=${FW_asm}
            )
endif ()

if (ENABLE_BUILD_BIN)
    set(FW_bin ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)
    add_custom_command(TARGET ${PROJECT_NAME}.axf POST_BUILD
            COMMENT "Building ${FW_bin}"
            COMMAND ${CMAKE_OBJCOPY} --bin $<TARGET_FILE:${PROJECT_NAME}.axf> --output=${FW_bin}
            )
endif ()
