# Heltec ESP32 Series anti bootloop dirty quick fix for Linux+Arduino IDE
some devices based on ESP32+SX1262 do suffer from a bootloop when flashing with Arduino IDE + Heltec framework on Linux:
http://community.heltec.cn/t/heltec-v3-infinite-loop-spi-fast-flash-boot/12221
http://community.heltec.cn/t/wireless-stick-lite-v3-reboots-after-uploading-a-simple-code/11982
happens on LoRa V3 devices

the bootloop only happens when flashing with Arduino IDE (by now v2.0.4), under Linux, not with Windows
most likely the esptool.py (v3.3-dev) provided as default in the Heltec's framework for Arduino IDE didn't work as expected, and caused a wrong flash (probably QIO instead of DIO, but not sure)
looking for a quick solution, I found a dirty fix switching from the framework's default esptool.py v3.3-dev to the latest esptool executable, by now v4.5.1

1. install the official arduino ide framework from heltec's wifi kit series site  
2. download the latest esptool executable from https://github.com/espressif/esptool/releases/  
and put in {arduino_workdir}/packages/Heltec-esp32/tools/esptool_py/{version}/  
besides with the provided py file  
3. modify the configuration files accordingly  
{arduino_workdir}/packages/Heltec-esp32/hardware/esp32/0.0.7/platform.txt  
changing the occurrences of esptool.py in "cmd.linux" and "pattern.linux" rows accordingly and putting the correct esptool executable  
  
##beginning of file  
...  
#tools.esptool_py.cmd.linux=esptool.py  
tools.esptool_py.cmd.linux=esptool  
  
##Upload Sketch  
...  
#tools.esptool_py.upload.pattern.linux=python3 "{path}/{cmd}" {upload.pattern_args}  
tools.esptool_py.upload.pattern.linux="{path}/{cmd}" {upload.pattern_args}  
  
##Program Application  
...  
#tools.esptool_py.upload.pattern.linux=python3 "{path}/{cmd}" {upload.pattern_args}  
tools.esptool_py.upload.pattern.linux="{path}/{cmd}" {upload.pattern_args}  
  
##Erase Chip  
...  
#tools.esptool_py.erase.pattern.linux=python3 "{path}/{cmd}" {erase.pattern_args}  
tools.esptool_py.erase.pattern.linux="{path}/{cmd}" {erase.pattern_args}  
  
4. (optional) cleaner solution: create a new directory in  
{arduino_workdir}/packages/Heltec-esp32/tools/esptool_py/{version}/  
reflecting the downloaded esptool version, but you'll had to change the version in  
{arduino_workdir}/packages/Heltec-esp32/hardware/esp32/0.0.7/installed.json  
I didn't do it for lazyness

