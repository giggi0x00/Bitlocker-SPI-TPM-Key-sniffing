Bootloader and TPM Communicate use different protocols such as lpc, spi, I2C. Let's see how it works when communication takes place via this SPI protocol.

This project was ispired by  : https://pulsesecurity.co.nz/articles/TPM-sniffing. Thanks!

The SPI protocol is based on 4 signals, SPI CLK, SPI MISO, SPI MOSI and SPI CS. The data go in and out using MISO and MOSI and the slave is selected by pulling down the signal SPI CS.

SPI CS   <--------> SPI CS <br/>
SPI MOSI ---------> SPI MOSI<br/>
SPI MISO <--------- SPI MISO<br/>
SPI CLK  <--------> SPI CLK<br/>
<br/>

So the commnication between the bootloader and TPM uses 4 lines. What we have to do it is just sniffing the traffic that takes place on these data lines.

First of all, we have to identify the TPM installed on the main board, in my case:


![alt text](https://github.com/giggi0x00/Bitlocker-SPI-TPM-Key-sniffing/blob/main/2020-12-07_20-15.png?raw=true)

Pretty small. :)

<br/>

And so even if you don't know the specific TPM in front of you just remember that the pinout is a standard.
https://trustedcomputinggroup.org/wp-content/uploads/PC-Client-Specific-Platform-TPM-Profile-for-TPM-2p0-vp04p34_pubrev.pdf .<br/>

Then solder some wire to the relative pins and start sniffing using a Logical Analyzer with a frequency of at least 4 times in order to recostruct the original signal.

![alt text](https://github.com/giggi0x00/Bitlocker-SPI-TPM-Key-sniffing/blob/main/2020-12-07_20-31.png?raw=true)
</br>


The command used by the bootloader to read back the VMK is "80h" from the address "0024h" TPM_DATA_FIFO_0, so what we have to do in order to get the key is looking a these specific command and address.
</br>
Starting from the VMK header is “2C 00  00  00  01   00   00    00   03  20   00  00" and write down 32 bytes.
</br>
![alt text](https://github.com/giggi0x00/Bitlocker-SPI-TPM-Key-sniffing/blob/main/2020-12-07_20-46.png?raw=true)
</br>

Read 32 bytes and you will get the key.
Now you are ready to decrypt the harddisk. 
Mount it and use:</br>
sudo dislocker-metadata -V /dev/sda2 > dislocker-metadata.txt </br>
then: </br>
python bitlocker_fvek_decrypt.py -f dislocker-metadata.txt -k vmk.bin where vmk.bin is the key extracted from the bus.
</br>

$ python bitlocker_fvek_decrypt.py -f dislocker-metadata.txt -k vmk.bin </br>

</br> 
  ___ _ _   _            _             _____   _____ _  __  ___                       _  
 | _ |_) |_| |   ___  __| |_____ _ _  | __\ \ / / __| |/ / |   \ ___ __ _ _ _  _ _ __| |_
 | _ \ |  _| |__/ _ \/ _| / / -_) '_| | _| \ V /| _|| ' <  | |) / -_) _| '_| || | '_ \  _|
 |___/_|\__|____\___/\__|_\_\___|_|   |_|   \_/ |___|_|\_\ |___/\___\__|_|  \_, | .__/\__|
                                                                            |__/|_|      

</br>


BitLocker FVEK Decrypt v0.2 by Matthias Deeg - SySS GmbH</br>

Decrypts encrypted BitLocker Full Volume Encryption Keys (FVEK)</br>
---
[+] Extracted nonce: </br>
    409b87a369dbd501d9010000 </br>
[+] Extracted MAC: </br>
    12c7b1c759e76ad88c3efd451a0fc945 </br>
[+] Extracted payload:</br>
    fd82fcf27ded951a2327e2e9d00b9ba0a3245f949bc53163bcc26088531215d17be6f99794d3fcfeb22bb41e</br>
[+] Decrypted Full Volume Encryption Key (FVEK):</br>
    561bd26ca61fa3fb3445994b0f62649ce86e90085c0ff25dda57be61c2667cb6</br>
[+] Created FVEK file 'fvek.bin' for use with dislocker</br>

</br>

Here we are...! @giggi0x000








