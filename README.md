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
6 - fill the registers with the correct values (which values you can determine with smartRF studio)  
7 - send or receive data  

# Info from DN022
In application note DN022 are recommondations for ASK/OOK.  
AGCCTRL2 = 0x03 to 0x07  
AGCCTRL1 = 0x00  
AGCCTRL0 = 0x91 or 0x92  


# Registers
Strobe Registers/commands:  
    SRES = 0x30  
    SFSTXON = 0x31  
    SXOFF = 0x32  
    SCAL = 0x33  
    SRX = 0x34  
    STX = 0x35  
    SIDLE = 0x36  
    SWOR = 0x38  
    SPWD = 0x39  
    SFRX = 0x3A  
    SFTX = 0x3B  
    SWORRST = 0x3C  
    SNOP = 0x3D  
    
Configuration Registers Overview  
    IOCFG2 = 0x00  # GDO2 output pin configuration  
    IOCFG1 = 0x01  # GDO1 output pin configuration  
    IOCFG0 = 0x02  # GDO0 output pin configuration  
    FIFOTHR = 0x03  # RX FIFO and TX FIFO thresholds  
    SYNC1 = 0x04  # Sync word, high byte  
    SYNC0 = 0x05  # Sync word, low byte  
    PKTLEN = 0x06  # Packet length  
    PKTCTRL1 = 0x07  # Packet automation control  
    PKTCTRL0 = 0x08  # Packet automation control  
    ADDR = 0x09  # Device address  
    CHANNR = 0x0A  # Channel number  
    FSCTRL1 = 0x0B  # Frequency synthesizer control  
    FSCTRL0 = 0x0C  # Frequency synthesizer control  
    FREQ2 = 0x0D  # Frequency control word, high byte  
    FREQ1 = 0x0E  # Frequency control word, middle byte  
    FREQ0 = 0x0F  # Frequency control word, low byte  
    MDMCFG4 = 0x10  # Modem configuration  
    MDMCFG3 = 0x11  # Modem configuration  
    MDMCFG2 = 0x12  # Modem configuration  
    MDMCFG1 = 0x13  # Modem configuration  
    MDMCFG0 = 0x14  # Modem configuration  
    DEVIATN = 0x15  # Modem deviation setting  
    MCSM2 = 0x16  # Main Radio Control State Machine configuration  
    MCSM1 = 0x17  # Main Radio Control State Machine configuration  
    MCSM0 = 0x18  # Main Radio Control State Machine configuration  
    FOCCFG = 0x19  # Frequency Offset Compensation configuration  
    BSCFG = 0x1A  # Bit Synchronization configuration  
    AGCTRL2 = 0x1B  # AGC control  
    AGCTRL1 = 0x1C  # AGC control  
    AGCTRL0 = 0x1D  # AGC control  
    WOREVT1 = 0x1E  # High byte Event 0 timeout  
    WOREVT0 = 0x1F  # Low byte Event 0 timeout  
    WORCTRL = 0x20  # Wake On Radio control  
    FREND1 = 0x21  # Front end RX configuration  
    FREND0 = 0x22  # Front end TX configuration  
    FSCAL3 = 0x23  # Frequency synthesizer calibration  
    FSCAL2 = 0x24  # Frequency synthesizer calibration  
    FSCAL1 = 0x25  # Frequency synthesizer calibration  
    FSCAL0 = 0x26  # Frequency synthesizer calibration  
    RCCTRL1 = 0x27  # RC oscillator configuration  
    RCCTRL0 = 0x28  # RC oscillator configuration  
    FSTEST = 0x29  # Frequency synthesizer calibration control  
    PTEST = 0x2A  # Production test  
    AGCTEST = 0x2B  # AGC test  
    TEST2 = 0x2C  # Various test settings  
    TEST1 = 0x2D  # Various test settings  
    TEST0 = 0x2E  # Various test settings  

Status Registers Overview
    PARTNUM = 0x30  # Part number for CC1101  
    VERSION = 0x31  # Current version number  
    FREQEST = 0x32  # Frequency Offset Estimate  
    LQI = 0x33  # Demodulator estimate for Link Quality  
    RSSI = 0x34  # Received signal strength indication  
    MARCSTATE = 0x35  # Control state machine state  
    WORTIME1 = 0x36  # High byte of WOR timer  
    WORTIME0 = 0x37  # Low byte of WOR timer  
    PKTSTATUS = 0x38  # Current GDOx status and packet status  
    VCO_VC_DAC = 0x39  # Current setting from PLL calibration module  
    TXBYTES = 0x3A  # Underflow and number of bytes in the TX FIFO  
    RXBYTES = 0x3B  # Overflow and number of bytes in the RX FIFO  
    RCCTRL1_STATUS = 0x3C  # Last RC oscillator calibration result  
    RCCTRL0_STATUS = 0x3D  # Last RC oscillator calibration result  

PATABLE Access  
    PATABLE = 0x3E    
