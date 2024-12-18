# cc1101
Connecting CC1101 to Pico.

# The hardware
Mine CC1101 board has 8 pins:
GOD1, GDO2, SCK, MOSI, GDO0, CSN, GND, 3V3. The GOD1 is MISO.

The connection to Pico I made is
GOD1 - GP4
SCK - GP2
MOSI - GP3
GDO0 - GP6
GND - pin38
3V3 - pin36

# The software
I wanted to understand how to control the CC1101 and receive ASK/OOK signals. I'm using Circuitpython. There is a library on https://github.com/unixb0y/CPY-CC1101 but it didn't work for my usecase. But it is an excellent startingpoint.

First somethings I'm missing about the CC1101, maybe because it is so straightforward no one has writen it down. But as a newbie in RF and SPI it took me some time to understand how this should working.
So the CC1101 can be set via registers. Each register is an address you can write a 7/8 - bit value to. Which registers there are and how to use them is writen in the datasheet on the website of Texas Instruments (search for "ti cc1101 datasheet"). There are many registers, but TI made our lifes a bit easier with smartRF studio software. So to fill the register you write a hex-value to a hex-address via the MOSI connection and you read via the MISO.  
You can write or read a single byte and the CC1101 also has an burst mode for write and read.  

You can test your connection by reading out the values in PARTNUM = 0xF0 and VERSION = 0xF1 registers or write a value to SYNC0 and SYNC1 and read them out.  
PATABLE controls power and is a 8-bytes table. Writen to in burst mode. In singlebyte mode only the first byte can be reached.


So the steps to follow for sending or recieving:
1 - init your connections
2 - reset CC1101 chip (send value 0x00 to 0x30 (=SRES))
3 - calculate en set frequency
4 - write PATABLE
5 - flush FIFO's
6 - fill the registers with the correct values (which values you can determine with smartRF studio).
7 - send or receive data
 
