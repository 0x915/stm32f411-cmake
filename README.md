## STM32F411 Cmake
  Building STM32F4 firmware with cmake (armclang)  
  使用CMake与Armclang构建STM32F411固件
  Armcc支持已移除，但命令和测试结果仍旧保留，若有需求提issue

  测试通过C&C++混合编程的编译及下载运行
#### _main.cpp
```
#include "stm32f4xx_hal.h"
#include "stm32f4xx_hal_gpio.h"

#ifdef __cplusplus
extern "C"
{
#endif

int main_cpp(void);

#ifdef __cplusplus
}
#endif

int main_cpp(void){
	HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
	HAL_Delay(1000);
	HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
 	HAL_Delay(1000);
	return 0;
}
```
#### main.c
```
....
extern int main_cpp(void);
//!已移出CubeMX的匹配字符串!
int main(void) {
	HAL_Init();
	SystemClock_Config();
	MX_GPIO_Init();
	MX_USART2_UART_Init();
	while (1) {
		main_cpp();
	}
}
....
```
## Compile & Link
  编译与链接参数建议基于Keil工程选项调整

## ARMClang
  参考文件使用Keil内置编译器，位与 Keil5安装目录\ARM\ARMCLANG\bin
  也可自定义编译器路径，但编译器必须授权才可用

## CMakeFile
  #### 指向Keil安装目录  
  	set(KEIL_PATH C:/Keil_v5)
  #### 指向ARMCC可执行文件目录  
  	set(ARMCC_PATH ${KEIL_PATH}/ARM/ARMCC/bin)
  #### 指向ARMClang可执行文件目录  
  	set(ARMCLANG_PATH ${KEIL_PATH}/ARM/ARMCLANG/bin)
  #### 从Keil编译缓存目录(MDK-ARM\ProjectName)获取sct文件并复制到CMakeFile同目录  
  	set(LINK_SCT_FILE ../templete.sct)  
   	该文件定义RAM与ROM的启址及大小，相同晶圆不同引脚封装的MCU应该是通用的。

  #### 定义响应MCU宏(用于HAL库)   
  	add_compile_definitions(USE_HAL_DRIVER STM32F411xE)  
  #### 定义头文件搜索路径     
  	include_directories(...)  
  #### 定义源文件匹配名称  
  	file(...)  
  	模板内使用的是懒人写法自动搜索源文件   
   	但建议实际使用把这堆删了换成具体定义的源文件列表   
   	因为CubeMX复制到工程的文件可能会影响编译，例如stm32f4xx_hal_msp_template.c以及其他*_template.c   
  


  #### 参考Keil工程选项 C/C++(AC6) & Asm & Linker 卡最下方的命令修改
  	set(C_CXX_COMPILE_OPTIONS ...)   
  	set(ASM_COMPILE_OPTIONS ...)   
   	set(LINK_EXECUTABLE "${LINKER} ...")   
	
## Command

  cmake  
	  -DCMAKE_BUILD_TYPE=Release   
	  -DCMAKE_MAKE_PROGRAM=PATH(mingw32-make)  
	  -DCMAKE_C_COMPILER=PATH(armclang)   
	  -DCMAKE_CXX_COMPILER=PATH(armclang)   
	  -G "MinGW Makefiles"    
	  -S PATH(Project)     
	  -B PATH(Project/.armclang)   
	  
![image](https://user-images.githubusercontent.com/15169084/203590871-7065db98-8cc2-4a84-903f-f8d7e7f7899f.png)
![image](https://user-images.githubusercontent.com/15169084/203591378-aad1b9f2-2693-4444-b652-5aaceea8c552.png)

  cmake--build PATH(Project\/.armcc or Project/.armcc) --target template.axf -- -j 12  
  cmake--build PATH(Project/.armcc or Project/.armclang) --target template.axf -- -j 12  
  
 ![image](https://user-images.githubusercontent.com/15169084/203591960-66b48bcc-dec1-4dd7-8da9-f97714cbe749.png)
 ![image](https://user-images.githubusercontent.com/15169084/203592166-b9288700-6ca8-467a-8cd9-57afdf44edd2.png)


  
