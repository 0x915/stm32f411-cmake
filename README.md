# STM32-CMake
  Building STM32F4 firmware with cmake (armcc/armclang)
  使用CMake构建STM32F4系列固件

# Compile & Link
  编译与链接参数建议基于Keil工程选项修改

# ARMCC & ARMClang
  本仓库参考文件使用Keil内置编译器，也可自定义编译器路径，若无Keil需要适当修改编译选项

# CMakeFile
  #指向Keil安装目录
  set(KEIL_PATH C:/Keil_v5) 
  #指向ARMCC可执行文件目录
  set(ARMCC_PATH ${KEIL_PATH}/ARM/ARMCC/bin) 
  #指向ARMClang可执行文件目录
  set(ARMCLANG_PATH ${KEIL_PATH}/ARM/ARMCLANG/bin)

  #定义MCU内核
  set(CMAKE_SYSTEM_PROCESSOR Cortex-M4)
  #定义宏
  add_compile_definitions(USE_HAL_DRIVER STM32F411xE)
  #定义头文件搜索路径
  include_directories(...)
  #定义源文件匹配名称
  file(...)

  #从Keil编译缓存目录(MDK-ARM\ProjectName)获取sct文件并复制到CMakeFile同目录
  set(LINK_SCT_FILE ../templete.sct)

  #参考Keil工程选项修改(ARMCC与ARMClang独立设置)
  set(C_CXX_COMPILE_OPTIONS ...)
  set(ASM_COMPILE_OPTIONS ...)
  set(CMAKE_C_LINK_EXECUTABLE ...)
