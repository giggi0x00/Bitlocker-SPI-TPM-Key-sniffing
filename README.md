Bootloader and TPM speaks using different protocol such as LPC, SPI, I2C let's see how it works using SPI.

This project was ispired by  : https://pulsesecurity.co.nz/articles/TPM-sniffing. Thanks!

The SPI protocol works basically on top of 4 signals, SPI CLK, SPI MISO, SPI MOSI and SPI CS, the data goes in and out using MISO and MOSI and the slave is selected by pulling down the signal SPI CS.

SPI CS   <--------> SPI CS
SPI MOSI ---------> SPI MOSI
SPI MISO <--------- SPI MISO
SPI CLK  <--------> SPI CLK


The communication with the bootloader evolved 4 lines, what we have to do is just sniffing the trafficing over the data lines.

First of all, we have to identify the TPM installed on the main board, in my case:


![alt text](https://github.com/giggi0x00/Bitlocker-SPI-TPM-Key-sniffing/blob/main/2020-12-07_20-15.png?raw=true)
