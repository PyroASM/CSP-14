# CSP-14  
Common Serial Port 14 pin  

o front of side 1 (host, holes)  
\+ back of side 2 (device, pins)  


v0.01 The idea  
Based on 14 pin 1.27 mm pin header  

Common small port to connect common peripheral modules to common MCUs  
(if it's supported by MCU of course)  

SPI, I2C, UART, ADC, PWM

Host and Device sides are different 

v1-v8 i didn't realised that two hosts will be connected by straight cable as is pin to pin, with no any reversing or replacement  


Reverse safe design, to avoid fatal connection errors  

+ pros  
+ swap between USART and SPI on reverse connection  
+ fully symmetrical   
+ safe to reverse  

- No place for i2c  
- Ideas to overlap i2c somewhere  

o 
GND PWM STX1 SCK1 URX2 ADC VCC
VCC ADC SRX1 UCK2 UTX2 PWM GND

+ 
GND PWM SRX1 SCK1 UTX2 ADC VCC
VCC ADC STX1 UCK2 URX2 PWM GND

+ back of side 2 (SPI USART I2C)
GND SCL SRX1 SCK1 UTX2 SDA VCC
VCC SDA STX1 UCK2 URX2 SCL GND


+ back of side 2 (hd SPI, hd UART)
GND PWM STX1 SCK1 PIN  ADC VCC
VCC ADC PIN  PIN  UTX2 PWM GND

+ back of side 2 (hd SPI, fd UART, I2C)
GND PWM STX1 SCK1 UTX2 ADC VCC
VCC ADC SDA  SCL  URX2 PWM GND

+ back of side 2 (fd SPI, hd uart, I2C)
GND PWM STX1 SCK1 SDA ADC VCC
VCC ADC SRX1 SCL  UTX PWM GND

+ back of side 2 (shared 4 half duplex SPI)
GND PWM STX1 SCK1 CS3  ADC VCC
VCC ADC CS1  CS2  CS4  PWM GND

+ back of side 2 (lcd on SPI, I2C touch, hd uart)
GND BL  SRX1 SCK1 CS/R ADC VCC
VCC DC  SDA  SCL  TE/U PWM GND



v0.02
o fully reverseable, 8 data pins
CS  MOSI GND VCC GND RX CK
SCK MISO GND VCC GND TX CS

o GND VCC remap, 12 data pins, main side after cfg
ADC ADC MOSI SCK MISO PWM SCL
SDA PWM  URX GND UTX  ADC VCC

o GND VCC remap, 12 data pins, rev side after cfg
VCC ADC N/A  GND N/A  PWM N/A
N/A PWM  URX CK  UTX  ADC ADC
(N/A or GPIO/SOFTWARE EMULATED)

+ GND VCC remap, 12 data pins
ADC ADC MOSI SCK MISO PWM SCL
SDA PWM  URX GND UTX  ADC VCC
(SCL,SDA must have PULLUP to VCC to detect connector side)





v3a

SDA ADC MOSI CLK MISO PWM VCC
SCL PWM  URX CLK UTX  ADC GND


v3b basic version o
MISO PWM SDA GND ADC SCK MOSI
UTX  VCC ADC GND SCL PWM URX



v3
PWM MISO SDA GND ADC MOSI SCK
VCC UTX  ADC GND SCL URX  PWM


v3-4
PWM MISO SDA SCK ADC MOSI GND
GND UTX  ADC NC  SCL URX  PWM





Use I2C get rid of VCC
Use switchable VCC and provide low power from gpio 

o v4

GND PWM MOSI SCK MISO ADC SCL
SDA ADC URX  UCK UTX  PWM GND

+ v4 (just TX-RX swap, SPI is slave here)
GND PWM MOSI SCK MISO ADC SCL
SDA ADC UTX  UCK URX  PWM GND

+ v4 SPI Flash 
GND CS  MOSI SCK MISO CS  -
 -  CS  ALT  VDD ALT  CS GND






Various size pins, compatible pinout 

o v5
PWM GND MOSI SCK MISO SCL ADC
ADC SDA URX  UCK UTX  GND PWM

csp-10
o v5
GND MOSI SCK MISO SCL
SDA URX  UCK UTX  GND

csp-18
o v5
PWM PWM GND MOSI SCK MISO SCL ADC ADC
ADC ADC SDA URX  UCK UTX  GND PWM PWM





- No reverse 
+ I2C and Power preset 

Reverse protection using mosfet

+ Firstly implemented module 
- too difficult design for MCU and module developers

o v6
PWM_A VDD MOSI SCK MISO SCL ADC_A
ADC_B SDA URX  UCK UTX  GND PWM_B

For HOST AT LEAST VDD should be switchable off
to detect wrong orientation
using pull-ups which in case of reversed
connection will be pulled down 

Host classes:
UH - Unsafe Host (VDD is always 3.3v)
SH - Safe Host (VDD switched off by default)
RH - Reversing Supported Host (VDD and GND can exchange polarity AND VDD can be switched off)

device classes:
UD - Unsafe Device
RP - Reverse Protected
RI - Reverse Invariant (led, lcd, passive devices)
LP - Low Power. Powered using one or more 20mA GPIO. VDD is NOT connected or invariant to device functionality






+ additional adc 
o v0.7
ADC_C
PWM_A VDD MOSI SCK   MISO SCL ADC_A
ADC_B SDA URX  UCK   UTX  GND PWM_B
               ADC_C





trying to get reverseable design for at least one communication interface at a time
+ allow any analog pins combination, recommend the placement of ADC and PWM 
o v0.8
AN1 SDA MISO AN0 VDD SCL  AN3
AN4 SCK URX  GND UTX MOSI AN2
               


finally got it, two hosts can be connected freely with straight cable 
except UART which need tx/rx swap 
return to first version layout mostly 
o v0.9
AN1 VDD MOSI SCK   MISO SCL AN3
AN4 SDA URX  UCK   UTX  GND AN2



+ allow connection of symmetrical pins on Device side for power and data reversing

o v0.10
SCL VDD MOSI SCK MISO AN3 AN1
SDA GND URX  UCK UTX  AN4 AN2
             AN0
