## STM32F411 Cmake
  Building STM32F4 firmware with cmake (armcc/armclang)  
  使用CMake与Armcc/Armclang构建STM32F411固件
  
## Compile & Link
  编译与链接参数建议基于Keil工程选项修改

## ARMCC & ARMClang
  本仓库参考文件使用Keil内置编译器，也可自定义编译器路径，若无Keil需要适当修改编译选项

## CMakeFile
  #### 指向Keil安装目录  
  	set(KEIL_PATH C:/Keil_v5)
  #### 指向ARMCC可执行文件目录  
  	set(ARMCC_PATH ${KEIL_PATH}/ARM/ARMCC/bin)
  #### 指向ARMClang可执行文件目录  
  	set(ARMCLANG_PATH ${KEIL_PATH}/ARM/ARMCLANG/bin)

  #### 定义MCU内核  
  	set(CMAKE_SYSTEM_PROCESSOR Cortex-M4)  
  #### 定义响应MCU宏  
  	add_compile_definitions(USE_HAL_DRIVER STM32F411xE)  
  #### 定义头文件搜索路径  
  	include_directories(...)  
  #### 定义源文件匹配名称  
  	file(...)  
  
  #### 从Keil编译缓存目录(MDK-ARM\ProjectName)获取sct文件并复制到CMakeFile同目录  
  	set(LINK_SCT_FILE ../templete.sct)  

  #### 参考Keil工程选项 C/C++(AC6)|C/C++ & Asm & Linker 修改(ARMCC与ARMClang独立设置)  
  	set(C_CXX_COMPILE_OPTIONS ...)  
  	set(ASM_COMPILE_OPTIONS ...)  
  	set(CMAKE_C_LINK_EXECUTABLE ...)  
	
## Command

  cmake  
	  -DCMAKE_BUILD_TYPE=Release   
	  -DCMAKE_MAKE_PROGRAM=PATH(mingw32-make)  
	  -DCMAKE_C_COMPILER=PATH(armcc or armclang)   
	  -DCMAKE_CXX_COMPILER=PATH(armcc or armclang)   
	  -G "MinGW Makefiles"    
	  -S PATH(Project)     
	  -B PATH(Project/.armcc or Project/.armclang)   
	  
![image](https://user-images.githubusercontent.com/15169084/203590871-7065db98-8cc2-4a84-903f-f8d7e7f7899f.png)
![image](https://user-images.githubusercontent.com/15169084/203591378-aad1b9f2-2693-4444-b652-5aaceea8c552.png)

  cmake--build PATH(Project\/.armcc or Project/.armcc) --target template.axf -- -j 12  
  cmake--build PATH(Project/.armcc or Project/.armclang) --target template.axf -- -j 12  
  
 ![image](https://user-images.githubusercontent.com/15169084/203591960-66b48bcc-dec1-4dd7-8da9-f97714cbe749.png)
 ![image](https://user-images.githubusercontent.com/15169084/203592166-b9288700-6ca8-467a-8cd9-57afdf44edd2.png)


  
