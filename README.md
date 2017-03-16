

![enter image description here](https://github.com/EasySensors/ButtonSizeNode/blob/master/pics/bttnsz.jpg?raw=true)
## Specification: ##
 - Dimensions 42.2mm x 20.9mm
 - Temperature and humidity sensor Si7021 
 - High Accuracy Temperature Sensor ±0.4 °C (max), –10 to 85 °C
 - Precision Relative Humidity Sensor ± 3% RH (max), 0–80% RH
 - Light sensor BH1750,  spectral responsibility is approximately human eye response.
 - Authentication security - Atmel ATSHA204A Crypto Authentication Chip
 - External JDEC EPROM
 - RFM69-HW (high power version) or HC (low power consumption version) 433 MHz Radio transceiver
 - Battery voltage sensor (via divider)
 - Supply voltage up to 6.5 Volts
 - The Digital and Analog pins are 3.3 volts
 - Battery connector CR2032 240mAh
 

**For Indoor Use Only**
 
 Pin out: 


Arduino Pins|	Description
------------|--------------
A0, A1, A2 |	Available ARDUINO analog GPIO / DIGITAL GPIO
A6 |	Connected to Battery voltage sensor (via divider)
A4 |	Connected to si1132 and bh1750 SDA 
A5 |	Connected to si1132 and bh1750 SCL
A3 |	Connected to  ATSHA204A
D3, D4, D5, D6,D7, D9 |	Available ARDUINO digital GPIO
D8 |	Connected to CS FLASH chip (OTA) M25P40
MISO, MOSI, SCK, RST |	Connected to ISP header
ANT |	RFM69 antenna
Bat+ | Unregulated power up to 6.5 Volts
Gnd | Ground
Scissors line | you cat cut sensors and battery holder if you need just controller and radio


How to use it as home automation (IOT) node controller
------------------------------------------------------


ButtonSizeNode.ino is the Arduino example sketch using [MySensors](https://www.mysensors.org/) API. 

To turn these nodes into home automation network you need at least two Nodes one as a node and the other one as “Gateway Serial” and connect them to a controller (I personally love [Domoticz](https://domoticz.com/)). However, for no-controller setup you can use 3 nodes - one node as gateway, node as relay and last one as switch. No controller needed then.

Things worth mentioning about the  [MySensors](https://www.mysensors.org/) Arduino sketch:
Define which radio we use – here is RFM 69 with frequency 433 MHZ and it is HW type – one of the most powerful RFM 69 radios.  If your radio is RFM69CW - comment out this  #define MY_IS_RFM69HW 
```c++
// Enable and select radio type attached
#define MY_RADIO_RFM69
#define MY_RFM69_FREQUENCY   RF69_433MHZ
#define MY_IS_RFM69HW
```
Define Node address. I prefer to use static addresses and in Hexadecimal since it is easier to identify the node address in  [Domoticz](https://domoticz.com/) devices list after it will be discovered by controller ( [Domoticz](https://domoticz.com/)).
```c++
#define MY_NODE_ID 0xE0
```
However, you can use AUTO instead of the hardcoded number (like 0xE0) though.  [Domoticz](https://domoticz.com/) will automatically assign node ID then.

Define OTA feature. OTA stands for “Over The Air firmware updates”. If your node does not utilize Sleep mode you can send new “firmware” (compiled sketch binary) by air. **Here is the link on how to do it.**
For OTA we use JDEC Flash chip where the node stores new firmware and once it received and control sum (CRC) is correct it reboots and flashes your new code into the node controller. So we define it is "erase type" as 0x2020 here. This define should be as below: 

```c++
#define MY_OTA_FIRMWARE_FEATURE
#define MY_OTA_FLASH_JDECID 0x2020
```
If you would like to use Crypto Authentication to secure your nodes from intruders or interference, you need to enable:

```c++
#define MY_SIGNING_ATSHA204
```
After that, you have to “personalize” all the nodes, which have those, defines enabled. **How to “personalize” nodes with encryption key**

Connect the Node to FTDI USB adaptor, Select Pro Mini 8MHz board and burn the sketch.


> How to “personalize” nodes with encryption key - 3 easy steps as usual )


**Step 1 - generate MY_HMAC_KEY**

Open SecurityPersonalizer.ino from MySensors examples.
 Comment out lines:
```c++
//#define USER_KEY
//#define SKIP_UART_CONFIRMATION
```
Upload sketch to the node and open serial monitor. Copy #define MY_HMAC_KEY 0x blah blah blah

![open serial monitor and copy #define MY_HMAC_KEY 0x blah blah blah](https://github.com/EasySensors/ButtonSizeNode/blob/master/pics/personalization.jpg?raw=true)

**Step 2 - save MY_HMAC_KEY **

Put #define MY_HMAC_KEY  into here 
![Put it here ](https://github.com/EasySensors/ButtonSizeNode/blob/master/pics/personalization1.jpg?raw=true)

**Step 3 - write MY_HMAC_KEY**

unconment
```c++
#define LOCK_CONFIGURATION
#define USER_KEY
#define SKIP_UART_CONFIRMATION
```
Upload sketch to the node 

Done!


----------


The board is created by  [Koresh](https://www.openhardware.io/user/143/projects/Koresh) 