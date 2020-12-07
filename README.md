Bootloader and TPM Communicate using different protocols such as lpc, spi, I2C. Let's see how it works when communication takes place via this SPI protocol.

This project was ispired by  : https://pulsesecurity.co.nz/articles/TPM-sniffing. Thanks!

The SPI protocol is based on 4 signals, SPI CLK, SPI MISO, SPI MOSI and SPI CS. The dataes go in and out using MISO and MOSI and the slave is selected by pulling down the signal SPI CS.

SPI CS   <--------> SPI CS <br/>
SPI MOSI ---------> SPI MOSI<br/>
SPI MISO <--------- SPI MISO<br/>
SPI CLK  <--------> SPI CLK<br/>
<br/>

Commnication with the bootloader uses 4 lines. What we have to do it is just sniffing the traffic that takes place on these data lines.

First of all, we have to identify the TPM installed on the main board, in my case:


![alt text](https://github.com/giggi0x00/Bitlocker-SPI-TPM-Key-sniffing/blob/main/2020-12-07_20-15.png?raw=true)

Pretty small. :)

<br/>

And so even if you don't know the specific TPM in front of you just remember that the pinout is a standard.
https://trustedcomputinggroup.org/wp-content/uploads/PC-Client-Specific-Platform-TPM-Profile-for-TPM-2p0-vp04p34_pubrev.pdf .<br/>

Then solder some wire to the relative pins and start sniffing using a Logical Analyzer with a frequency of at least 4 times in order to recostruct the original signal.

![alt text](https://github.com/giggi0x00/Bitlocker-SPI-TPM-Key-sniffing/blob/main/2020-12-07_20-31.png?raw=true)
</br>


The command used by the bootloader to read back the VMK is "80h" and the address from where the data come out is the "0024h" TPM_DATA_FIFO_0, so what we have to do in order to get the key is looking a these specific command and address.
The VMK header is “2C 00  00  00  01   00   00    00   03  20   00  00".
</br>
![alt text](https://github.com/giggi0x00/Bitlocker-SPI-TPM-Key-sniffing/blob/main/2020-12-07_20-46.png?raw=true)
</br>














