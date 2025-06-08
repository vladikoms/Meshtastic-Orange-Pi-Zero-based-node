It has been tested on the Orange Pi Zero 512MB platform (Armbian v25.8 rolling for Orange Pi Zero running Armbian Linux 6.12.30-current-sunxi) and RFM95 radio module

Connections
-----------
- RFM95  - Orange Pi Zero

- 3.3V  - 3.3V (header pin #17)
- GND  - GND (pin #25)
- MOSI  - MOSI (pin #19)
- MISO  - MISO (pin #21)
- SCK   - CLK (pin #23)
- DIO0  - GPIO2 (pin #22)
- NSS   - GPIO13 (pin #24)
- RST	  - GPIO18 (pin #18)

Enable SPI interface
--------------------

Edit **armbianEnv.txt** file
```
sudo nano /boot/armbianEnv.txt
```
Add to **overlays** string: spi-spidev, spi-add-cs1  
And add strings:
```
param_spidev_spi_bus=1
param_spidev_spi_cs=0
```
armbianEnv.txt file:
```
verbosity=1
bootlogo=false
console=both
disp_mode=1920x1080p60
overlay_prefix=sun8i-h3
overlays=spi-add-cs1 spi-spidev usbhost2 usbhost3 tve
rootdev=UUID=9e32abbc-8e17-4652-84e6-cb1f194ab4e4
rootfstype=ext4
param_spidev_spi_bus=1
param_spidev_spi_cs=0
usbstoragequirks=0x2537:0x1066:u,0x2537:0x1068:u
```
Check SPI
```
ls /dev | grep spi
```
If you see 
```
spidev1.0
```
then everything is OK

Downloading & Installing Meshtastic
------------------------------------
```
echo 'deb http://download.opensuse.org/repositories/network:/Meshtastic:/beta/Debian_12/ /' | sudo tee /etc/apt/sources.list.d/network:Meshtastic:beta.list
curl -fsSL https://download.opensuse.org/repositories/network:Meshtastic:beta/Debian_12/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/network_Meshtastic_beta.gpg > /dev/null
sudo apt update
sudo apt install meshtasticd
```

Configuration
-------------

Edit **/etc/meshtasticd/config.yaml** file

```
nano /etc/meshtasticd/config.yaml
```
```
  Module: RF95  # Elecrow Lora RFM95 IOT https://www.elecrow.com/lora-rfm95-iot-board-for-rpi.html
  Reset: 18
  CS: 13
  IRQ: 2

### Specify the SPI device to use in /dev/. Defaults to spidev0.0
### Some devices, like the pinedio, may require spidev0.1 as a workaround.
  spidev: spidev1.0

Webserver:
  Port: 443 # Port for Webserver & Webservices
  RootPath: /usr/share/meshtasticd/web # Root Dir of WebServer
  SSLKey: /etc/meshtasticd/ssl/private_key.pem # Path to SSL Key, generated if not present
  SSLCert: /etc/meshtasticd/ssl/certificate.pem # Path to SSL Certificate, generated if not present

### Don`t Forget add MAC Address!

  MACAddress: AA:BB:CC:DD:EE:FF
```

Start the service
-------------

```
sudo systemctl start meshtasticd
```
