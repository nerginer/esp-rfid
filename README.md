# Update: Please be patience while we are switching to new development environment, if you are new to esp-rfid project please get latest stable source files from the github's [release](https://github.com/omersiar/esp-rfid/releases) facility and get instructions from the downloaded "readme.md" file.




# ESP RFID - Access Control with ESP8266, RC522
Access Control demonstration using a cheap MFRC522 RFID Hardware or Wiegand RFID readers and Espressif's ESP8266 Microcontroller. This is a community driven project.

This is the **development branch** of the project, if you are looking for stable, production ready version of esp-rfid please use **stable** branch.

[![Chat at https://gitter.im/esp-rfid/Lobby](https://badges.gitter.im/esp-rfid.svg)](https://gitter.im/esp-rfid/Lobby) Join community chat

![IP](https://github.com/omersiar/esp-rfid/raw/development/demo/index.png)
![SP](https://github.com/omersiar/esp-rfid/raw/development/demo/settings.png)
![UP](https://github.com/omersiar/esp-rfid/raw/development/demo/users.png)
![LP](https://github.com/omersiar/esp-rfid/raw/development/demo/logs.png)

## Features
### For Users
* Minimal effort for setting up your Access Control system
* Capable of managing up to 1.000 Users (even more is possible)
* Great for Maker Spaces, Labs, Schools, etc
* Cheap to build and easy to maintain
### For Tinkerers
* Open Source (minimum amount of hardcoded variable, this means more freedom)
* Using WebSocket protocol to exchange data between Hardware and Web Browser
* Data is encoded as JSON object
* Records are Timestamped (Time synced from a NTP Server)
* MQTT enabled
* Bootstrap, jQuery, FooTables for beautiful Web Pages for both Mobile and Desktop Screens
* Thanks to ESPAsyncWebServer Library communication is Asyncronous

## Getting Started
This project still in its development phase. New features (and also bugs) are introduced often and some functions may become deprecated. Please feel free to comment or give feedback.
* Latest development version is v0.3beta
* Latest released compiled binaries are from v0.3beta and can be found in directory [/compiledbin](https://github.com/omersiar/esp-rfid/tree/master/compiledbin)
* See [Known Issues](https://github.com/omersiar/esp-rfid#known-issues) before starting right away.
* See [ChangeLog](https://github.com/omersiar/esp-rfid/blob/master/CHANGELOG.md)
* See [To Do](https://github.com/omersiar/esp-rfid#to-do) for what to expect in future.

### What You Will Need
### Hardware
* An ESP8266 module or development board like **WeMos D1 mini** or **NodeMcu 1.0** with at least **32Mbit Flash (equals to 4MBytes)** (ESP32 does not supported for now)
* A MFRC522 RFID PCD Module Or Wiegand based RFID reader
* A Relay Module (or you can build your own circuit)
* n quantity of Mifare Classic 1KB (recommended due to available code base) PICCs (RFID Tags) equivalent to User Number

### Software

#### Using Compiled Binaries
[Compiled binaries and the flasher tool](https://github.com/omersiar/esp-rfid/tree/master/compiledbin) are available in directory /compiledbin. On Windows you can use "flash.bat" it will ask you which COM port that ESP is connected and then flashes it. You can use any flashing tool and do the flashing manually. The flashing process itself has been described at numerous places on Internet.

#### Building With Platformio
Install platformio

Build and upload filesystem : pio run --target uploadfs

Build and upload code : pio run --target upload --upload-port COM12

NOTE: In case you want to automate the process without writing commands, set "upload_port = /dev/ttyUSB0" to point to your port and also, you might want to set "build_flags = -Wl,-Tesp8266.flash.4m.ld" according to your pletform spiffs size.


### Pin Layout

The following table shows the typical pin layout used for connecting MFRC522 hardware to ESP:

| Signal        | MFRC522       | WeMos D1 mini  | NodeMcu | Generic      |
|---------------|:-------------:|:--------------:| :------:|:------------:|
| RST/Reset     | RST           | N/C [1]        | N/C [1] | N/C [1]      |
| SPI SS        | SDA [3]       | D8 [2]         | D8 [2]  | GPIO-15 [2]  |
| SPI MOSI      | MOSI          | D7             | D7      | GPIO-13      |
| SPI MISO      | MISO          | D6             | D6      | GPIO-12      |
| SPI SCK       | SCK           | D5             | D5      | GPIO-14      |

1. Not Connected. Hard-reset no longer needed.
2. Configurable via settings page.
3. The SDA pin might be labeled SS on some/older MFRC522 boards.

For Wiegand based readers, you can configure D0 and D1 pins via settings page. By default, D0 is GPIO-4 and D1 is GPIO-5

### Steps
* First, flash firmware (you can use /compiledbin/flash.bat on Windows) to your ESP either using Arduino IDE or with your favourite flash tool
* Flash webfiles data to SPIFFS (ignore this step if you used flash.bat for flashing) either using ESP8266FS Uploader tool or with your favourite flash tool
* (optional) Fire up your serial monitor to get informed
* Power on your ESP
* Search for Wireless Network "esp-rfid" and connect to it (It should be an open network and does not reqiure password)
* Open your browser and type either "http://192.168.4.1" or "http://esp-rfid.local" (.local needs Bonjour installed on your computer) on address bar.
* Log on to ESP, default password is "admin"
* Go to "Settings" page
* Configure your amazing access control device. Push "Scan" button to join your wireless network, configure RFID hardware, Relay Module.
* Save settings, when rebooted your ESP will try to join your wireless network.
* Check your new IP address from serial monitor and connect to your ESP again. (You can also connect to "http://esp-rfid.local")
* Go to "Users" page
* Scan a PICC (RFID Tag) then it should glimpse on your Browser's screen.
* Type "User Name" or "Label" for the PICC you scanned.
* Choose "Allow Access" if you want to
* Click "Add"
* Congratulations, everything went well, if you encounter any issue feel free to ask help on GitHub.

### Known Issues
* MFRC522 RFID Hardware should be connected to ESP or you will likely get a WDT Reset (boot loop) [#13](https://github.com/omersiar/esp-rfid/issues/13).
* Currently only Git version (2.4.0rc) of ESP8266 Core is supported, due to new function is introduced (WiFi.scanNetworksAsync()).

#### Time
We are syncing time from a NTP Server (in Client -aka infrastructure- Mode). This will require ESP to have an Internet connection. Additionaly your ESP can also work without Internet connection too (Access Point -aka Ad-Hoc- Mode),  without giving up functionality.
This will require you to do syncing manually. ESP can store and hold time for you approximately 51 days without a major issue, device time can drift from actual time depending on usage, temprature, etc.
So you have to login to settings page and sync it in a timely fashion.

## Scalability
Since we are limited on both flash and ram size things may get ugly at some point in the future. You can find out some test results below.

### Tests

#### How many RFID Tag can be handled?
Restore some [randomly generated](https://github.com/omersiar/esp-rfid/raw/master/demo/demo-users-data.json) user data on File System worth:

* 1000 seperate "userfile"
* random 4 Bytes long UID and
* random User Names and
* 4 bytes random Unix Time Stamp
* each have "access type" 1 byte integer "1" or "0".

Total 122,880 Bytes

At least 1000 unique User (RFID Tag) can be handled, the test were performed on WeMos D1 mini.

#### Additional testing is needed:

* Logging needs testing. How long should it need to log access? What if a Boss needs whole year log?
* Realiability on Flash (these NOR Flash have limited write cycle on their cells). It depends on manufacturer choice of Flash Chip and usage.

## Community
* [![Chat at https://gitter.im/esp-rfid/Lobby](https://badges.gitter.im/esp-rfid.svg)](https://gitter.im/esp-rfid/Lobby) Join community chat on Gitter

### Projects that is based on esp-rfid
* [ESP-IO](https://github.com/Pako2/EventGhostPlugins/tree/master/ESP-IO)

### Contributions
Thanks to the community, ESP-RFID come to alive with their great effort:

- @rneurink
- @thunderace
- @zeraien
- @nardev

See [ChangeLog](https://github.com/omersiar/esp-rfid/blob/master/CHANGELOG.md)

## Donations
If this project helps you in a way, you can buy us a beer. You can make a donation to the ESP-RFID community with [Bountysource](https://salt.bountysource.com/teams/esp-rfid)

#### Donators
* 2017-10-03 [steinar-t](https://github.com/steinar-t)
* 2017-12-10 [saschaludwig](https://github.com/saschaludwig)

Thank you for your contributions.

## License
UNLICENSE
