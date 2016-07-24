# Team MicroDynamics STM32风格指南

## 0. 前言

### 0.1 版本

- 1.0版本(2016.7.24)：缪宇飏(myyerrol)创建团队 STM32 开发风格指南。本文档参考《STM32嵌入式系统开发实战指南》有关章节，并根据实际的需求，对内容进行了总结。新成员应该认真学习本指南，掌握 STM32 基本的使用风格以及适用于本项目组的特定规则。如果有细节不统一的地方或者对本文档某处不是很认同，请在组内讨论统一之后，修改本指南。因为文档排版使用的是 Markdown 纯文本标记语言，也请后来者遵循本文档的开发方式，使用 Markdown 来修改、添加内容。

### 0.2 背景

对于嵌入式开发来说，代码和工程的管理规范是至关重要的。

## 1. 代码规范

简要介绍基于 C 语言的嵌入式编程规范的排版、注释、标识符命名、变量使用、代码可测性、程序效率、质量保证、代码编译、测试、程序版本与维护等内容。

### 1.1 ST 固件库编程规范

#### 1.1.1 缩写

|缩写      |外设/单元 |
|:--------|:--------|
|ADC      |模数转换器|
|BKP      |备份寄存器|
|CAN      |控制器局域网模块|
|DMA      |直接内存存取控制器|
|EXTI     |外部中断事件控制器|
|FLASH    |闪存存储器|
|GPIO     |通用输入输出|
|I2C      |内部集成电路|
|IWDG     |独立看门狗|
|NVIC     |嵌套中断向量列表控制器|
|PWR      |电源/功耗控制|
|RCC      |复位与时钟控制器|
|RTC      |实时时钟|
|SPI      |串行外设接口|
|SysTick  |系统嘀嗒定时器|
|TIM      |通用定时器|
|TIM1     |高级控制定时器|
|USART    |通用同步异步接收发射端|
|WWDG     |窗口看门狗|

#### 1.1.2 命名规则

1. 系统、源程序文件和头文件命名都以“stm32f10x_”作为开头，例如：stm32f10x_conf.h。
2. 常量仅被应用于一个文件的，定义于该文件中。被应用于多个文件的，在对应头文件中定义。所有常量都由英文字母大写书写。
3. 寄存器作为常量处理。他们的命名都由英文字母大写书写。在大多数情况下，他们采用
与缩写规范与本用户手册一致。
4. 外设函数的命名以该外设的缩写加下划线为开头。每个单词的第一个字母都由英文字母
大写书写，例如：SPI_SendData。在函数名中，只允许存在一个下划线，用以分隔外设缩写和函数名的其它部分。
5. 名为 PPP_Init 的函数，其功能是根据 PPP_InitTypeDef 中指定的参数，初始化外设 PPP，例如 TIM_Init。
6. 名为 PPP_DeInit 的函数，其功能为复位外设 PPP 的所有寄存器至缺省值，例如 TIM_DeInit。
7. 名为 PPP_StructInit 的函数，其功能为通过设置 PPP_InitTypeDef 结构中的各种参数来定义外设的功能。例如:USART_StructInit。
8. 名为 PPP_Cmd 的函数，其功能为使能或者失能外设 PPP。例如：SPI_Cmd。
9. 名为 PPP_ITConfig 的函数，其功能为使能或者失能来自外设 PPP 某中断源，例如：RCC_ITConfig。
10. 名为 PPP_DMAConfig 的函数，其功能为使能或者失能外设 PPP 的 DMA 接口。例如：TIM1_DMAConfig。
11. 用以配置外设功能的函数，总是以字符串“Config”结尾。例如：GPIO_PinRemapConfig。
12. 名为 PPP_GetFlagStatus 的函数，其功能为检查外设 PPP 某标志位被设置与否。例如：I2C_GetFlagStatus。
13. 名为 PPP_ClearFlag 的函数，其功能为清除外设 PPP 标志位。例如：I2C_ClearFlag。
14. 名为 PPP_GetITStatus 的函数，其功能为判断来自外设 PPP 的中断发生与否。例如：I2C_GetITStatus。
15. 名为 PPP_ClearITPendingBit 的函数，其功能为清除外设 PPP 中断待处理标志位。例如：I2C_ClearITPendingBit。

### 1.1.3 编码规则

1. 变量
固态函数库定义了 24 个变量类型，他们的类型和大小是固定的。在文件 stm32f10x_type.h 中我们定义了这些变量：

``` c
typedef signed long s32;
typedef signed short s16;
typedef signed char s8;
typedef signed long const sc32; /* Read Only */
typedef signed short const sc16; /* Read Only */
typedef signed char const sc8; /* Read Only */
typedef volatile signed long vs32;
typedef volatile signed short vs16;
typedef volatile signed char vs8;
typedef volatile signed long const vsc32; /* Read Only */
typedef volatile signed short const vsc16; /* Read Only */
typedef volatile signed char const vsc8; /* Read Only */
typedef unsigned long u32;
typedef unsigned short u16;
typedef unsigned char u8;
typedef unsigned long const uc32; /* Read Only */
typedef unsigned short const uc16; /* Read Only */
typedef unsigned char const uc8; /* Read Only */
typedef volatile unsigned long vu32;
typedef volatile unsigned short vu16;
typedef volatile unsigned char vu8;
typedef volatile unsigned long const vuc32; /* Read Only */
typedef volatile unsigned short const vuc16; /* Read Only */
typedef volatile unsigned char const vuc8; /* Read Only */
```

2. 布尔型
在文件 stm32f10x_type.h 中，布尔形变量被定义如下：

```c
typedef enum
{
    FALSE = 0,
    TRUE = !FALSE
} bool;
```

3. 标志位状态类型
在文件 stm32f10x_type.h 中，我们定义标志位类型(FlagStatus type)的 2 个可能值为“设置”100与“重置”(SET or RESET)。

```c
typedef enum
{
    RESET = 0,
    SET = !RESET
} FlagStatus;
```

4. 功能状态类型
在文件 stm32f10x_type.h 中，我们定义功能状态类型(FunctionalState type)的 2 个可能值为“使能”与“失能”(ENABLE or DISABLE)。

```c
typedef enum
{
    DISABLE = 0,
    ENABLE = !DISABLE
} FunctionalState;
```