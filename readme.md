# Realtek RTL8811GU USB wifi adapter driver for Raspberry OS
## References
I synthesize knowledge from the following sources to build driver of RTL8811GU chipset for Raspberry OS:
- https://github.com/McMCCRU/rtl8188gu
- https://github.com/morrownr/8821cu
- https://github.com/brektrou/rtl8821CU

## Installation Steps
1. Update the system
   ```
   sudo apt update
   ```
2. Install the required packages
   ```
   sudo apt install -y raspberrypi-kernel-headers bc build-essential dkms git
   ```
3. Clone the source code
   ```
   mkdir ~/build
   cd build
   git clone https://github.com/McMCCRU/rtl8188gu
   git clone https://github.com/morrownr/8821cu
   ```
4. Update the code to build for Raspberry Pi OS
- For 32bit
   ```
   cp 8821cu/raspi32.sh rtl8188gu/
   cd rtl8188gu
   ./raspi32.sh
   ```
- For 64bit
   ```
   cp 8821cu/raspi64.sh rtl8188gu/
   cd rtl8188gu
   ./raspi64.sh
   ```
5. Build driver
   ```
   make -j4
   ```
6. Installing
   ```
   sudo make install
   sudo systemctl start bluetooth.service
   ```
7. If your device is a "multi-state" USB WiFi adapters, try to change USB mode to Wifi adapter
   ```
   sudo usb_modeswitch -KW -v 0bda -p 1a2b
   ```
   Alaways switch USB mode at startup
   - Edit usb_modeswitch rules: ```sudo nano /lib/udev/rules.d/40-usb_modeswitch.rules```
   - Append before the end line LABEL="modeswitch_rules_end" the following:
     ```
     # Realtek 8211GU Wifi
     ATTR{idVendor}=="0bda", ATTR{idProduct}=="1a2b", RUN+="/usr/sbin/usb_modeswitch -K -v 0bda -p 1a2b"
     ```
