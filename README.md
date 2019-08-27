# Ardu32
Build files and Hardware SDK for the Ardu32 game console

# Cartridge Specifications

The Ardu32 is a game console that makes use of the TVOut library for generating audio and video. While completely self contained, it can accept game cartridges, creating a retro experience.

# How Cartridges Work

The Ardu32 features an Atmel AVR 328P, which is also in the Arduino Uno. It has full video and audio generation capability, as well as power supply and game controller inputs. 

The Ardu32 Bootstrap Software can detect if a cartridge is inserted through a GPIO line. If so, it plays the cartridge screen and promptly reliqushes control of video by stopping its TVOut library process. The cartridge should wait for this process to complete, otherwise, it will cause disruption of the video signal.

The bootstrap software uses a standard 57600 baud TVOut compatible serial port to transmit its status, such as its video output state, controller state, and other specialized functions. This allows the cartridge to focus on video generation, audio generation, game play while the Ardu32 processor aggrigates 10 GPIO lines and 4 analog lines into a simple serial stream.

# Cartridge Pinout

The cartridge pinout is designed to be expandable, with unused lines tied to ground. It makes use of the commonly available DB37. The Ardu32 is female, while the cartridge is male.

The pinouts are as follows:

Pin | Name      | Description
----|-----------|------------
1   | Reserved  | Tied to ground on Ardu32 V1
2   | Reserved  | Tied to ground on Ardu32 V1
3   | Reserved  | Tied to ground on Ardu32 V1
4   | Reserved  | Tied to ground on Ardu32 V1
5   | Reserved  | Tied to ground on Ardu32 V1
6   | Loopback  | Looped back to pin 25 for Ardu32 V1 detection
7   | AUX1      | Broken out to AUX1 header, with a return to pin 26
8   | LED (D4)  | Direct connection to LED D4, which has a 330 ohm resistor shared with D3
9   | Reserved  | Tied to ground on Ardu32 V1
10  | Video (PD7) | Connected to 330 ohm resistor, advise 1N4148 on cart video side
11  | Reserved  | Tied to ground on Ar7du32 V1
12  | TXD       | TXD from Arduino (Connect to RX on cart processor)
13  | AUX2      | Broken out to AUX2 header, with return to pin 32
14  | Reserved  | Tied to ground on Ardu32 V1
15  | Reserved  | Tied to ground on Ardu32 V1
16  | Reserved  | Tied to ground on Ardu32 V1
17  | Reserved  | Tied to ground on Ardu32 V1
18  | Audio (PB3) | Connected to 330 ohm resistor, advise 1N4148 on cart audio side
19  | Reserved  | Tied to ground on Ardu32 V1
20 | VCC  |5V DC, connected to main 1.5A regulator
21 | Reserved  | Tied to ground on Ardu32 V1
22 | PD6 |  Tied to PD6. Used for cart detection to disable video when HIGH.
23 | Reserved  | Tied to ground on Ardu32 V1
24 | Reserved  | Tied to ground on Ardu32 V1
25 | Loopback | Looped back to pin 6 for Ardu32 V1 detection
26 | AUX1 | Return, connected to cart pin 7
27 | Reserved | Tied to ground on Ardu32 V1
28 | Video (PB1) | Connected to 1K ohm resistor, advise 1N4148 on cart audio side
29 | Reserved | Tied to ground on Ardu32 V1
30 | RXD | RXD from Arduino (Connect to TX on cart processor)
31 | Reserved | Tied to ground on Ardu32 V1
32 | AUX2 | Return from pin 13 on AUX2 header
33 | LED (D3) | Direct connection to LED D3, which has a 330 ohm resistor
34 | RESET | Ardu32 processor reset pin with 10K pullup, tie to cart RESET
35 | Reserved | Tied to ground on Ardu32 V1
36 | Reserved | Tied to ground on Ardu32 V1
37 | Reserved | Tied to ground on Ardu32 V1

# Serial Protocol

The Ardu32 makes use of a simple, autocorrelating framing protocol to allow rapid transmission of controller inputs, control functions, and additional expandability.

A frame has a type, a variable data field, and a semicolon terminator. This allows for complex data to be transceived, while making controller status messages very short.

1 Byte | 1 Byte        | 1-64 bytes    | 1 Byte |
-------|---------------|---------------|--------|
 \*     | Control Value | Variable Data | ; |

Any data in the variable data field must be escaped with a backslash: \\ \* and \;

## Control Values from Ardu32

| Hex value | Size | Purpose             |
|-----------|------|---------------------|
| 0x01      |  1   | Controller A Bitmap |
| 0x02      |  1   | Controller B Bitmap |
| 0x03      |  1   | Analog Controller A |
| 0x04      |  1   | Analog Controller B |
| 0x05      |  1   | Video status message |
| 0x06      |  V   | WiFi scan result    |
| 0x07      |  1   | WiFi status         |
| 0x08      |  4   | Socket status      |
| 0x09      |  V   | Socket message     |
| 0x0A      |  V   | Ardu32 firmware version |
| 0x0B      |  1   | Pong                |
| 0x0C      |  1   | Ping                |
| 0x0D      |  V   | Cheat code          |

### Controller A and B Bitmap

The controller bitmaps contains the status of the game port. Bit 7, input change, is toggled when an input change occurs. This allows for simple logic functions to scan for changes before the controller input is processed again.

| Bit | Description |
|-----|-------------|
|  0  | Up  |
|  1  | Down |
|  2  | Left |
|  3  | Right |
|  4  | Fire |
|  5  | Fire 2 |
|  6  | Fire 3 |
|  7  | Input Change |

### Analog Controller A and B

Byte value of 0-255, depending on POT reading. 

### Video Status Message

| Hex Value | Purpose |
|-----------|---------|
|   0x01    | Disable cartridge video, Ardu32 is enabling |
|   0x02    | Ardu32 bootstrap video is disabled, enable cartridge video |

The cartridge should always respond to disable video messages, as this allows the user to interrupt the cartridge and
go back to the bootstrap screen for whatever reason. The program should suspend. 

### WiFi Scan Result

A future adapter cartridge (ArduGenie) and potentially a future console variant, will offer online WiFi play. 

The first byte indicates the WiFi network option number, and the rest of the packet is the WiFi name. Multiple scan results can occur. 

A null packet (0x00) indicates the scan result is complete.

### WiFi Status

Used to indicate if the WiFi is connected or not.

| Hex Value | Purpose |
|-----------|---------|
|   0x01    | WiFi connected |
|   0x02    | WiFi disconnected |

### Socket Status


This section is still being written.



## Control Values to Ardu32 from Cartridge

| Hex value | Size | Purpose             |
|-----------|------|---------------------|
| 0x01      |  1   | Configure controller intervals |
| 0x02      |  1   | Enable/Disable analog messages |
| 0x03      |  1   | Video status message |
| 0x04      |  1   | Scan WiFi            |
| 0x05      |  V   | WiFi select          |
| 0x06      |  1   | Query WiFi status    |
| 0x07      |  1   | Query socket         |
| 0x08      |  V   | Socket message       |
| 0x09      |  1   | Query firmware version |
| 0x0A      |  1   | Pong                 |
| 0x0B      |  1   | Ping                 |


