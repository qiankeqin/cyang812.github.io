title: STM32 BSRR BRR ODR 寄存器
date: 2017/8/1 12:35:23
tags:

- STM32

  categories:
- STM32
---

# 一、用法

经常会看到类似如下的宏定义语句，用于对已经初始化后的 IO 口输出高、低电平。

```c
#define SET_BL_HIGH()           GPIOA->BSRR=GPIO_Pin_0 
#define SET_BL_LOW()			GPIOA->BRR=GPIO_Pin_0
```

其作用类似于如下两个库函数，

```c
void GPIO_SetBits(GPIO_Typedef* GPIOx， uint16_t GPIO_Pin)
void GPIO_ResetBits(GPIO_Typedef* GPIOx, uint16_t GPIO_Pin)  
```

而且实际上这两个库函数就是通过修改BSRR，BRR寄存器的值来实现对 IO 口设置的。如下便是输出高电平的函数体：

```c
void GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)
{
  /* Check the parameters */
  assert_param(IS_GPIO_ALL_PERIPH(GPIOx));
  assert_param(IS_GPIO_PIN(GPIO_Pin));

  GPIOx->BSRR = GPIO_Pin;
}
```

 因此，使用宏或者库函数本质上都是一样的。区别在于使用宏更快，而使用函数更灵活。



# 二、解释

BSRR 和 BRR 都是 STM32 系列 MCU 中 GPIO 的寄存器。 BSRR 称为端口位设置/清楚寄存器，BRR称为端口位清除寄存器。

BSRR 低 16 位用于设置 GPIO 口对应位输出高电平，高 16 位用于设置 GPIO 口对应位输出低电平。

BRR 低 16 位用于设置 GPIO 口对应位输出低电平。高 16 位为保留地址，读写无效。

所以理论上来讲，BRR 寄存器的功能和 BSRR 寄存器高 16 位的功能是一样的。也就是说，输出低电平的宏语句，可以有如下两种写法。

```c
#define SET_BL_LOW()			GPIOA->BRR=GPIO_Pin_0
等价于
#define SET_BL_LOW()            GPIOA->BSRR=GPIO_Pin_0 << 16 
```

这么来看的话，其实 BRR 寄存器是比较多余的。而实际上，在最新的 STM32F4 系列 MCU 的 GPIO 寄存器中，已经找不到 BRR 寄存器了，仅保留了 BSRR 寄存器用于实现端口输出高低电平。因此，在 STM32F4 系列 MCU 的库函数中，对 GPIO 口输出高低电平的函数为如下形式：

```c
void HAL_GPIO_WritePin(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, GPIO_PinState PinState)
{
  /* Check the parameters */
  assert_param(IS_GPIO_PIN(GPIO_Pin));
  assert_param(IS_GPIO_PIN_ACTION(PinState));

  if(PinState != GPIO_PIN_RESET)
  {
    GPIOx->BSRR = GPIO_Pin;
  }
  else
  {
    GPIOx->BSRR = (uint32_t)GPIO_Pin << 16U;
  }
}
```

可见，不管是输出高还是输出低，都是对 BSRR 寄存器的操作。



# 三、BSRR、BRR、 ODR 之间的关系

配置 BSRR , BRR 是为了对端口输出进行配置，而 ODR 寄存器也是用于输出数据的寄存器，一个 ODR 寄存器控制了一组（16位）的 GPIO 输出。因此，对 ODR 进行修改也可以到达对 IO 口输出进行配置。

但是，由于对 ODR 寄存器的读写操作必须以 16 位的形式进行。因此，如果使用 ODR 改写数据以控制输出时，须采用“读-改-写”的形式进行。

假设需要对 GPIOA_Pin_6 输出高电平。采用改写 ODR 寄存器的方式时，使用“读-改-写”操作，代码如下：

```c
uint32_t temp;
temp = GPIOA->ODR;
temp = temp | GPIO_Pin_6;
GPIOA->ODR = temp;
```

而使用改写 BSRR 寄存器时，仅需要使用如下语句：

```c
GPIOA->BSRR = GPIO_Pin_6;
```

这是因为在修改 ODR 时，为了确保对端口 6 的修改不会影响到其他端口的输出，需要对端口的原始数据进行保存，之后再对端口 6 的值进行修改，最后再写入寄存器。而对 BSRR 的操作，是写 1 有效，写 0 不改变原状态，因此可以对端口 6 置 1，其他位保持为 0。BSRR 为 1 的位，会修改相应的 ODR 位，从而控制输出电平。

对 BSRR 的操作可以实现原子操作。因此在设置单个 IO 口输出时，使用 BSRR 进行操作会更加方便。

但也有例外的时候，在需要对单个IO口进行 Toggle 操作时（即对当前输出取反输出，当前输出为高则输出低，当前输出低则输出高），官方的库函数就是直接对 ODR 寄存器进行操作的。代码如下：

```c
void HAL_GPIO_TogglePin(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)
{
  /* Check the parameters */
  assert_param(IS_GPIO_PIN(GPIO_Pin));

  GPIOx->ODR ^= GPIO_Pin;
}
```

这是因为，0 和 1 与 1 进行异或操作被取反，0 和 1 与 0 进行异或操作保持原值。如下：

```c
0 ^ 1 = 1
1 ^ 1 = 0

0 ^ 0 = 0
1 ^ 0 = 1
```

