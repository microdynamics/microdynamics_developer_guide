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
- 寄存器作为常量处理。他们的命名都由英文字母大写书写。在大多数情况下，他们采用的缩写规范与本用户手册一致。
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
    u16 RESERVED1;
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
#define EXT extern
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

**8、对齐只使用空格键，不使用 TAB 键。**

说明：以免用不同的编辑器阅读程序时，因 TAB 键所设置的空格数目不同而造成程序布局不整齐，不要使用 BC 作为编辑器合版本。因为 BC 会自动将 8 个空格变为一个 TAB 键，因此使用 BC 合入的版本大多会将缩进变乱。

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

说明：注释的原则是有助于对程序的阅读理解，注释应准确、易懂、简洁，此外为与各种编译环境的兼容，注释语言宜采用英文注释(为了便于读者理解，本规范中的示例源代码采用中文注释)。

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
```

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
```

**5、注释应与编写代码同步，修改代码同时修改相应的注释，以保证注释与代码的一致性。**

**6、注释的内容要清楚、明了，含义准确，避免产生歧义。**

**7、避免在注释中使用缩写，特别是非常用缩写，命名的标识符除外。**

说明：在使用缩写时或之前，应对缩写进行必要的说明。

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
*      it,another module can visit it through call the function
*      GetGTTransErrorCode()
*************************************************************/
BYTE g_GTTranErrorCode;
```

**12、注释与所描述内容进行同样的缩排。**

说明：可使程序排版整齐，并方便注释的阅读与理解。

示例：如下例子，排版不整齐，阅读稍感不方便。

```c
void example_fun(void)
{
/* code one comments */
    CodeBlock One

        /* code two comments */
    CodeBlock Two
}
```

应改为如下布局。

```c
void example_fun(void)
{
    /* code one comments */
    CodeBlock One

    /* code two comments */
    CodeBlock Two
}
```

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
```

**14、对变量的定义和分支语句(条件分支、循环语句等)必须编写注释。**

说明：这些语句往往是程序实现某一特定功能的关键，对于维护人员来说，良好的注释帮助更好的理解程序，有时甚至优于看设计文档。

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

**16、避免在一行代码或表达式的中间插入注释。**

说明：除非必要，不应在代码或表达中间插入注释，否则容易使代码可理解性变差。

**17、通过对函数或过程、变量、结构等正确的命名以及合理地组织代码的结构，使代码成为自注释的。**

说明：清晰准确的函数、变量等的命名，可增加代码可读性，并减少不必要的注释。

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

**19、在程序块的结束行右方加注释标记，以表明某程序块的结束。**

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
```

**20、注释格式尽量统一，建议使用“/* ...... */”。**

**21、注释应考虑程序易读及外观排版的因素，使用的语言若是中、英兼有的，建议多使用中文，除非能用非常流利准确的英文表达。**

说明：注释语言不统一，影响程序易读性和外观排版，出于对维护人员的考虑，建议使用中文。

#### 1.2.3 标识符命名

**1、标识符的命名要清晰、明了，有明确含义，同时使用完整的单词或大家基本可以理解的缩写，避免使人产生误解。**

说明：较短的单词可通过去掉“元音”形成缩写。较长的单词可取单词的头几个字母形成缩写。一些单词有大家公认的缩写。

示例：如下单词的缩写能够被大家基本认可。

|单词          |缩写       |单词          |缩写  |单词          |缩写|
|:-------      |:---       |:-------      |:---  |:-------      |:---|
|addition      |add        |float         |flt   |previous      |pre或prev |
|answer        |ans        |frequency     |freq  |payload type  |pt |
|array         |arr        |header        |hdr   |pointer       |ptr |
|average       |avg        |index         |idx   |return code   |rc |
|buffer        |buf或buff  |image         |img   |record        |rcd |
|capture       |cap或capt  |increment     |inc   |receive       |recv |
|check         |chk        |initalize     |init  |result        |res |
|count         |cnt        |iteration     |itr   |return        |ret |
|column        |col        |length        |len   |source        |src |
|control       |ctrl       |memory        |mem   |stack         |stk |
|decode        |dec        |middle        |mid   |statistic     |stat |
|define        |def        |make          |mk    |string        |str |
|delete        |del        |message       |msg   |subtraction   |sub |
|destination   |dst或dest  |multiplication|mul   |table         |tab |
|display       |disp       |number        |num   |temporary     |tmp或temp |
|division      |div        |operand       |opnd  |total         |tot |
|encode        |enc        |optimization  |opt   |time stamp    |ts |
|environment   |env        |operator      |optr  |value         |val |
|error         |err        |packet        |pkt 
|flag          |flg        |positon       |pos 


**2、命名中若使用特殊约定或缩写，则要有注释说明。**

说明：应该在源文件的开始之处，对文件中所使用的缩写或约定，特别是特殊的缩写，进行必要的注释说明。

**3、自己特有的命名风格，要自始至终保持一致，不可来回变化。**

说明：个人的命名风格，在符合所在项目组或产品组的命名规则的前提下，才可使用(即命名规则中没有规定到的地方才可有个人命名风格)。

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

**5、命名规范必须与所使用的系统风格保持一致，并在同一项目中统一，比如采用 UNIX 的全小写加下划线的风格或大小写混排的方式，不要使用大小写与下划线混排的方式，用作特殊标识如标识成员变量或全局变量的 m_ 和 g_，其后加上大小写混排的方式是允许的。**

示例：Add_User 不允许，add_user、AddUser、m_AddUser 允许。

**6、除非必要,不要用数字或较奇怪的字符来定义标识符。**

示例：如下命名，使人产生疑惑。

```c
#define _EXAMPLE_0_TEST_
#define _EXAMPLE_1_TEST_

void set_sls00(BYTE sls);
```

应改为有意义的单词命名

```c
#define _EXAMPLE_UNIT_TEST_
#define _EXAMPLE_ASSERT_TEST_

void set_udt_msg_sls(BYTE sls);
```

**7、在同一软件产品内，应规划好接口部分标识符(变量、结构、函数及常量)的命名，防止编译、链接时产生冲突。**

说明：对接口部分的标识符应该有更严格限制，防止冲突。如可规定接口部分的变量与常量之前加上“模块”标识等。

**8、用正确的反义词组命名具有互斥意义的变量或相反动作的函数等。**

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
int add_user(BYTE *user_name);
int delete_user(BYTE *user_name);
```

**9、除了编译开关/头文件等特殊应用，应避免使用 _EXAMPLE_TEST_ 之类以下划线开始和结尾的定义。**

#### 1.2.4 代码可读性

**1、注意运算符的优先级，并用括号明确表达式的操作顺序，避免使用默认优先级。**

说明：防止阅读程序时产生误解，防止因默认的优先级与设计思想不符而导致程序出错。

示例：下列语句中的表达式

```c
word = (high << 8) | low (1)
if ((a | b) && (a & c))  (2)
if ((a | b) < (c & d))   (3)
```

如果书写为

```c
high << 8 | low
a | b && a & c
a | b < c & d
```

由于

```
high << 8 | low = ( high << 8) | low
a | b && a & c = (a | b) && (a & c)
```

(1)(2)不会出错，但语句不易理解。

```c
a | b < c & d = a | (b < c) & d
```

(3)造成了判断条件出错。

**2、避免使用不易理解的数字，用有意义的标识来替代。涉及物理状态或者含有物理意义的常量，不应直接使用数字，必须用有意义的枚举或宏来代替。**

示例：如下的程序可读性差。

```c
if (Trunk[index].trunk_state == 0)
{
    Trunk[index].trunk_state = 1;
    ... // program code
}
```

应改为如下形式。

```c
#define TRUNK_IDLE 0
#define TRUNK_BUSY 1

if (Trunk[index].trunk_state == TRUNK_IDLE)
{
    Trunk[index].trunk_state = TRUNK_BUSY;
    ... // program code
}
```

**3、源程序中关系较为紧密的代码应尽可能相邻。**

说明：便于程序阅读和查找。

示例：以下代码布局不太合理。

```c
rect.length = 10;
char_poi = str;
rect.width = 5;
```

若按如下形式书写，可能更清晰一些。

```c
rect.length = 10;
rect.width = 5;
char_poi = str;
```

**4、不要使用难懂的技巧性很高的语句，除非很有必要时。**

说明：高技巧语句不等于高效率的程序，实际上程序的效率关键在于算法。

示例：如下表达式，考虑不周就可能出问题，也较难理解。

```c
* stat_poi ++ += 1;
* ++ stat_poi += 1;
```

应分别改为如下。

```c
*stat_poi += 1;
stat_poi++;     // 此二语句功能相当于“ * stat_poi ++ += 1; ”

++ stat_poi;
*stat_poi += 1; // 此二语句功能相当于“ * ++ stat_poi += 1; ”
```

#### 1.2.5 变量、结构

**1、尽可能少地使用公共变量。**

说明：公共变量是增大模块间耦合的原因之一，故应减少没必要的公共变量以降低模块间的耦合度。

**2、仔细定义并明确公共变量的含义、作用、取值范围及公共变量间的关系。**

说明：在对变量声明的同时，应对其含义、作用及取值范围进行注释说明，同时若有必要还应说明与其它变量的关系。

**3、明确公共变量与操作此公共变量的函数或过程的关系，如访问、修改及创建等。**

说明：明确过程操作变量的关系后，将有利于程序的进一步优化、单元测试、系统联调以及代码维护等。这种关系的说明可在注释或文档中描述。

示例：在源文件中，可按如下注释形式说明。

|RELATION|System_Init|Input_Rec|Print_Rec|Stat_Score   |
|:-------|:----------|:--------|:--------|:------------|
|Student |Create     |Modify   |Access   |Access       |
|Score   |Create     |Modify   |Access   |Access/Modify|

注：RELATION 为操作关系。System_Init、Input_Rec、Print_Rec、Stat_Score 为四个不同的函数。Student、Score 为两个全局变量。Create 表示创建，Modify 表示修改，Access 表示访问。其中，函数 Input_Rec、Stat_Score 都可修改变量 Score，故此变量将引起函数间较大的耦合，并可能增加代码测试、维护的难度。

**4、当向公共变量传递数据时，要十分小心，防止赋与不合理的值或越界等现象发生。**

说明：对公共变量赋值时，若有必要应进行合法性检查，以提高代码的可靠性、稳定性。

**5、防止局部变量与公共变量同名。**

说明：若使用了较好的命名规则，那么此问题可自动消除。

**6、严禁使用未经初始化的变量作为右值。**

说明：特别是在 C/C++ 中引用未经赋值的指针，经常会引起系统崩溃。

**7、构造仅有一个模块或函数可以修改、创建，而其余有关模块或函数只访问的公共变量，防止多个不同模块或函数都可以修改、创建同一公共变量的现象。**

说明：降低公共变量耦合度。

**8、使用严格形式定义的、可移植的数据类型，尽量不要使用与具体硬件或软件环境关系密切的变量。**

说明：使用标准的数据类型，有利于程序的移植。

示例：如下例子(在 DOS 下 BC3.1 环境中)，在移植时可能产生问题。

```c
void main()
{
    register int index; // 寄存器变量

    _AX = 0x4000;       // _AX 是 BC3.1 提供的寄存器“伪变量”
    ... // program code
}
```

**9、结构的功能要单一，是针对一种事务的抽象。**

说明：设计结构时应力争使结构代表一种现实事务的抽象，而不是同时代表多种。结构中的各元素应代表同一事务的不同侧面，而不应把描述没有关系或关系很弱的不同事务的元素放到同一结构中。

示例：如下结构不太清晰、合理。

```c
typedef struct STUDENT_STRU
{
    unsigned char name[8];         /* student's name */
    unsigned char age;             /* student's age */
    unsigned char sex;             /* student's sex, as follows */
                                   /* 0 - FEMALE; 1 - MALE */
    unsigned char teacher_name[8]; /* the student teacher's name */
    unisgned char teacher_sex;     /* his teacher sex */
} STUDENT;
```

若改为如下，可能更合理些。

```c
typedef struct TEACHER_STRU
{
    unsigned char name[8];    /* teacher name */
    unisgned char sex;        /* teacher sex, as follows */
                              /* 0 - FEMALE; 1 - MALE */
} TEACHER;

typedef struct STUDENT_STRU
{
    unsigned char name[8];    /* student's name */
    unsigned char age;        /* student's age */
    unsigned char sex;        /* student's sex, as follows */
                              /* 0 - FEMALE; 1 - MALE */
    unsigned int teacher_ind; /* his teacher index */
} STUDENT;
```

**10、不要设计面面俱到、非常灵活的数据结构。**

说明：面面俱到、灵活的数据结构反而容易引起误解和操作困难。

**11、不同结构间的关系不要过于复杂。**

说明：若两个结构间关系较复杂、密切，那么应合为一个结构。

示例：如下两个结构的构造不合理。

```c
typedef struct PERSON_ONE_STRU
{
    unsigned char name[8];
    unsigned char addr[40];
    unsigned char sex;
    unsigned char city[15];
} PERSON_ONE;

typedef struct PERSON_TWO_STRU
{
    unsigned char name[8];
    unsigned char age;
    unsigned char tel;
} PERSON_TWO;
```

由于两个结构都是描述同一事物的，那么不如合成一个结构。

```c
typedef struct PERSON_STRU
{
    unsigned char name[8];
    unsigned char age;
    unsigned char sex;
    unsigned char addr[40];
    unsigned char city[15];
    unsigned char tel;
} PERSON;
```

**12、结构中元素的个数应适中。若结构中元素个数过多可考虑依据某种原则把元素组成不同的子结构，以减少原结构中元素的个数。**

说明：增加结构的可理解性、可操作性和可维护性。

示例：假如认为如上的 _PERSON 结构元素过多，那么可如下对之划分。

```c
typedef struct PERSON_BASE_INFO_STRU
{
    unsigned char name[8];
    unsigned char age;
    unsigned char sex;
} PERSON_BASE_INFO;

typedef struct PERSON_ADDRESS_STRU
{
    unsigned char addr[40];
    unsigned char city[15];
    unsigned char tel;
} PERSON_ADDRESS;

typedef struct PERSON_STRU
{
    PERSON_BASE_INFO person_base;
    PERSON_ADDRESS person_addr;
} PERSON;
```

**13、仔细设计结构中元素的布局与排列顺序，使结构容易理解、节省占用空间，并减少引起误用现象。**

说明：合理排列结构中元素顺序，可节省空间并增加可理解性。

示例：如下结构中的位域排列，将占较大空间，可读性也稍差。

```c
typedef struct EXAMPLE_STRU
{
    unsigned int valid: 1;
    PERSON person;
    unsigned int set_flg: 1;
} EXAMPLE;
```

若改成如下形式，不仅可节省 1 字节空间，可读性也变好了。

```c
typedef struct EXAMPLE_STRU
{
    unsigned int valid: 1;
    unsigned int set_flg: 1;
    PERSON person;
} EXAMPLE;
```

**14、结构的设计要尽量考虑向前兼容和以后的版本升级，并为某些未来可能的应用保留余地(如预留一些空间等)。**

说明：软件向前兼容的特性，是软件产品是否成功的重要标志之一。如果要想使产品具有较好的前向兼容，那么在产品设计之初就应为以后版本升级保留一定余地，并且在产品升级时必须考虑前一版本的各种特性。

**15、留心具体语言及编译器处理不同数据类型的原则及有关细节。**

说明：如在 C 语言中，static 局部变量将在内存“数据区”中生成，而非 static 局部变量将在“堆栈”中生成。这些细节对程序质量的保证非常重要。

**16、编程时，要注意数据类型的强制转换。**

说明：当进行数据类型强制转换时，其数据的意义、转换后的取值等都有可能发生变化，而这些细节若考虑不周，就很有可能留下隐患。

**17、对编译系统默认的数据类型转换，也要有充分的认识。**

示例：如下赋值，多数编译器不产生告警，但值的含义还是稍有变化。

```c
char chr;
unsigned short int exam;

chr = -1;
exam = chr; // 编译器不产生告警,此时 exam 为 0xFFFF 。
```

**18、尽量减少没有必要的数据类型默认转换与强制转换。**

**19、合理地设计数据并使用自定义数据类型，避免数据间进行不必要的类型转换。**

**20、对自定义数据类型进行恰当命名，使它成为自描述性的，以提高代码可读性。注意其命名方式在同一产品中的统一。**

说明：使用自定义类型，可以弥补编程语言提供类型少、信息量不足的缺点，并能使程序清晰、简洁。

示例：可参考如下方式声明自定义数据类型。下面的声明可使数据类型的使用简洁、明了。

```c
typedef unsigned char  BYTE;
typedef unsigned short WORD;
typedef unsigned int   DWORD;
```

下面的声明可使数据类型具有更丰富的含义。

```C
typedef float DISTANCE;
typedef float SCORE;
```

**21、当声明用于分布式环境或不同 CPU 间通信环境的数据结构时，必须考虑机器的字节顺序、使用的位域及字节对齐等问题。**

说明：比如 Intel CPU 与 68360 CPU，在处理位域及整数时，其在内存存放的“顺序”正好相反。

#### 1.2.6 函数、过程

**1、对所调用函数的错误返回码要仔细、全面地处理。**

**2、明确函数功能，精确(而不是近似)地实现函数设计。**

**3、编写可重入函数时，应注意局部变量的使用(如编写 C/C++ 语言的可重入函数时，应使用 auto 即缺省态局部变量或寄存器变量)。**

说明：编写 C/C++ 语言的可重入函数时，不应使用 static 局部变量，否则必须经过特殊处理，才能使函数具有可重入性。

**4、编写可重入函数时，若使用全局变量，则应通过关中断、信号量(即 P、V 操作)等手段对其加以保护。**

说明：若对所使用的全局变量不加以保护，则此函数就不具有可重入性，即当多个进程调用此函数时，很有可能使有关全局变量变为不可知状态。

示例：假设 Exam 是 int 型全局变量，函数 Squre_Exam 返回 Exam 平方值。那么如下函数不具有可重入性。

```c
unsigned int example(int para)
{
    unsigned int temp;

    Exam = para; // ( ** )
    temp = Square_Exam();
    return temp;
}
```

此函数若被多个进程调用的话，其结果可能是未知的，因为当(**)语句刚执行完后，另外一个使用本函数的进程可能正好被激活，那么当新激活的进程执行到此函数时，将使 Exam 赋与另一个不同的 para 值，所以当控制重新回到“temp = Square_Exam( )”后，计算出的 temp 很可能不是预想中的结果。此函数应如下改进：

```c
unsigned int example(int para)
{
    unsigned int temp;

    [申请信号量操作]

    /* 若申请不到“信号量”,说明另外的进程正处
    于给 Exam 赋值并计算其平方过程中(即正在使
    用此信号),本进程必须等待其释放信号后,
    才可继续执行。若申请到信号,则可继续执行,
    但其它进程必须等待本进程释放信号量后,才
    能再用本信号。 */

    Exam = para;
    temp = Square_Exam();

    [释放信号量操作]

    return temp;
}
```

**5、在同一项目组应明确规定对接口函数参数的合法性检查应由函数的调用者负责还是由接口函数本身负责，缺省是由函数调用者负责。**

说明：对于模块间接口函数的参数的合法性检查这一问题，往往有两个极端现象，即：要么是调用者和被调用者对参数均不作合法性检查，结果就遗漏了合法性检查这一必要的处理过程，造成问题隐患。要么就是调用者和被调用者均对参数进行合法性检查，这种情况虽不会造成问题，但产生了冗余代码，降低了效率。

**6、防止将函数的参数作为工作变量。**

说明：将函数的参数作为工作变量，有可能错误地改变参数内容，所以很危险。对必须改变的参数，最好先用局部变量代之，最后再将该局部变量的内容赋给该参数。

示例：下函数的实现不太好。

```c
void sum_data( unsigned int num, int *data, int *sum )
{
    unsigned int count;
    *sum = 0;

    for (count = 0; count < num; count++)
    {
        *sum += data[count]; // sum 成了工作变量,不太好。
    }
}
```

若改为如下，则更好些。

```c
void sum_data(unsigned int num, int *data, int *sum)
{
    unsigned int count;
    int sum_temp;
    sum_temp = 0;

    for (count = 0; count < num; count++)
    {
        sum_temp += data[count];
    }

    *sum = sum_temp;
}
```

**7、函数的规模尽量限制在 200 行以内。**

说明：不包括注释和空格行。

**8、一个函数仅完成一件功能。**

**9、为简单功能编写函数。**

说明：虽然为仅用一两行就可完成的功能去编函数好象没有必要，但用函数可使功能明确化，增加程序可读性，亦可方便维护、测试。

示例：如下语句的功能不很明显。

```c
value = (a > b) ? a : b;
```

改为如下就很清晰了。

```c
int max (int a, int b)
{
    return ((a > b) ? a : b);
}

value = max(a, b);
```

或改为如下。

```c
#define MAX(a, b) (((a) > (b)) ? (a) : (b))

value = MAX(a, b);
```

**10、不要设计多用途面面俱到的函数。**

说明：多功能集于一身的函数，很可能使函数的理解、测试、维护等变得困难。

**11、函数的功能应该是可以预测的，也就是只要输入数据相同就应产生同样的输出。**

说明：带有内部“存储器”的函数的功能可能是不可预测的，因为它的输出可能取决于内部存储器(如某标记)的状态。这样的函数既不易于理解又不利于测试和维护。在  C/C++语言中，函数的 static 局部变量是函数的内部存储器，有可能使函数的功能不可预测，然而，当某函数的返回值为指针类型时，则必须是 STATIC 的局部变量的地址作为返回值，若为 AUTO 类，则返回为错针。

示例：如下函数，其返回值(即功能)是不可预测的。

```c
unsigned int integer_sum(unsigned int base)
{
    unsigned int index;
    static unsigned int sum = 0; // 注意,是 static 类型的。
                                 // 若改为 auto 类型，则函数即变为可预测。

    for (index = 1; index <= base; index++)
    {
        sum += index;
    }

    return sum;
}
```

**12、尽量不要编写依赖于其他函数内部实现的函数。**

说明：此条为函数独立性的基本要求。由于目前大部分高级语言都是结构化的，所以通过具体语言的语法要求与编译器功能，基本就可以防止这种情况发生。但在汇编语言中，由于其灵活性，很可能使函数出现这种情况。

示例：如下是在 DOS 下 TASM 的汇编程序例子。过程 Print_Msg 的实现依赖于 Input_Msg 的具体实现，这种程序是非结构化的，难以维护、修改。

```c
...            // 程序代码
proc Print_Msg // 过程(函数) Print_Msg
    ...        // 程序代码
    jmp LABEL
    ...        // 程序代码
endp

proc Input_Msg // 过程(函数) Input_Msg
    ...        // 程序代码
LABEL:
    ...        // 程序代码
endp
```

**13、避免设计多参数函数，不使用的参数从接口中去掉。**

说明：目的减少函数间接口的复杂度。

**14、非调度函数应减少或防止控制参数，尽量只使用数据参数。**

说明：本建议目的是防止函数间的控制耦合。调度函数是指根据输入的消息类型或控制命令，来启动相应的功能实体(即函数或过程)，而本身并不完成具体功能。控制参数是指改变函数功能行为的参数，即函数要根据此参数来决定具体怎样工作。非调度函数的控制参数增加了函数间的控制耦合，很可能使函数间的耦合度增大，并使函数的功能不唯一。

示例：如下函数构造不太合理。

```c
int add_sub(int a, int b, unsigned char add_sub_flg)
{
    if (add_sub_flg == INTEGER_ADD)
    {
        return (a + b);
    }
    else
    {
        return (a – b);
    }
}
```

不如分为如下两个函数清晰。

```c
int add(int a, int b)
{
    return (a + b);
}

int sub(int a, int b)
{
    return (a – b);
}
```

**15、检查函数所有参数输入的有效性。**

**16、检查函数所有非参数输入的有效性，如数据文件、公共变量等。**

说明：函数的输入主要有两种：一种是参数输入。另一种是全局变量、数据文件的输入，即非参数输入。函数在使用输入之前，应进行必要的检查。

**17、函数名应准确描述函数的功能。**

**18、使用动宾词组为执行某操作的函数命名。如果是 OOP 方法，可以只有动词(名词是对象本身)。**

示例：参照如下方式命名函数。

```c
void print_record(unsigned int rec_ind);
int input_record(void);
unsigned char get_current_color(void);
```

**19、避免使用无意义或含义不清的动词为函数命名。**

说明：避免用含义不清的动词如 process、handle 等为函数命名，因为这些动词并没有说明要具体做什么。

**20、函数的返回值要清楚、明了，让使用者不容易忽视错误情况。**

说明：函数的每种出错返回值的意义要清晰、明了、准确，防止使用者误用、理解错误或忽视错误返回码。

**21、除非必要，最好不要把与函数返回值类型不同的变量，以编译系统默认的转换方式或强制的转换方式作为返回值返回。**

**22、让函数在调用点显得易懂、容易理解。**

**23、在调用函数填写参数时，应尽量减少没有必要的默认数据类型转换或强制数据类型转换。**

说明：因为数据类型转换或多或少存在危险。

**24、避免函数中不必要语句，防止程序中的垃圾代码。**

说明：程序中的垃圾代码不仅占用额外的空间，而且还常常影响程序的功能与性能，很可能给程序的测试、维护等造成不必要的麻烦。

**25、防止把没有关联的语句放到一个函数中。**

说明：防止函数或过程内出现随机内聚。随机内聚是指将没有关联或关联很弱的语句放到同一个函数或过程中。随机内聚给函数或过程的维护、测试及以后的升级等造成了不便，同时也使函数或过程的功能不明确。使用随机内聚函数，常常容易出现在一种应用场合需要改进此函数，而另一种应用场合又不允许这种改进，从而陷入困境。<br>
在编程时，经常遇到在不同函数中使用相同的代码，许多开发人员都愿把这些代码提出来，并构成一个新函数。若这些代码关联较大并且是完成一个功能的，那么这种构造是合理的，否则这种构造将产生随机内聚的函数。

示例：如下函数就是一种随机内聚。

```c
void Init_Var(void)
{
    Rect.length = 0;
    Rect.width = 0; /* 初始化矩形的长与宽 */

    Point.x = 10;
    Point.y = 10;   /* 初始化“点”的坐标 */
}
```

矩形的长、宽与点的坐标基本没有任何关系，故以上函数是随机内聚。应如下分为两个函数：

```c
void Init_Rect(void)
{
    Rect.length = 0;
    Rect.width = 0; /* 初始化矩形的长与宽 */
}

void Init_Point(void)
{
    Point.x = 10;
    Point.y = 10;  /* 初始化“点”的坐标 */
}
```

**26、如果多段代码重复做同一件事情，那么在函数的划分上可能存在问题。**

说明：若此段代码各语句之间有实质性关联并且是完成同一件功能的，那么可考虑把此段代码构造成一个新的函数。

**27、功能不明确较小的函数，特别是仅有一个上级函数调用它时，应考虑把它合并到上级函数中，而不必单独存在。**

说明：模块中函数划分的过，一般会使函数间的接口变得复杂。所以过小的函数，特别是扇入很低的或功能不明确的函数，不值得单独存在。

**28、设计高扇入、合理扇出(小于 7)的函数。**

说明：扇出是指一个函数直接调用(控制)其它函数的数目，而扇入是指有多少上级函数调用它。<br>
扇出过大，表明函数过分复杂，需要控制和协调过多的下级函数。而扇出过小，如总是 1，表明函数的调用层次可能过多，这样不利程序阅读和函数结构的分析，并且程序运行时会对系统资源如堆栈空间等造成压力。函数较合理的扇出(调度函数除外)通常是 3-5。扇出太大，一般是由于缺乏中间层次，可适当增加中间层次的函数。扇出太小，可把下级函数进一步分解多个函数，或合并到上级函数中。当然分解或合并函数时，不能改变要实现的功能，也不能违背函数间的独立性。<br>
扇入越大，表明使用此函数的上级函数越多，这样的函数使用效率高，但不能违背函数间的独立性而单纯地追求高扇入。公共模块中的函数及底层函数应该有较高的扇入。
较良好的软件结构通常是顶层函数的扇出较高，中层函数的扇出较少，而底层函数则扇入到公共模块中。

**29、减少函数本身或函数间的递归调用。**

说明：递归调用特别是函数间的递归调用(如 A->B->C->A)，影响程序的可理解性。递归调用一般都占用较多的系统资源(如栈空间)。递归调用对程序的测试有一定影响。故除非为某些算法或功能的实现方便，应减少没必要的递归调用。

**30、仔细分析模块的功能及性能需求，并进一步细分，同时若有必要画出有关数据流图，据此来进行模块的函数划分与组织。**

说明：函数的划分与组织是模块的实现过程中很关键的步骤，如何划分出合理的函数结构，关系到模块的最终效率和可维护性、可测性等。根据模块的功能图或数据流图映射出函数结构是常用方法之一。

**31、改进模块中函数的结构，降低函数间的耦合度，并提高函数的独立性以及代码可读性、效率和可维护性。优化函数结构时，要遵守以下原则：**

- 不能影响模块功能的实现。
- 仔细考查模块或函数出错处理及模块的性能要求并进行完善。
- 通过分解或合并函数来改进软件结构。
- 考查函数的规模,过大的要进行分解。
- 降低函数间接口的复杂度。
- 不同层次的函数调用要有较合理的扇入、扇出。
- 函数功能应可预测。
- 提高函数内聚。(单一功能的函数内聚最高)

说明：对初步划分后的函数结构应进行改进、优化，使之更为合理。

**32、在多任务操作系统的环境下编程，要注意函数可重入性的构造。**

说明：可重入性是指函数可以被多个任务进程调用。在多任务操作系统中，函数是否具有可重入性是非常重要的，因为这是多个进程可以共用此函数的必要条件。另，编译器是否提供可重入函数库，与它所服务的操作系统有关，只有操作系统是多任务时，编译器才有可能提供可重入函数库。如 DOS 下 BC 和 MSC 等就不具备可重入函数库，因为 DOS 是单用户单任务操作系统。

**33、避免使用 BOOL 参数。**

说明：原因有二。其一是 BOOL 参数值无意义，TURE/FALSE 的含义是非常模糊的，在调用时很难知道该参数到底传达的是什么意思。其二是 BOOL 参数值不利于扩充。还有 NULL 也是一个无意义的单词。

**34、对于提供了返回值的函数，在引用时最好使用其返回值。**

**35、当一个过程(函数)中对较长变量(一般是结构的成员)有较多引用时，可以用一个意义相当的宏代替。**

说明：这样可以增加编程效率和程序的可读性。

示例：在某过程中较多引用 TheReceiveBuffer[FirstSocket].byDataPtr,
则可以通过以下宏定义来代替：

```c
# define pSOCKDATA TheReceiveBuffer[FirstScoket].byDataPtr
```

#### 1.2.7 可测性

**1、在同一项目组或产品组内，要有一套统一的为集成测试与系统联调准备的调测开关及相应打印函数，并且要有详细的说明。**

说明：本规则是针对项目组或产品组的。

**2、在同一项目组或产品组内，调测打印出的信息串的格式要有统一的形式。信息串中至少要有所在模块名(或源文件名)及行号。**

说明：统一的调测信息格式便于集成测试。

**3、编程的同时要为单元测试选择恰当的测试点，并仔细构造测试代码、测试用例，同时给出明确的注释说明。测试代码部分应作为(模块中的)一个子模块，以方便测试代码在模块中的安装与拆卸(通过调测开关)。**

说明：为单元测试而准备。

**4、在进行集成测试/系统联调之前，要构造好测试环境、测试项目及测试用例，同时仔细分析并优化测试用例，以提高测试效率。**

说明：好的测试用例应尽可能模拟出程序所遇到的边界值、各种复杂环境及一些极端情况等。

**5、使用断言来发现软件问题，提高代码可测性。**

说明：断言是对某种假设条件进行检查(可理解为若条件成立则无动作，否则应报告)，它可以快速发现并定位软件问题，同时对系统错误进行自动报警。断言可以对在系统中隐藏很深，用其它手段极难发现的问题进行定位，从而缩短软件问题定位时间，提高系统的可测性。实际应用时，可根据具体情况灵活地设计断言。

示例：下面是 C 语言中的一个断言，用宏来设计的。(其中 NULL 为 0L)

```c
#ifdef _EXAM_ASSERT_TEST_ // 若使用断言测试

void exam_assert(char *file_name, unsigned int line_no)
{
    printf("\n[EXAM]Assert failed: %s, line %u\n", file_name, line_no );
    abort();
}

#define EXAM_ASSERT(condition)
    if (condition) // 若条件成立,则无动作
        NULL;
    else           // 否则报告
        exam_assert(__FILE__, __LINE__)

#else // 若不使用断言测试
#define EXAM_ASSERT(condition) NULL
#endif /* end of ASSERT */
```

**6、用断言来检查程序正常运行时不应发生但在调测时有可能发生的非法情况。**

**7、不能用断言来检查最终产品肯定会出现且必须处理的错误情况。**

说明：断言是用来处理不应该发生的错误情况的，对于可能会发生的且必须处理的情况要写防错程序，而不是断言。如某模块收到其它模块或链路上的消息后，要对消息的合理性进行检查，此过程为正常的错误检查，不能用断言来实现。

**8、对较复杂的断言加上明确的注释。**

说明：为复杂的断言加注释，可澄清断言含义并减少不必要的误用。

**9、用断言确认函数的参数。**

示例：假设某函数参数中有一个指针，那么使用指针前可对它检查，如下：

```c
int exam_fun(unsigned char *str)
{
    EXAM_ASSERT( str != NULL ); // 用断言检查“假设指针不为空”这个条件
    ... //other program code
}
```

**10、用断言保证没有定义的特性或功能不被使用。**

示例：假设某通信模块在设计时，准备提供“无连接”和“连接”这两种业务。但当前的版本中仅实现了“无连接”业务，且在此版本的正式发行版中，用户(上层模块)不应产生“连接”业务的请求，那么在测试时可用断言检查用户是否使用“连接”业务。如下：

```c
#define EXAM_CONNECTIONLESS 0 // 无连接业务
#define EXAM_CONNECTION     1 // 连接业务

int msg_process(EXAM_MESSAGE *msg)
{
    unsigned char service; /* message service class */
}
```

## 2. 工程规范

以下讲解 STM32 工程组织的规范，具体例子请参考 [STM32 工程模板](https://github.com/microdynamics/microdynamics_stm32_template)。

### 2.1 建立工程目录

在项目根目录下分别创建 Build、Documents、Drivers、Libraries、Project、System 和 User 目录。

**1、Build** <br>
Build 目录中包含 Listing 和 Output 两个子目录。Listing 目录存放产生的列表文件，Output 目录中主要存放编译过程中产生的临时文件。

**2、Documents** <br>
Documents 目录中存放工程的说明文档等资料。

**3、Drivers** <br>
Drivers 目录中存放硬件驱动代码。

**4、Libraries** <br>
Libraries 目录中包含 CMSIS 和 FWLib 两个子目录。CMSIS 目录存放 ARM 提供的软件接口标准文件，而 FWLib 目录中存放的是 ST 提供的固件库。

**5、Project** <br>
Project 目录中存放 ARM-MDK 产生的工程文件。

**6、System** <br>
System 目录中存放相关软件实现代码。

**7、User** <br>
User 目录存放用户自己编写的各种源文件。

### 2.2 添加库文件

1、将 STM32F10x_StdPeriph_Driver 中的 inc 和 src 目录复制到 Drivers/FWLib 目录中。

2、将 CoreSupport 文件夹下的文件复制到 Drivers/CMSIS 目录中。

3、将 DeviceSupport 中的文件复制到 Drivers/CMSIS 目录中。

4、将 startup/arm 中的文件复制到 Drivers/CMSIS/startup 目录中。

5、将 STM32F10x_StdPeriph_Template 中的相关文件复制到 User 目录中。

### 2.3 建立工程

1、用 Keil5 创建工程时，将保存工程文件的目录选择为 Project。删掉系统默认生成的 Listings 和 Objects 目录。

2、修改工程名为小写，并与工程目录名保持一致。

3、为工程添加项目组并根据需求添加相应的文件。具体的文件分组和添加请看 [STM32 工程模板](https://github.com/microdynamics/microdynamics_stm32_template)。

### 2.4 工程配置

**1、Target** <br>
选择 `Use MicroLIB` 选项。

**2、Output** <br>
选择 `Create Executable`、`Debug Information`、`Create Hex`、`Browse Information` 等选项，并设置输出文件夹为 Build/Output。

**3、Listing** <br>
选择输出文件夹为 Build/Listing。

**4、C/C++** <br>
在 `Define` 中输入 `STM32F10X_MD, USE_STDPERIPH_DRIVER`。选择 `One ELF Section per Function` 选项，并在 `Include Paths` 中将所有库的头文件路径包含进去。

### 2.5 调试配置

请自行查看相关资料。
