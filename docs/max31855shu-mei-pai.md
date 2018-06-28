### RASPBERRY PI 2 B 及 RASPBERRY PI 3 B pin脚![](/assets/pigpiopin.png)GPIO.setmode\(\)

指定针脚的编号方式，这里有两种选择：

1. GPIO.BOARD

   使用针脚的物理顺序来编号

2. GPIO.BCM

   Broadcom SoC 的编码方式，上图中外侧的编号

### Open Pi SPI function

![](/assets/pi_spi_enable.png)

reboot

```
ls /dev/spi*
```

it should get result as below

```
pi@raspberrypi:~ $ ls /dev/spi*
/dev/spidev0.0  /dev/spidev0.1
```



