# CSP-14
Common Serial Port 14 pin  

o front of side 1 (host, holes)  
\+ back of side 2 (device, pins)

o v0.11

|  |  |  |  |  |  |  |
|--|--|--|--|--|--|--|
| AN4 | AN3 | MOSI | SCK | MISO | AN2 | AN1 |
| SDA | GND | URX | UCK/AN0 | UTX | VDD | SCL |


Based on 14 pin 1.27 mm pin header  

Common small port to connect common peripheral modules to common MCUs  
(if it's supported by MCU of course)  

SPI, I2C, UART, ADC, PWM  

Host and Device sides are different.  
The reverseablity term is applied to Device side only. This possibility do not dependent on Host.


# Recommendations for Host implementation:   

Priority for PWM placement:  
AN4 AN3 AN2 AN1 AN0  

Priority for ADC placement:  
AN1 AN2 AN0 AN4 AN3  

If pin supports both ADC and PWM place it on AN1 or AN4 if there's a choice


It's safe to fix 4 lines as power lines permanently to reduce pin count requirements
AN2 - GND  
AN3 - VDD  

ESP32 just need to optimally place ADC, 1 or 2 ports  
RP2 have many groups of SPI I2C UART but few adc 2-3 ports  
STM32 is hard to find optimal layout to get more interfaces but possible 2 and more ports  
STM8f103, CH32v003, Atmega can have single CSP-14 port  




# Recommendations for device implementation:

Reverseable design is not required  

All GPIOs are software remappable. So you don't need to think about LCD DC CS pins when designing reverseable Device  


You can safely tie symmetrical pins together to get reverseable design and use just one in software ignoring other in z-state

This applies to power pins. If you need reverse, forget about AN2 and AN3


You can use all comm interfaces at once with no reverseablity. Ensure power on AN2 AN3 will not burn you Device out 

I2C can be software emulated easily. If you don't need excessive usage and need more pins and reverse, choose software emulation of I2C on AN1 AN4


It's possible to software emulate UART tx easily and use hw UART tx line to receive data on STM32 and clones in HDSEL mode


Analog pins placement priorities should be accounted.
