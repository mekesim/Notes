# SPI接口

## SPI接口概述

SPI（Serial Peripheral Interface）是由摩托罗拉公司提出的一种同步串行外设接口总线，它可以使MCU与各种外围设备以串行方式进行通信以及交换信息，总线采用3根或4根数据线进行数据传输，常用的是4根线，即两条控制线（芯片选择CS和时钟SCLK）以及两条数据信号线SDI和SDO。

SPI是一种高速、全双工、同步的通信总线。在摩托罗拉公司的SPI技术规范中，数据信号线SDI称为MISO（Master-In-Slave-Out，主入从出），数据信号线SDO称为MOSI（Master-Out-Slave-In，主出从入），控制信号线CS称为SS（Slave-Select，从属选择），将SCLK称为SCK（Serial-Clock，串行时钟）。在SPI通信中，数据是同步进行发送和接收的。数据传输的时钟基于来自主处理器产生的时钟脉冲，摩托罗拉公司没有定义任何通用的SPI时钟规范。

| 缩写 | Moto规范 | 描述 |  |
| :--- | :--- | :--- | :--- |
| SDI | MISO | Master In Slave Out | 主入从出 |
| SDO | MOSI | Master Out Slave In | 主出从入 |
| CS | SS | Slave Select | 从属选择 |
| SCLK | SCK | Serial Clock | 串行时钟 |

SPI是以主从方式工作的，其允许一个主设备和多个从设备进行通信，主设备通过不同的SS信号线选择不同的从设备进行通信。

当器件的CS/SS引脚为低电平时，它与主器件通信。 当它为高电平时，它忽略主器件。 这样可以使多个SPI器件共享相同的MISO，MOSI和CLK线。其典型应用示意图如图所示。![](/assets/spi-connections.png)当主设备选中某一个从设备后，MISO和MOSI用于串行数据的接收和发送，SCK提供串行通信时钟，上升沿发送，下降沿接收。在实际应用中，未选中的从设备的MOSI信号线需处于高阻状态，否则会影响主设备与选中从设备间的正常通信。

使用SPI设备前需了解以下几点：

1. 设备的最大SPI速度。由SPISettings中的第一个参数控制。 如果使用的额定频率为15 MHz，请使用15000000。Arduino将自动使用等于或小于SPISettings使用的最佳速度。 
2. 数据是否在最高有效位（MSB）或最低有效位（LSB）中移位？ 这由第二SPISettings参数控制，MSBFIRST或LSBFIRST。 大多数SPI芯片使用MSB第一个数据顺序。
3. 数据时钟空闲时是高还是低电平？ 在时钟脉冲的上升沿还是下降沿采样？ 组合起来就有四种模式，如下表，由SPISettings中的第三个参数控制。

| **Mode** | **Clock Polarity \(CPOL\)** | **Clock Phase \(CPHA\)** | **Output Edge** | **Data Capture** |
| :--- | :--- | :--- | :--- | :--- |
| SPI\_MODE0 | 0 | 0 | Falling | Rising |
| SPI\_MODE1 | 0 | 1 | Rising | Falling |
| SPI\_MODE2 | 1 | 0 | Rising | Falling |
| SPI\_MODE3 | 1 | 1 | Falling | Rising |

Clock Polarity时钟在高还是低电平时闲置，clock phase数据是在Clock上升沿还是下降沿移进移出的。

![](/assets/max6675data.png)

### begin 函数

begin 函数用于初始化SPI总线，函数原型如下：

```
void SPIClass::begin()
{
      // Set direction register for SCK and MOSI pin.
      // MISO pin automatically overrides to INPUT.
      // When the SS pin is set as OUTPUT, it can be used as
      // a general purpose output port (it doesn't influence
      // SPI operations).

      pinMode(SCK, OUTPUT);
      pinMode(MOSI, OUTPUT);
      pinMode(SS, OUTPUT);

      digitalWrite(SCK, LOW);
      digitalWrite(MOSI, LOW);
      digitalWrite(SS, HIGH);
      // Warning: if the SS pin ever becomes a LOW INPUT then SPI
      // automatically switches to Slave, so the data direction of
      // the SS pin MUST be kept as OUTPUT.
      SPCR |= _BV(MSTR);
      SPCR |= _BV(SPE);
```

setBitOrder的作用是在设置串行数据传输时是先传输低位还是先传输高位，函数有一个type类型的参数bitOrder，有LSBFIRST（最低位在前）和MSBFIRST（最高位在前）两种类型可选。函数无返回值，原型如下：

```
void SPIClass::setBitOrder(uint8_t bitOrder)
{
       if(bitOrder == LSBFIRST)
       {
                 SPCR |= _BV(DORD);
       }
       else
       {
                 SPCR &= ~(_BV(DORD));
       }
}
```

### setClockDivider 函数

setClockDivider 函数的作用是设置SPI串行通信的时钟，通信时钟是由系统时钟分频而得到，分频值可选2、4、8、16、32、64及128，有一个type类型的参数rate，有7种类型，对应7个分频值分别为SPI\_CLOCK\_DIV2、SPI\_CLOCK\_DIV4、SPI\_CLOCK\_DIV8、SPI\_CLOCK\_DIV16、SPI\_CLOCK\_DIV32、SPI\_CLOCK\_DIV64和SPI\_CLOCK\_DIV128。函数默认参数设置是SPI\_CLOCK\_DIV4，设置SPI串行通信时钟为系统时钟的1/4。函数原型如下：

```
void SPIClass::setClockDivider(uint8_t rate)
{
      SPCR = (SPCR & ~SPI_CLOCK_MASK) | (rate & SPI_CLOCK_MASK);
      SPSR = (SPSR & ~SPI_2XCLOCK_MASK) | (rate & SPI_2XCLOCK_MASK);
}
```

### setDataMode函数

setDataMode函数的作用是设置SPI数据模式，由于在SPI通信中没有定义任何通用的时钟规范，所以在具体应用中有的在上升沿采样，有的在下降沿采样，由此SPI存在4种数据模式，如下表所示。

SPI通信数据模式  
说明

| 模式0 | 上升沿采样 | 下降沿置位 | SCK闲置时为0 |
| :--- | :--- | :--- | :--- |
| 模式1 | 上升沿置位 | 下降沿采样 | a |
| 模式2 | 下降沿采样 | 上升沿置位 | SCK闲置时为1 |
| 模式3 | 下降沿置位 | 上升沿采样 | a |

setDataMode函数的type类型的参数mode有4种类型可选，分别是SPI\_MODE0、SPI\_MODE1、SPI\_MODE2和SPI\_MODE3。函数原型如下：

```
void SPIClass::setDataMode(uint8_t mode)
{
      SPCR = (SPCR & ~SPI_MODE_MASK) | mode;
}
```

### 

transfer函数

transfer函数用来传输一个数据，由于SPI是一种全双工、同步的通信总线。所以传输一个数据实际上会发送一个数据，同时接收一个数据。函数的参数为发送的数据值，返回的参数为接收的数据值。函数原型如下：

```
byte SPIClass::transfer(byte _data)

{

SPDR = _data;

while (!(SPSR & _BV(SPIF)));

return SPDR;

}
```

### end函数

end函数停止SPI总线的使用，函数原型如下：

```
void SPIClass::end()
{
      SPCR &= ~_BV(SPE);
}
```



