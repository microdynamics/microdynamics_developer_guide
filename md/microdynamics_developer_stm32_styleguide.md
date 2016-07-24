# Team MicroDynamics STM32风格指南

[TOC]

## 0. 前言

### 0.1 版本

- 1.0版本(2016.7.24)：缪宇飏(myyerrol)创建团队 STM32 开发风格指南。本文档参考《STM32嵌入式系统开发实战指南》有关章节，并根据实际的需求，对内容进行了总结。新成员应该认真学习本指南，掌握 STM32 基本的使用风格以及适用于本项目组的特定规则。如果有细节不统一的地方或者对本文档某处不是很认同，请在组内讨论统一之后，修改本指南。因为文档排版使用的是 Markdown 纯文本标记语言，也请后来者遵循本文档的开发方式，使用 Markdown 来修改、添加内容。

### 0.2 背景

对于嵌入式开发来说，代码和工程的管理规范是至关重要的。

## 1. 代码规范

简要介绍基于 C 语言的嵌入式编程规范的排版、注释、标识符命名、变量使用、代码可测性、程序效率、质量保证、代码编译、测试、程序版本与维护等内容。

### 1.1 ST 固件库编程规范

#### 1.1.1 缩写

|缩写      |外设/单元|
|:--------|:-------|
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

- 系统、源程序文件和头文件命名都以“stm32f10x_”作为开头。例如：stm32f10x_conf.h。
- 常量仅被应用于一个文件的，定义于该文件中。被应用于多个文件的，在对应头文件中定义。所有常量都由英文字母大写书写。
- 寄存器作为常量处理。他们的命名都由英文字母大写书写。在大多数情况下，他们采用与缩写规范与本用户手册一致。
- 外设函数的命名以该外设的缩写加下划线为开头。每个单词的第一个字母都由英文字母大写书写，例如：SPI_SendData。在函数名中，只允许存在一个下划线，用以分隔外设缩写和函数名的其它部分。
- 名为 PPP_Init 的函数，其功能是根据 PPP_InitTypeDef 中指定的参数，初始化外设 PPP，例如 TIM_Init。
- 名为 PPP_DeInit 的函数，其功能为复位外设 PPP 的所有寄存器至缺省值，例如 TIM_DeInit。
- 名为 PPP_StructInit 的函数，其功能为通过设置 PPP_InitTypeDef 结构中的各种参数来定义外设的功能。例如:USART_StructInit。
- 名为 PPP_Cmd 的函数，其功能为使能或者失能外设 PPP。例如：SPI_Cmd。
- 名为 PPP_ITConfig 的函数，其功能为使能或者失能来自外设 PPP 某中断源，例如：RCC_ITConfig。
- 名为 PPP_DMAConfig 的函数，其功能为使能或者失能外设 PPP 的 DMA 接口。例如：TIM1_DMAConfig。
- 用以配置外设功能的函数，总是以字符串“Config”结尾。例如：GPIO_PinRemapConfig。
- 名为 PPP_GetFlagStatus 的函数，其功能为检查外设 PPP 某标志位被设置与否。例如：I2C_GetFlagStatus。
- 名为 PPP_ClearFlag 的函数，其功能为清除外设 PPP 标志位。例如：I2C_ClearFlag。
- 名为 PPP_GetITStatus 的函数，其功能为判断来自外设 PPP 的中断发生与否。例如：I2C_GetITStatus。
- 名为 PPP_ClearITPendingBit 的函数，其功能为清除外设 PPP 中断待处理标志位。例如：I2C_ClearITPendingBit。

#### 1.1.3 编码规则

1、变量<br>
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

2、布尔类型<br>
在文件 stm32f10x_type.h 中，布尔形变量被定义如下：

```c
typedef enum
{
    FALSE = 0,
    TRUE = !FALSE
} bool;
```

3、标志位状态类型<br>
在文件 stm32f10x_type.h 中，我们定义标志位类型(FlagStatus type)的 2 个可能值为“设置”100与“重置”(SET or RESET)。

```c
typedef enum
{
    RESET = 0,
    SET = !RESET
} FlagStatus;
```

4、功能状态类型<br>
在文件 stm32f10x_type.h 中，我们定义功能状态类型(FunctionalState type)的 2 个可能值为“使能”与“失能”(ENABLE or DISABLE)。

```c
typedef enum
{
    DISABLE = 0,
    ENABLE = !DISABLE
} FunctionalState;
```

5、错误类型<br>
在文件 stm32f10x_type.h 中，我们错误状态类型类型(ErrorStatus type)的 2 个可能值为“成功”与“出错”(SUCCESS or ERROR)。

```c
typedef enum
{
    ERROR = 0,
    SUCCESS = !ERROR
} ErrorStatus;
```

6、外设<br>
用户可以通过指向各个外设的指针访问各外设的控制寄存器。这些指针所指向的数据结构与各个外设的控制寄存器布局一一对应。<br>
外设控制寄存器结构文件 stm32f10x_map.h 包含了所有外设控制寄存器的结构，下例为 SPI 寄存器结构的声明：

```c
/*---------------- Serial Peripheral Interface --------------*/
typedef struct
{
    vu16 CR1;
    u16 RESERVED0;
    vu16 CR2;
    101u16 RESERVED1;
    vu16 SR;
    u16 RESERVED2;
    vu16 DR;
    u16 RESERVED3;
    vu16 CRCPR;
    u16 RESERVED4;
    vu16 RXCRCR;
    u16 RESERVED5;
    vu16 TXCRCR;
    u16 RESERVED6;
} SPI_TypeDef;
```

寄存器命名遵循上节的寄存器缩写命名规则。RESERVEDi(i 为一个整数索引值)表示被保留区域。

文件 stm32f10x_map.h 包含了所有外设的声明，下例为 SPI 外设的声明：

```c
#ifndef EXT
#Define EXT extern
#endif
...
#define PERIPH_BASE ((u32)0x40000000)
#define APB1PERIPH_BASE PERIPH_BASE
#define APB2PERIPH_BASE (PERIPH_BASE + 0x10000) ...
/* SPI2 Base Address definition*/
#define SPI2_BASE (APB1PERIPH_BASE + 0x3800) ...
/* SPI2 peripheral declaration*/
#ifndef DEBUG
...
#ifdef _SPI2
#define SPI2 ((SPI_TypeDef *) SPI2_BASE)
#endif /*_SPI2 */
...
#else /* DEBUG */
...
#ifdef _SPI2
102EXT SPI_TypeDef *SPI2;
#endif /*_SPI2 */
...
#endif /* DEBUG */
```

如果用户希望使用外设 SPI，那么必须在文件 stm32f10x_conf.h 中定义_SPI 标签。通过定义标签_SPIn，用户可以访问外设 SPIn 的寄存器。例如，用户必须在文件 stm32f10x_conf.h 中定义标签_SPI2，否则是不能访问 SPI2 的寄存器的。在文件 stm32f10x_conf.h 中，用户可以按照下例定义标签 _SPI 和 _SPIn。

```c
#define _SPI
#define _SPI1
#define _SPI2
```

每个外设都有若干寄存器专门分配给标志位。我们按照相应的结构定义这些寄存器。标志位的命名，同样遵循上节的外设缩写规范，以‘PPP_FLAG_’开始。对于不同的外设，标志位都被定义在相应的文件 stm32f10x_ppp.h 中。<br>
用户想要进入除错(DEBUG)模式的话，必须在文件 stm32f10x_conf.h 中定义标签 DEBUG。这样会在 SRAM 的外设结构部分创建一个指针。因此我们可以简化除错过程，并且通过转储外设获得来获得所有寄存器的状态。在所有情况下，SPI2 都是一个指向外设 SPI2 首地址的指针。<br>
变量 DEBUG 可以仿照下例定义：

```c
#define DEBUG 1
```

可以初始化 DEBUG 模式与文件 stm32f10x_lib.c 中如下：

```c
#ifdef DEBUG void debug(void)
{
...
#ifdef _SPI2
    SPI2 = (SPI_TypeDef *) SPI2_BASE;
#endif /*_SPI2 */
...
}
#endif /* DEBUG*/
```

**Note：**<br>
1、当用户选择 DEBUG 模式，宏 assert_param 被扩展，同时运行时间检查功能也在固态函数库代码中被激活。<br>
2、进入 DEBUG 模式会增大代码的尺寸，降低代码的运行效率。因此，我们强烈建议仅仅在除错的时候使用相应代码，在最终的应用程序中，删除它们。

### 1.2 基于 C 语言的嵌入式编程规范

#### 1.2.1 源代码的排版

**1、程序块要采用缩进风格编写，建议缩进的空格数为 4 个。**

**2、相对独立的程序块之间、变量说明之后必须加空行，并加注其功能。**

示例：如下例子不符合规范。

```c
if (!valid_ni(ni))
{
... // program code
}
repssn_ind = ssn_data[index].repssn_index;
repssn_ni = ssn_data[index].ni;
```

应如下书写

```c
if (!valid_ni(ni))
{
... // program code
}

/* Initialize the response value */
repssn_ind = ssn_data[index].repssn_index;
repssn_ni = ssn_data[index].ni;
```

**3、较长的语句(>80 字符)要分成多行书写，长表达式要在低优先级操作符处划分新行，操作符放在新行之首，划分出的新行要进行适当的缩进，使排版整齐，语句可读。**

示例：

```c
perm_count_msg.head.len = NO7_TO_STAT_PERM_COUNT_LEN
       + STAT_SIZE_PER_FRAM * sizeof( _UL );

act_task_table[frame_id * STAT_TASK_CHECK_NUMBER + index].occupied
= stat_poi[index].occupied;

act_task_table[taskno].duration_true_or_false
           = SYS_get_sccp_statistic_state( stat_item );

report_or_not_flag = ((taskno < MAX_ACT_TASK_NUMBER)
&& (n7stat_stat_item_valid (stat_item))
&& (act_task_table[taskno].result_data != 0));
```

**4、循环、判断等语句中若有较长的表达式或语句，则要进行适应的划分，长表达式要在低优先级操作符处划分新行，操作符放在新行之首。**

示例：

```c
if ((taskno < max_act_task_number)
&& (n7stat_stat_item_valid (stat_item)))
{
... // program code
}
for (i = 0, j = 0; (i < BufferKeyword[word_index].word_length)
&& (j < NewKeyword.word_length); i++, j++)
{
... // program code
}
for (i = 0, j = 0;
    (i < first_word_length) && (j < second_word_length);
    i++, j++)
{
... // program code
}
```

**5、若函数或过程中的参数较长，则要进行适当的划分。**

示例：

```c
n7stat_str_compare((BYTE *) & stat_object,
                (BYTE *) & (act_task_table[taskno].stat_object),
                sizeof (_STAT_OBJECT));

n7stat_flash_act_duration( stat_item,
frame_id STAT_TASK_CHECK_NUMBER+index,
stat_object );
```

**6、不允许把多个短语句写在一行中，即一行只写一条语句。**

示例：如下例子不符合规范。

```c
rect.length = 0; rect.width = 0;
```

应如下书写

```c
106rect.length = 0;
rect.width = 0;
```

**7、if、for、do、while、case、switch、default 等语句自占一行，且 if、for、do、while 等语句的执行语句部分无论多少都要加括号{}。**

示例：如下例子不符合规范。

```c
if (pUserCR == NULL) return;
```

应如下书写：

```c
if (pUserCR == NULL)
{
    return;
}
```

**8、对齐只使用空格键，不使用 TAB 键。**

说明：以免用不同的编辑器阅读程序时，因 TAB 键所设置的空格数目不同而造成程序布局不整齐，不要使用 BC 作为编辑器合版本。因为 BC 会自动将 8 个空格变为一个 TAB 键，因此使用 BC 合入的版本大多会将缩进变乱。

**9、函数或过程的开始、结构的定义及循环、判断等语句中的代码都要采用缩进风格，case语句下的情况处理语句也要遵从语句缩进要求。**

**10、程序块的分界符(如 C/C++语言的大括号‘{’和‘}’)应各独占一行并且位于同一列，同时与引用它们的语句左对齐。在函数体的开始、类的定义、结构的定义、枚举的定义以及 if、for、do、while、switch、case 语句中的程序都要采用如上的缩进方式。**

示例：如下例子不符合规范。

```c
for (...) {
    ... // program code
}

if (...)
   {
   ... // program code
   }

void example_fun( void )
   {
   ... // program code
   }
```

应如下书写。

```c
for (...)
{
    ... // program code
}

if (...)
{
    ... // program code
}

void example_fun( void )
{
    ... // program code
}
```

**11、在两个以上的关键字、变量、常量进行对等操作时，它们之间的操作符之前、之后或者前后要加空格。进行非对等操作时，如果是关系密切的立即操作符(如->)，后不应加空格。**

说明：采用这种松散方式编写代码的目的是使代码更加清晰。<br>
由于留空格所产生的清晰性是相对的，所以，在已经非常清晰的语句中没有必要再留空格，如果语句已足够清晰则括号内侧(即左括号后面和右括号前面)不需要加空格，多重括号间不必加空格，因为在 C/C++语言中括号已经是最清晰的标志了。<br>
在长语句中，如果需要加的空格非常多，那么应该保持整体清晰，而在局部不加空格。给操作符留空格时不要连续留两个以上空格。

示例：

(1) 逗号、分号只在后面加空格。

```c
int a, b, c;
```

(2) 比较操作符，赋值操作符"="、"+="，算术操作符"+"、"%"，逻辑操作符"&&"、"&"，位域操作符"<<"、"^"等双目操作符的前后加空格。

```c
if (current_time >= MAX_TIME_VALUE)
a = b + c;
a *= 2;
a = b ^ 2;
```

(3) "!"、"~"、"++"、"--"、"&"(地址运算符)等单目操作符前后不加空格。

```c
*p = 'a';        // 内容操作 "*" 与内容之间
flag = !isEmpty; // 非操作 "!" 与内容之间
p = &mem;        // 地址操作 "&" 与内容之间
i++;             // "++","--" 与内容之间
```

(4)"->"、"."前后不加空格。

```c
p->id = pid; // "->" 指针前后不加空格
```

(5) if、for、while、switch 等与后面的括号间应加空格，使 if 等关键字更为突出、明显。

```c
if (a >= b && c > d)
```

**12、一行程序以小于 80 字符为宜，不要写得过长。**

#### 1.2.2 源代码的注释

**1、一般情况下，源程序应尽量简洁明了。**

说明：注释的原则是有助于对程序的阅读理解，注释应准确、易懂、简洁,此外为与各种编译环境的兼容，注释语言宜采用英文注释(为了便于读者理解,本规范中的示例源代码采用中文注释)。

**2、说明性文件(如头文件.h 文件、.icf 文件、.txt 文件等)头部应进行注释，注释必须列出：版权说明、版本号、生成日期、作者、内容、功能、与其它文件的关系、修改日志等。头文件的注释中还应有函数功能简要说明。**

示例：下面这段头文件的头注释比较标准。当然，并不局限于此格式，但上述信息建议要包含在内。

```c
/*************************************************
Copyright (C), 1988-1999, Huawei Tech. Co., Ltd.

// 文件名
File name:

// 作者、版本及完成日期
Author:    Version:    Date:

// 用于详细说明此程序文件完成的主要功能,与其他模块
// 或函数的接口,输出值、取值范围、含义及参数间的控
// 制、顺序、独立或依赖等关系
Description:

// 其它内容的说明
Others:

// 主要函数列表,每条记录应包括函数名及功能简要说明
Function List:
1. ....

// 修改历史记录列表,每条修改记录应包括修改日期、修改
// 者及修改内容简述
History:
1. Date:
   Author:
   Modification:
2. ...
*************************************************/
```

**3、源文件头部应进行注释，列出：版权说明、版本号、生成日期、作者、模块目的/功能、主要函数及其功能、修改日志等。**

示例：下面这段源文件的头注释比较标准，当然，并不局限于此格式，但上述信息建议要包含在内。

```c
/************************************************************
Copyright (C), 1988-1999, Huawei Tech. Co., Ltd.

FileName: test.cpp

Author:    Version:    Date:

// 模块描述
Description:

// 版本信息
Version:

// 主要函数及其功能
Function List:
1. -------

// 历史修改记录
History:
<author>    <time>    <version>    <desc>
David       96/10/12  1.0          Build this moudle
***********************************************************/
```

说明：Description 一项描述本文件的内容、功能、内部各部分之间的关系及本文件与其它文件关系等。History 是修改历史记录列表，每条修改记录应包括修改日期、修改者及修改内容简述。

**4、函数头部应进行注释，列出：函数的目的/功能、输入参数、输出参数、返回值、调用关系(函数、表)等。**

示例：下面这段函数的注释比较标准，当然，并不局限于此格式，但上述信息建议要包含在内。

```c
/*************************************************
Function:       // 函数名称
Description:    // 函数功能、性能等的描述
Calls:          // 被本函数调用的函数清单
Called By:      // 调用本函数的函数清单
Table Accessed: // 被访问的表(此项仅对于牵扯到数据库操作的程序)
Table Updated:  // 被修改的表(此项仅对于牵扯到数据库操作的程序)
Input:          // 输入参数说明,包括每个参数的作
                // 用、取值说明及参数间关系。
Output:         // 对输出参数的说明。
Return:         // 函数返回值的说明
Others:         // 其它说明
*************************************************/
```

**5、注释应与编写代码同步，修改代码同时修改相应的注释，以保证注释与代码的一致性。**

**6、注释的内容要清楚、明了，含义准确，避免产生歧义。**

**7、避免在注释中使用缩写，特别是非常用缩写，命名的标识符除外。**

说明：在使用缩写时或之前，应对缩写进行必要的说明。

**8、注释应与其描述的代码相近，对代码的注释应放在其上方或右方(对单条语句的注释)
相邻位置，不可放在下面，如放于上方则需与其上面的代码用空行隔开。**

示例：如下例子不符合规范。

例 1：

```c
/* get replicate sub system index and net indicator */
repssn_ind = ssn_data[index].repssn_index;
111repssn_ni = ssn_data[index].ni;
```

例 2：

```c
repssn_ind = ssn_data[index].repssn_index;
repssn_ni = ssn_data[index].ni;
/* get replicate sub system index and net indicator */
```

应如下书写

```c
/* get replicate sub system index and net indicator */
repssn_ind = ssn_data[index].repssn_index;
repssn_ni = ssn_data[index].ni;
```

**9、对于所有具有物理含义的变量、常量，如果其命名不是充分自注释的，在声明时都必须加以注释，说明其物理含义。变量、常量、宏的注释应放在其上方相邻位置或右方。**

示例：

```c
/* active statistic task number */
#define MAX_ACT_TASK_NUMBER 1000
#define MAX_ACT_TASK_NUMBER 1000 /* active statistic task number */
```

10、数据结构声明(包括数组、结构、枚举等)，如果其命名不是充分自注释的，必须加以注释。对数据结构的注释应放在其上方相邻位置，不可放在下面。对结构中的每个域的注释放在此域的右方。

示例：可按如下形式说明枚举/数据/联合结构。

```c
/* sccp interface with sccp user primitive message name */
enum SCCP_USER_PRIMITIVE
{
    N_UNITDATA_IND, /* sccp notify sccp user unit data come */
    N_NOTICE_IND,   /* sccp notify user the No.7 network can not
                    */
    /* transmission this message */
    N_UNITDATA_REQ, /* sccp user's unit data transmission
                    request*/
};
```

**11、全局变量要有较详细的注释，包括对其功能、取值范围、哪些函数或过程存取它以及存取时注意事项等的说明。**

示例：

```c
/*************************************************************
* Variable function:
*      The ErrorCode when SCCP translate Global Title failure, as
*follows
* Avalable value:
*      0 - SUCCESS
*      1 - GT Table error
*      2 - GT error
*      Others - no use
* Call ralationship:
*      only function SCCPTranslate() in this modual can modify
*      it,andother module can visit it through call the function
*      GetGTTransErrorCode()
*************************************************************/
BYTE g_GTTranErrorCode;
```

**12、注释与所描述内容进行同样的缩排。**

说明：可使程序排版整齐，并方便注释的阅读与理解。

示例：如下例子，排版不整齐，阅读稍感不方便。

```c
void example_fun( void )
{
/* code one comments */
    CodeBlock One

        /* code two comments */
    CodeBlock Two
}
```

应改为如下布局。

```c
void example_fun( void )
{
    /* code one comments */
    CodeBlock One

    /* code two comments */
    CodeBlock Two
}
```

**13、将注释与其上面的代码用空行隔开。**

示例：如下例子，显得代码过于紧凑。

```c
/* code one comments */
program code one
/* code two comments */
program code two
```

应如下书写

```c
/* code one comments */
program code one

/* code two comments */
program code two
```

**14、对变量的定义和分支语句(条件分支、循环语句等)必须编写注释。**

说明：这些语句往往是程序实现某一特定功能的关键，对于维护人员来说，良好的注释帮助更好的理解程序，有时甚至优于看设计文档。

**15、对于 switch 语句下的 case 语句，如果因为特殊情况需要处理完一个 case 后进入下一个 case 处理，必须在该 case 语句处理完、下一个 case 语句前加上明确的注释。**

说明：这样比较清楚程序编写者的意图，有效防止无故遗漏 break 语句。

示例：

```c
case CMD_UP:
    ProcessUp();
    break;
case CMD_DOWN:
    ProcessDown();
    break;
case CMD_FWD:
    ProcessFwd();
    if (...)
    {
        ...
        break;
    }
    else
    {
        // now jump into case CMD_A
        ProcessCFW_B();
    }
case CMD_A:
    ProcessA();
    break;
case CMD_B:
    ProcessB();
    break;
case CMD_C:
    ProcessC();
    break;
case CMD_D:
    ProcessD();
    break;
...
```

**16、避免在一行代码或表达式的中间插入注释。**

说明：除非必要，不应在代码或表达中间插入注释，否则容易使代码可理解性变差。

**17、通过对函数或过程、变量、结构等正确的命名以及合理地组织代码的结构，使代码成为自注释的。**

说明：清晰准确的函数、变量等的命名，可增加代码可读性，并减少不必要的注释。

**18、在代码的功能、意图层次上进行注释，提供有用、额外的信息。**

说明：注释的目的是解释代码的目的、功能和采用的方法，提供代码以外的信息，帮助读者理解代码，防止没必要的重复注释信息。

示例：如下注释意义不大。

```c
/* if receive_flag is TRUE */
if (receive_flag)
```

而如下的注释则给出了额外有用的信息。

```c
/* if mtp receive a message from links */
if (receive_flag)
```

**19、在程序块的结束行右方加注释标记,以表明某程序块的结束。**

说明：当代码段较长，特别是多重嵌套时，这样做可以使代码更清晰，更便于阅读。

示例：参见如下例子。

```c
if (...)
{
    // program code
    while (index < MAX_INDEX)
    {
        // program code
    } /* end of while (index < MAX_INDEX)*/ // 指明该条 while 语句结束

} /* end of if (...)*/ // 指明是哪条 if 语句结束
```

**20、注释格式尽量统一，建议使用“/* ...... */”。**

**21、注释应考虑程序易读及外观排版的因素，使用的语言若是中、英兼有的，建议多使用中文，除非能用非常流利准确的英文表达。**

说明：注释语言不统一，影响程序易读性和外观排版，出于对维护人员的考虑，建议使用中文。

#### 1.2.3 标识符命名

**1、标识符的命名要清晰、明了，有明确含义，同时使用完整的单词或大家基本可以理解的缩写，避免使人产生误解。**

说明：较短的单词可通过去掉“元音”形成缩写。较长的单词可取单词的头几个字母形成缩写。一些单词有大家公认的缩写。

示例：如下单词的缩写能够被大家基本认可。

|单词      |缩写|
|:------- |:---|
|temp     |tmp |
|flag     |flg |
|statistic|stat|
|increment|inc |
|message  |msg |

**2、命名中若使用特殊约定或缩写，则要有注释说明。**

说明：应该在源文件的开始之处，对文件中所使用的缩写或约定，特别是特殊的缩写，进行必要的注释说明。

**3、自己特有的命名风格，要自始至终保持一致，不可来回变化。**

说明：个人的命名风格，在符合所在项目组或产品组的命名规则的前提下，才可使用。(即命名规则中没有规定到的地方才可有个人命名风格)。

**4、对于变量命名，禁止取单个字符(如 i、j、k...)，建议除了要有具体含义外，还能表明其变量类型、数据类型等，但 i、j、k 作局部循环变量是允许的。**

说明：变量，尤其是局部变量，如果用单个字符表示，很容易敲错(如 i 写成 j)，而编译时又检查不出来，有可能为了这个小小的错误而花费大量的查错时间。

示例：下面所示的局部变量名的定义方法可以借鉴。

```c
int liv_Width;
```

其变量名解释如下：

|变量名|解释|
|:----|:--|
|l    |局部变量(Local)|
|g    |全局变量(Global)|
|i    |数据类型(Interger)|
|v    |变量(Variable)|
|c    |常量(Const)|

**5、命名规范必须与所使用的系统风格保持一致，并在同一项目中统一，比如采用 UNIX 的全小写加下划线的风格或大小写混排的方式，不要使用大小写与下划线混排的方式，用作特殊标识如标识成员变量或全局变量的 m_ 和 g_，其后加上大小写混排的方式是允许的。**

示例：Add_User 不允许，add_user、AddUser、m_AddUser 允许。

**6、除非必要,不要用数字或较奇怪的字符来定义标识符。**

示例：如下命名，使人产生疑惑。

```c
#define _EXAMPLE_0_TEST_
#define _EXAMPLE_1_TEST_
void set_sls00( BYTE sls );
```

应改为有意义的单词命名

```c
#define _EXAMPLE_UNIT_TEST_
#define _EXAMPLE_ASSERT_TEST_
void set_udt_msg_sls( BYTE sls );
```

**7、在同一软件产品内，应规划好接口部分标识符(变量、结构、函数及常量)的命名，防止编译、链接时产生冲突。**

说明：对接口部分的标识符应该有更严格限制，防止冲突。如可规定接口部分的变量与常量之前加上“模块”标识等。

**8、用正确的反义词组命名具有互斥意义的变量或相反动作的函数等。

说明下面是一些在软件中常用的反义词组。

|正义词    |反义词      |
|:--------|:----------|
|add      |remove     |
|begin    |end        |
|create   |destroy    |
|insert   |delete     |
|first    |last       |
|get      |release    |
|increment|decrement  |
|put      |get        |
|add      |delete     |
|lock     |unlock     |
|open     |close      |
|min      |max        |
|old      |new        |
|start    |stop       |
|next     |previous   |
|source   |target     |
|show     |hide       |
|send     |receive    |
|source   |destination|
|cut      |paste      |
|up       |down       |

示例：

```c
int min_sum;
int max_sum;
int add_user( BYTE *user_name );
int delete_user( BYTE *user_name );
```

**9、除了编译开关/头文件等特殊应用，应避免使用_EXAMPLE_TEST_之类以下划线开始和结尾的定义。**
