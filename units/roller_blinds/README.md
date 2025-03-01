# POE Roller Blinds

![poe_roller_blinds](/units/roller_blinds/assets/cad_assembly.png "Assembly")

## Features

| Feature      | Specifications                                                      |
|--------------|---------------------------------------------------------------------|
| POE          | 802.3af, 6W                                                         |
| Ethernet     | 802.3-2008, 10/100Mbps                                              |
| WiFi         | 802.11b/g/n, 150Mbps                                                |
| Bluetooth    | Bluetooth v4.2 BR/EDR and Bluetooth LE                              |
| Motor driver | TMC2209, silent, sensor-less homing, speed and acceleration control |

## Main parts

| Part              | Datasheet                                            | EU Shop                                                                                      |
|-------------------|------------------------------------------------------|----------------------------------------------------------------------------------------------|
| M5Stack POE ESP32 | [link](/units/roller_blinds/assets/docs/poesp32.pdf) | [link](https://eu.mouser.com/ProductDetail/M5Stack/U138?qs=vvQtp7zwQdPAxf%252BUv9YNIg%3D%3D) |

## 3D printed parts

| Part                | File                                                                          | Note                                                                                          |
|---------------------|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| Motor mount         | [file](/units/roller_blinds/assets/cad_files/for_printing/28byj-48_mount.3mf) | Holds the motor                                                                               |
| Spring mount        | [file](/units/roller_blinds/assets/cad_files/for_printing/spring_mount.3mf)   | Holds the spring assembly                                                                     |
| Motor shaft adapter | [file](/units/roller_blinds/assets/cad_files/for_printing/motor_adapter.3mf)  | Adapts the motor shaft to the aluminium extrusion of the roller blind                         |
| Motor cover         | [file](/units/roller_blinds/assets/cad_files/for_printing/motor_cover.3mf)    | Holds the electronics and can be used to cover up the other side too if you like the symmetry |

## Optional parts

| Part                                     | Datasheet                                                                     | EU Shop                                                                                                  | Note                                                |
|------------------------------------------|-------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|-----------------------------------------------------|
| 2.4GHz BT/Wifi flexible adhesive antenna | [link](/units/roller_blinds/assets/Amphenol_04262021_PIOV0MSNRBA-2306519.pdf) | [link](https://eu.mouser.com/ProductDetail/Amphenol-SAA/PIOV0MSNRBA-100?qs=GedFDFLaBXF5eAFxhrTnpw%3D%3D) | Can enable Bluetooth functionality for various uses |

## Electronics

The electronics assembly is based on M5Stack POE ESP32 module:

![module_product](/units/roller_blinds/assets/poesp32_01.png "M5Stack POE ESP32")
![module_schematic](/units/roller_blinds/assets/SCH_UNIT-PoESP32_sch_01.png "M5Stack POE ESP32 schematic")

When opened it will contain 2 PCBs connected via 2x5 pin header. The PCB with the RJ45 port and ESP32 module will be used as the main board for this project, the other PCB will be dismantled for the POE module.

The complete assembly could be done with a perforated soldering board and a few wires, but since we do things proper-ish here the printed circuit board was designed:

![schematic](/units/roller_blinds/assets/poe_roller_blind_controller_schematic.png "Schematic")
![board_view](/units/roller_blinds/assets/poe_roller_blind_controller_pcb.png "Board view")
![3d_top](/units/roller_blinds/assets/roller_blind_controller_3d_top.png "3D top view")
![3d_bottom](/units/roller_blinds/assets/roller_blind_controller_3d_bottom.png "3D bottom view")

## Motor preparation

The 28BYJ-48 unipolar motor is designed to be controlled with simpler logic, for example shift registers and contains the center taps of both of the coils connected to the 5th pin in the connector:

![coils_and_pins](/units/roller_blinds/assets/28BYJ-48-stepper-motor-coils.jpg "Coil and cable pin connections")

This makes the motor unusable with regular bipolar motor drivers like TMC2209. Luckily there is a simple modification to convert 28BYJ-48 to bipolar operation.

### The modification

You will need to take off the blue plastic cover from the side of the motor to reveal a small PCB with all the electrical connections of the motor. Then cut the center trace. I have done it with a rotary tool, byt a sharp knife would work nicely too.

### Before

![unmodified_motor](/units/roller_blinds/assets/unmodified_28BYJ-48.jpg "Unmodified 28BYJ-48")

### After

![modified_motor](/units/roller_blinds/assets/modified_28BYJ-48.jpg "Modified 28BYJ-48")

Now the motor will work with any common stepper motor driver.

## Mechanical

## ESPHome
