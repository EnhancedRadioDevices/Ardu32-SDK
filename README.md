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

TBD
