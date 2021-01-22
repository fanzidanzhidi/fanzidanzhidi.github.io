---
title:  ESP学习笔记（四）
date: 2020-10-22 12:50:07
tags:
   - ESP
categories:
   - ESP学习笔记
---

# 使用ESP32驱动电子墨水屏

[![IEEE1364](https://pic1.zhimg.com/d859faa3b38799fa85b5644f48352063_xs.jpg)](https://www.zhihu.com/people/tao-guan-fu)

[IEEE1364](https://www.zhihu.com/people/tao-guan-fu)

人在江湖身不由己

34 人赞同了该文章

## 未经私信同意禁止转载！

## **一、简介**

显示技术发展很快，高中的时候大家还在学习阴极射线管显示，天天在算电子的轨迹。而如今随处可见LED显示液晶显示。
今天给大家介绍一下电子墨水屏。目前生活中比较常见的是在kindle上使用的，显示效果很像纸，质感很好，不上眼睛。这篇文章就是介绍电子墨水屏，并给大家提供在 ESP32 上使用 MicroPython 驱动墨水屏的demo。

电子墨水屏，又称电子纸，采用“微胶囊电泳显示”技术进行图像显示， 其基本原理是悬浮在液体中的带电纳米粒子受到电场作用而产生迁移。电子纸显示屏是靠反射环境光来显示图案的，不需要背光，即使是在阳光底下，电子纸显示屏依然清晰可视，可视角度几乎达到了 180°。因此，电子纸显示屏非常适合阅读。具有耗低、视角宽、阳光直射下仍可清晰显示等优点，常用于货架标签、工业仪表等显示
应用。电子纸不同于我们常见的显示技术，它的的特点是在掉电状态下可以保持显示状态，这点特性使得它在低刷新低功耗领域有很大的应用潜力，比如电子书、电子广告牌、电子标签等等。当然目前电子墨水屏由以下三个缺点：
1.造价很高，严重限制其广泛应用。
2.支持的显示颜色很少，目前主流的是黑白两色，市场上能看到支持黑白红或者背白黄三色的，但是价格高多了。
3.刷新速率低，无法支持动态显示。

我这次试用的是微雪的1.54in黑白显示模块。在这里放一个带彩色显示的官方图片（微雪应该付我广告费）。

hbqrclhkfdldmkvt

![img](https://pic4.zhimg.com/80/v2-33eaea8070221d8261fcd61f5921115b_1440w.jpg)微雪1.54inc黑白红三色电子墨水屏



电子墨水屏使用的SPI接口，其引脚定义以及和ESP32的连接关系如下

![img](https://pic3.zhimg.com/80/v2-404b7e73844d613f7a439afc66847fca_1440w.jpg)接口定义



## **二、驱动编写**

电子墨水屏的驱动官方提供C语言版本和python版本，我写的是Micropyon版本，主要是在pyhon版本的基础拿掉Micropython中无法使用的图片库，并且做了底层驱动数据格式上的转换，驱动分为两个文件epdif.py和epd1in54.py。

epdif.py中定义了硬件接口和数据写入函数，源码如下

```python
from machine import Pin, SPI
import time

# Pin definition
reset_pin         = Pin(2,Pin.OUT)
dc_pin          = Pin(4,Pin.OUT)
cs_pin          = Pin(5,Pin.OUT)
busy_pin        = Pin(0,Pin.IN)

gpio_sck = Pin(18)
gpio_mosi = Pin(23)
gpio_miso = Pin(19)
    
spi = SPI(-1, baudrate=100000, polarity=1, phase=0, sck=gpio_sck, mosi=gpio_mosi, miso=gpio_miso)

def epd_digital_write(pin, val):
    pin.value(val)

def epd_digital_read(pin):
    return pin.value()

def epd_delay_ms(delaytime):
    time.sleep_ms(delaytime)

def spi_transfer(data):
    spi.write(data)

def epd_init():
    epd_digital_write(cs_pin, 0)
    spi.init(baudrate=200000)
    return 0
```

epd1in54.py中定义了复位设置刷新区域等函数，用于对显示信息进行设置。源码如下：



```python
import epdif
from machine import Pin, SPI
import ujson
import struct
# Display resolution
EPD_WIDTH       = 200
EPD_HEIGHT      = 200

# EPD1IN54 commands
DRIVER_OUTPUT_CONTROL                       = b'\x01'
BOOSTER_SOFT_START_CONTROL                  = b'\x0C'
GATE_SCAN_START_POSITION                    = b'\x0F'
DEEP_SLEEP_MODE                             = b'\x10'
DATA_ENTRY_MODE_SETTING                     = b'\x11'
SW_RESET                                    = b'\x12'
TEMPERATURE_SENSOR_CONTROL                  = b'\x1A'
MASTER_ACTIVATION                           = b'\x20'
DISPLAY_UPDATE_CONTROL_1                    = b'\x21'
DISPLAY_UPDATE_CONTROL_2                    = b'\x22'
WRITE_RAM                                   = b'\x24'
WRITE_VCOM_REGISTER                         = b'\x2C'
WRITE_LUT_REGISTER                          = b'\x32'
SET_DUMMY_LINE_PERIOD                       = b'\x3A'
SET_GATE_TIME                               = b'\x3B'
BORDER_WAVEFORM_CONTROL                     = b' '
SET_RAM_X_ADDRESS_START_END_POSITION        = b'\x44'
SET_RAM_Y_ADDRESS_START_END_POSITION        = b'\x45'
SET_RAM_X_ADDRESS_COUNTER                   = b'\x4E'
SET_RAM_Y_ADDRESS_COUNTER                   = b'\x4F'
TERMINATE_FRAME_READ_WRITE                  = b'\xFF'

class EPD :
    def __init__(self):
        self.reset_pin = epdif.reset_pin
        self.dc_pin = epdif.dc_pin
        self.busy_pin = epdif.busy_pin
        self.width = EPD_WIDTH
        self.height = EPD_HEIGHT
        self.lut = self.lut_full_update

    lut_full_update = [
        0x02, 0x02, 0x01, 0x11, 0x12, 0x12, 0x22, 0x22, 
        0x66, 0x69, 0x69, 0x59, 0x58, 0x99, 0x99, 0x88, 
        0x00, 0x00, 0x00, 0x00, 0xF8, 0xB4, 0x13, 0x51, 
        0x35, 0x51, 0x51, 0x19, 0x01, 0x00
    ]

    lut_partial_update  = [
        0x10, 0x18, 0x18, 0x08, 0x18, 0x18, 0x08, 0x00, 
        0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
        0x00, 0x00, 0x00, 0x00, 0x13, 0x14, 0x44, 0x12, 
        0x00, 0x00, 0x00, 0x00, 0x00, 0x00
    ]

    def digital_write(self, pin, value):
        epdif.epd_digital_write(pin, value)

    def digital_read(self, pin):
        return epdif.epd_digital_read(pin)

    def delay_ms(self, delaytime):
        epdif.epd_delay_ms(delaytime)

    def send_command(self, command):
        self.digital_write(self.dc_pin, 0)
        # the parameter type is list but not int
        # so use [command] instead of command
        epdif.spi_transfer(command)

    def send_data(self, data):
        self.digital_write(self.dc_pin, 1)
        # the parameter type is list but not int
        # so use [data] instead of data
        epdif.spi_transfer(data)

    def init(self, lut):
        if (epdif.epd_init() != 0):
            return -1
        # EPD hardware init start
        self.lut = lut
        self.reset()
        self.send_command(DRIVER_OUTPUT_CONTROL)
        self.send_data(struct.pack("B", (EPD_HEIGHT - 1) & 0xFF))
        self.send_data(struct.pack("B", ((EPD_HEIGHT - 1) >> 8) & 0xFF))
        self.send_data(b'\x00')                     # GD = 0 SM = 0 TB = 0
        self.send_command(BOOSTER_SOFT_START_CONTROL)
        self.send_data(b'\xD7')
        self.send_data(b'\xD6')
        self.send_data(b'\x9D')
        self.send_command(WRITE_VCOM_REGISTER)
        self.send_data(b'\xA8')                     # VCOM 7C
        self.send_command(SET_DUMMY_LINE_PERIOD)
        self.send_data(b'\x1A')                     # 4 dummy lines per gate
        self.send_command(SET_GATE_TIME)
        self.send_data(b'\x08')                     # 2us per line
        self.send_command(DATA_ENTRY_MODE_SETTING)
        self.send_data(b'\x03')                     # X increment Y increment
        self.set_lut(self.lut)      
        # EPD hardware init end
        
        return 0

    def wait_until_idle(self):
        while(self.digital_read(self.busy_pin) == 1):      # 0: idle, 1: busy
            self.delay_ms(100)
##
 #  @brief: module reset.
 #          often used to awaken the module in deep sleep,
 ##
    def reset(self):
        self.digital_write(self.reset_pin, 0)         # module reset
        self.delay_ms(300)
        self.digital_write(self.reset_pin, 1)
        self.delay_ms(300)    

##
 #  @brief: set the look-up table register
 ##
    def set_lut(self, lut):
        self.lut = lut
        self.send_command(WRITE_LUT_REGISTER)
        # the length of look-up table is 30 bytes
        for i in range(0, len(lut)):
            self.send_data(struct.pack("B", self.lut[i]))


##
 #  @brief: put an image to the frame memory.
 #          this won't update the display.
 ##
    def set_frame_memory(self, image, image_width,image_height,x, y):
        #if (image == None or x < 0 or y < 0):
        #    return
        # x point must be the multiple of 8 or the last 3 bits will be ignored
        x = x & 0xF8
        image_width = image_width & 0xF8
        if (x + image_width >= self.width):
            x_end = self.width - 1
        else:
            x_end = x + image_width - 1
        if (y + image_height >= self.height):
            y_end = self.height - 1
        else:
            y_end = y + image_height - 1        
        self.set_memory_area(x, y, x_end, y_end)
        self.set_memory_pointer(x, y)
        self.send_command(WRITE_RAM)
        # send the image data
        #pixels = image_monocolor.load()
        byte_to_send = 0x00
        for j in range(0, y_end - y + 1):
            # 1 byte = 8 pixels, steps of i = 8
            for i in range(0, (x_end - x + 1)/8):
                self.send_data(struct.pack("B", image[i+j*(image_width >>3)]))
                # Set the bits for the column of pixels at the current position.
                #if pixels[i, j] != 0:
                #    byte_to_send |= 0x80 >> (i % 8)
                #if (i % 8 == 7):
                #    self.send_data(struct.pack("B", byte_to_send))
                #    byte_to_send = 0x00
##
 #  @brief: clear the frame memory with the specified color.
 #          this won't update the display.
 ##
    def clear_frame_memory(self, color):
        self.set_memory_area(0, 0, self.width - 1, self.height - 1)
        self.set_memory_pointer(0, 0)
        self.send_command(WRITE_RAM)
        # send the color data
        for i in range(0, self.width / 8 * self.height):
            self.send_data(struct.pack("B", color))

##
 #  @brief: update the display
 #          there are 2 memory areas embedded in the e-paper display
 #          but once this function is called,
 #          the the next action of SetFrameMemory or ClearFrame will 
 #          set the other memory area.
 ##
    def display_frame(self):
        self.send_command(DISPLAY_UPDATE_CONTROL_2)
        self.send_data(b'\xC4')
        self.send_command(MASTER_ACTIVATION)
        self.send_command(TERMINATE_FRAME_READ_WRITE)
        self.wait_until_idle()

##
 #  @brief: specify the memory area for data R/W
 ##
    def set_memory_area(self, x_start, y_start, x_end, y_end):
        self.send_command(SET_RAM_X_ADDRESS_START_END_POSITION)
        # x point must be the multiple of 8 or the last 3 bits will be ignored
        self.send_data(struct.pack("B", (x_start >> 3) & 0xFF))
        self.send_data(struct.pack("B", (x_end >> 3) & 0xFF))
        self.send_command(SET_RAM_Y_ADDRESS_START_END_POSITION)
        self.send_data(struct.pack("B", y_start & 0xFF))
        self.send_data(struct.pack("B", (y_start >> 8) & 0xFF))
        self.send_data(struct.pack("B", y_end & 0xFF))
        self.send_data(struct.pack("B", (y_end >> 8) & 0xFF))

##
 #  @brief: specify the start point for data R/W
 ##
    def set_memory_pointer(self, x, y):
        self.send_command(SET_RAM_X_ADDRESS_COUNTER)
        # x point must be the multiple of 8 or the last 3 bits will be ignored
        self.send_data(struct.pack("B", (x >> 3) & 0xFF))
        self.send_command(SET_RAM_Y_ADDRESS_COUNTER)
        self.send_data(struct.pack("B", y & 0xFF))
        self.send_data(struct.pack("B", (y >> 8) & 0xFF))
        self.wait_until_idle()

##
 #  @brief: After this command is transmitted, the chip would enter the
 #          deep-sleep mode to save power.
 #          The deep sleep mode would return to standby by hardware reset.
 #          You can use reset() to awaken or init() to initialize
 ##
    def sleep(self):
        self.send_command(DEEP_SLEEP_MODE)
        self.wait_until_idle()
```



还编写了一个简单的测试demo：

```py3tb
def test():
    print('start\n')
    epd = epd1in54.EPD()
    epd.init(epd.lut_full_update)
    print('epd init finish \n')
    epd.clear_frame_memory(0xff)
    epd.set_frame_memory(gImage_imag,200,200, 0, 0)
    epd.display_frame()

    epd.delay_ms(2000)
```

我将测试源码和相关参考资料打包放在百度云盘中。
链接：[https://pan.baidu.com/s/1RrKF14Nc21VC-coSittHQQ](https://link.zhihu.com/?target=https%3A//pan.baidu.com/s/1RrKF14Nc21VC-coSittHQQ) 密码：jp6t

## **三、构想**

有了墨水屏和wifi功能，如果添加上简单的按键控制和电池，就具备一个kindle的基本结构了，做一个小号的kindle应该不难的哈。

最后，如果有大佬打赏，让我买个彩色的墨水屏玩玩就好了。