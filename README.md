<img width=250px src="https://atsign.dev/assets/img/atPlatform_logo_gray.svg?sanitize=true">

<a href="https://registry.platformio.org/libraries/jeremytubongbanua/at_client"><img src="https://badges.registry.platformio.org/packages/jeremytubongbanua/library/at_client.svg" alt="PlatformIO Registry" /></a>

# at_esp32

ESP32 C++ (Arduino framework) implementation of the atPlatform.

## Installation

Learn how to add `at_client` as a dependency in your PlatformIO project. The atPlatform only supports the Arduino framework for the [ESP32](https://www.espressif.com/en/products/modules/esp32).

You can either go to PlatformIO Home and go to Libraries and search up [`at_client`](https://registry.platformio.org/libraries/jeremytubongbanua/at_client) then click on "Add to Project", or simply:

Setup your `platform.ini` similar to:

```
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
lib_deps = 
    jeremytubongbanua/at_client@^0.2.1
monitor_speed = 115200
```

## Getting Started

### 1. Prerequisites

1. Install [VSCode](https://code.visualstudio.com/download) and the [PlatformIO VSCode extension](https://platformio.org/install/ide?install=vscode).
2. Get your [atSign](https://my.atsign.com/go) and its `.atKeys` file. Follow this [video](https://youtu.be/8xJnbsuF4C8) to get your free atSign and generate its `.atKeys` file. 
3. You will need an [ESP32](https://www.espressif.com/en/products/modules/esp32) and a USB-A to micro-USB cable to connect it to your computer.

### 2. Setting up your Project

1. Open VSCode and install the PlatformIO VScode extension. You will use this extension to interact with the ESP32.
2. Go to the PlatformIO Home (if you lost it just open the command palette via Ctrl/Cmd + Shift + P and search and run the `PlatformIO: Home` command).
3. Install the `Espressif32` platform by clicking on the `Platforms` > `Embedded` tab and searching for `Espressif32` and clicking on the `Install` button.
4. Next, go to `Projects` and press `+ Create New Project`. Give a name to your project, select the `Espressif ESP32 Dev Module` board, and select the "Arduino" framework. Select a location for your project and click "Finish."
5. Go back to the PlatformIO Home and click on the `Libraries` tab. Search for `at_client` by JeremyTubongbanua and click on the `Install` button. This will install the `at_client` library in your project. Do the same for the `ArduinoJson` library by Benoit Blanchon.

### 3. Uploading your `.atKeys`

1. Create a folder called `data` in the root of your project.
2. Copy your `.atKeys` file into the `data` folder. The file should be in the data folder like `data/@bob_key.atKeys`
3. Put your ESP32 into download mde by holding down the `BOOT` button and pressing the `RESET` button while still holding the `BOOT` button. It should be in download mode as long as you are holding `BOOT` down.
4. While it is in download mode, run the "Upload File System Image" command. This can be seen under Project Tasks > Platform usually on top of where your file explorer is on VSCode. If you don't see your project tasks, you can open the command palette via Ctrl/Cmd + Shift + P and search and run the `Project Tasks` and try run the `Explorer:` and `PlatformIO:` recommended commands.
5. Hopefully you get a [SUCCESS]. If you don't, make sure you are using a **data** USB-A to Micro USB cable (yes some cables don't transmit data). Also make sure the ESP32 is plugged in :) (it's happened before).

### 4. Building and Uploading your Project

1. Open the `main.cpp` file in the `src` folder and add the following headers:

```cpp
#include <WiFiClientSecure.h>
#include <SPIFFS.h>
#include "at_client.h"
```

2. Run "Build" under "Project Tasks." If you don't see your project tasks, open the command palette via Ctrl/Cmd + Shift + P and search and run the `Project Tasks` and try run the `Explorer:` and `PlatformIO:` recommended commands.

3. Hopefully you get a [SUCCESS].

4. Now let's upload our project to the ESP32. First, put the ESP32 into download mode by holding down the `BOOT` button and pressing the `RESET` button while still holding the `BOOT` button. It should be in download mode as long as you are holding `BOOT` down. Run "Upload and Monitor" under "Project Tasks." If you don't see your project tasks, open the command palette via Ctrl/Cmd + Shift + P and search and run the `Project Tasks` and try run the `Explorer:` and `PlatformIO:` recommended commands.

### 5. Pkam Authentication

1. Recall that each atSign has its own personal server known as an "atServer." You can just think of this as a key-value database but with a lot more features. The atServer speaks the atProtocol between clients and other atServers. Now let's write some code to allow our ESP32 to authenticate into its atServer so it can run operations.

2. First let's create a `constants.h` header file under the `include/` folder in our project. Let's define our WiFi SSID and Password. This can be your home WiFi or your own personal hotspot. Depending on your school, this may not work on your school WiFi.

```cpp
// constants.h

#pragma once

#define SSID "******"
#define PASSWORD "******"
```

3. Now let's include this header file into our `main.cpp` by including it like this:

```cpp
/// ...
#include "constants.h"
/// ...
```

4. Now let's add our pkam authentication code under the `void setup() {}` function like so:

```cpp

void setup()
{
    // put your setup code here, to run once:

    // change this to the atSign you own and have the keys to
    const auto *at_sign = new AtSign("@24glad32"); 
    
    // reads the keys on the ESP32
    const auto keys = keys_reader::read_keys(*at_sign); 
    
    // creates the AtClient object (allows us to run operations)
    auto *at_client = new AtClient(*at_sign, keys);  
    
    // pkam authenticate into our atServer
    at_client->pkam_authenticate(SSID, PASSWORD); 
}
```

5. Now put the ESP32 into download mode and upload and monitor your project.

6. Your output should be similar to the snippet below. If it is not, ensure that the `.atKeys` are uploaded and that you are using the correct SSID and Password. It may take a few attempts to get it to work (perhaps due to weak connection). For Tyler, it took a few attempts to get it to work. 

```sh
Attempting to connect to Soup...
..
Connected!
IP address: 2113972416
Connected to root.atsign.org:64
 ce749e70-f570-5486-9f86-eb9ec5e038d8.swarm0002.atsign.zone:5604
res: " ce749e70-f570-5486-9f86-eb9ec5e038d8.swarm0002.atsign.zone:5604"
Connected to ce749e70-f570-5486-9f86-eb9ec5e038d8.swarm0002.atsign.zone:5604
first: "@data:["publickey@24glad32","signing_publickey@24glad32"]"
Secondary connected: 1
challenge: "_def2da36-4c10-4b0e-97c1-f09c314637c2@24glad32:44fc42a6-774e-4bb8-bd6a-90d4c58b590a"
import: 0
complete: 0
private key check: 0
sign success: 0
pkam command: "pkam:VVw2RNcwhNw/l7vLLeC10VqNU2xqfUtjf7T82PvoxQ+uaN6QW9zj8Pi4+b8UgUNu6G8L1RkdmU2UMCkWrB/L0v2Jh1oK7eHth8PN4B91F/4rtxd1AGkfV+aiX5IyRLBC8prl5qdcqKqf3Gt6XCSV4/NDCOrXQqXdd/njZ/gmCLJlyPPlSu6IkOzMxu2nAcwNe7daSFw1agcyLLyhjdl3oJOopxGFX3zRar3vnex5F71A/1BIyP0h5OjZ0ZItngWaujMxEwwyvMINQJa0nflwk7z6phP+Vnt+7+oKNQCDhRfUTDaWHm4t++2Mp6BrHDh5KPCFCK9psgFGu+tx8nvpjg=="
response: success
authenticated: 1
```

### 6. Sending Data

### 7. Receiving Data

## Resources

Core assets used in this project:

- [ESP32-WROOM](https://www.espressif.com/en/products/modules/esp32)
- [PlatformIO](https://platformio.org/)
- [Mbed-TLS](https://github.com/Mbed-TLS/mbedtls)
- [mtausig's tiny-asn1](https://gitlab.com/mtausig/tiny-asn1)

## Contribute

1. Open VSCode and install the PlatformIO VScode extension. You will use this extension to interact with the ESP32. Go to the PlatformIO Home (if you lost it just open the command palette and search and run the `PlatformIO: Home` command) and install the `Espressif32` platform by clicking on the `Platforms` tab and searching for `Espressif32` and clicking on the `Install` button.

2. Make a fork of this repository and git clone it to your local machine.

```
git clone <YOUR_FORK_URL> <path
```

3. Open the PlatformIO CLI by doing `Ctrl (or Cmd) + Shift + P` and run the `PlatformIO: Open PlatformIO Core CLI` command to open the CLI terminal. Then run the following command in the Platform CLI to initialize the `.pio` file.

```
pio project init
```

4. Create a `main.cpp` file in the `src` folder and add the following code:

This will allow you to compile the code. 

```cpp
void setup() {};
void loop() {};
```

5. Open the command palette with `Ctrl (or Cmd) + Shift + P` and run the `PlatformIO: Build` command to build the project. You should get no errors and the terminal should output a build successful. If you get an error, ensure you are executing the commands in the root of the project (the same folder as the `platformio.ini` file).

Any and all pull requests are appreciated and welcome. Please read our [contributing guidelines](CONTRIBUTING.md) containing detailed instructions on how to contribute correctly and create a proper pull request. If you have any questions, please feel free to reach out to us on our [Discord server](https://discord.atsign.com).

## License

BSD 3-Clause License

Copyright (c) 2022, The Atsign Foundation

All rights reserved.

## Maintainers

- [JeremyTubongbanua](https://github.com/JeremyTubongbanua)
- [TylerTrott](https://github.com/TylerTrott)
- [cpswan](https://github.com/cpswan)
- [cconstab](https://github.com/cconstab)
- [XavierChanth](https://github.com/XavierChanth)
