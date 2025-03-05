# PoE Roller Blinds

![poe_roller_blinds](/units/roller_blinds/assets/cad_assembly.png "Assembly")

## Introduction

During my smart home development I was searching for smart PoE-powered black-out roller blinds and to my surprise I was not able to find anything remotely affordable and compatible with my home assistant setup.

There are a few offerings in the industry but they have their own downsides:

| Product                                                                                                                         | Features                                                                                                                 | Antifeatures                                                                                                                                         |
|---------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Powershades TruePoE](https://powershades.com/truepoe)                                                                          | All-in-one solution, easy to deploy                                                                                      | Cloud-based, unrepairable (no available parts), not sold in EU                                                                                       |
| [Somfy Sonesse® 30 PoE (and other variations)](https://www.somfysystems.com/en-us/products/1241147/sonesse-30-poe)              | All-in-one solution, easy to deploy                                                                                      | Too expensive (400 eur), unrepairable (no available parts), proprietary control interface                                                            |
| [Smart wings hardwired PoE roller blinds](https://www.smartwingshome.com/pages/the-worlds-first-poe-matter-over-ethernet-motor) | All-in-one solution, easy to deploy, optional PoE and DC power, compatible with Home Assistant and matter out of the box | The motor itself is 170eur, unrepairable (no available parts), but it is still the best solution if you want plug and play PoE powered roller blinds |

## Features

| Feature      | Specifications                                                      |
|--------------|---------------------------------------------------------------------|
| PoE          | 802.3af, 6W                                                         |
| Ethernet     | 802.3-2008, 10/100Mbps                                              |
| WiFi         | 802.11b/g/n, 150Mbps                                                |
| Bluetooth    | Bluetooth v4.2 BR/EDR and Bluetooth LE                              |
| Motor driver | TMC2209, silent, sensor-less homing, speed and acceleration control |

## Main parts

| Part                                      | Datasheet                                                                   | EU Shop                                                                                                       |
|-------------------------------------------|-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| M5Stack PoE ESP32                         | [link](/units/roller_blinds/assets/docs/poesp32.pdf)                        | [link](https://eu.mouser.com/ProductDetail/M5Stack/U138?qs=vvQtp7zwQdPAxf%252BUv9YNIg%3D%3D)                  |
| FYSETC TMC2209 V3/V4 stepper motor driver | [link](/units/roller_blinds/assets/docs/Silent2209%20-%20FYSETC%20WIKI.pdf) | [link](https://www.amazon.de/-/en/Printer-Upgrade-Microsteps-Motherboard-Controller/dp/B07Z8QP8M9/ref=sr_1_3) |
| 2.54mm female header kit                  |                                                                             | [link](https://www.amazon.de/-/en/Straight-Connector-Assortment-Arduino-Prototype/dp/B07Q1XBGFB/ref=sr_1_2)   |

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

The electronics assembly is based on M5Stack PoE ESP32 module:

![module_product](/units/roller_blinds/assets/poesp32_01.png "M5Stack PoE ESP32")
![module_schematic](/units/roller_blinds/assets/SCH_UNIT-PoESP32_sch_01.png "M5Stack PoE ESP32 schematic")

When opened it will contain 2 PCBs connected via 2x5 pin header. The PCB with the RJ45 port and ESP32 module will be used as the main board for this project, the other PCB will be dismantled for the PoE module.

The complete assembly could be done with a perforated soldering board and a few wires, but since we do things proper-ish around here - the printed circuit board was designed:

![schematic](/units/roller_blinds/assets/poe_roller_blind_controller_schematic.png "Schematic")
![board_view](/units/roller_blinds/assets/poe_roller_blind_controller_pcb.png "Board view")
![3d_top](/units/roller_blinds/assets/roller_blind_controller_3d_top.png "3D top view")
![3d_bottom](/units/roller_blinds/assets/roller_blind_controller_3d_bottom.png "3D bottom view")

## Motor preparation

The 28BYJ-48 unipolar motor is designed to be controlled with simpler logic, for example shift registers and contains the center taps of both of the coils connected to the 5th pin in the connector:

![coils_and_pins](/units/roller_blinds/assets/28BYJ-48-stepper-motor-coils.jpg "Coil and cable pin connections")

This makes the motor unusable with regular bipolar motor drivers like TMC2209. Luckily there is a simple modification to convert 28BYJ-48 to bipolar operation.

### The modification

You will need to take off the blue plastic cover from the side of the motor to reveal a small PCB with all the electrical connections of the motor. Then cut the center trace. I have done it with a rotary tool, but a sharp knife would work nicely too.

### Before

![unmodified_motor](/units/roller_blinds/assets/unmodified_28BYJ-48.jpg "Unmodified 28BYJ-48")

### After

![modified_motor](/units/roller_blinds/assets/modified_28BYJ-48.jpg "Modified 28BYJ-48")

Now the motor will work with any common stepper motor driver.

## Driver

The 28BYJ-48 motor is designed to take full 5V current on half of its coil. After the modification we technically doubled the coil resistance and can crank the TMC2209 driver current all the way up with the on-board potentiometer. I used FYSETC TMC2209 V4.0 driver boards so for me the potentiometer should be turned all the way clockwise.

> [!NOTE]
> If you are using any other TMC2209 driver - double check the RX, TX, INDEX, and DIAG pin positions, they differ from driver to driver and you may need to creatively add 1k resistor between RX and TX pins on the board. Also some of the boards have VREF, DIAG, and INDEX pins switched around. PCB only supports driver boards which have INDEX and DIAG pins at 90 degrees from the other pins.

## Electronics assembly

The complete electronics assembly looks like this:

![electronics_assembly](/units/roller_blinds/assets/electronics_compartment.png "Electronics assembly")

## Mechanical assembly

And the complete mechanical assembly looks like this:
![mechanical_assembly](/units/roller_blinds/assets/cad_assembly.png "Mechanical assembly")

## ESPHome configuration

```
esphome:
  name: blinds
  friendly_name: Blinds
  on_boot:
    - tmc2209.configure:
        microsteps: 1
        interpolation: True
        enable_spreadcycle: False
        tcool_threshold: 1000 # SG can be triggered when speed exceeds this value
    - tmc2209.stallguard:
        threshold: 5 # SG is triggered if the SG value is lower than 2x threshold
    - tmc2209.currents:
        standstill_mode: normal # coil_short_ls
        irun: 31 # 16 for best microstep performance (16/32 = 1/2 maximum motor current)
        ihold: 16 # should be 0 to enable passive breaking
        tpowerdown: 0
        iholddelay: 0
    - button.press: home_button # home right after boot

esp32:
  board: esp32dev
  framework:
    type: arduino
    
# Enable logging
logger:
  hardware_uart: UART2

# Enable Home Assistant API
api:
  encryption:
    key: "xxxxxxxxxxxxxxxxxxxxxxxxxx"

ota:
  - platform: esphome
    password: "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

ethernet:
  type: IP101
  mdc_pin: GPIO23
  mdio_pin: GPIO18
  clk_mode: GPIO0_IN
  phy_addr: 1
  power_pin: GPIO5

text_sensor:
  - platform: ethernet_info
    ip_address:
      name: "IP"
    mac_address:
      name: "MAC"
    dns_address:
      name: "DNS"

external_components:
  - source: github://slimcdk/esphome-custom-components
    components: [ tmc2209_hub, tmc2209, stepper ]

globals:
  - id: has_homed
    type: bool
    initial_value: "true"
    restore_value: False
  - id: blinds_bottom_pos
    type: int
    initial_value: '9999'
    restore_value: True
  - id: blinds_top_pos
    type: int
    initial_value: '0'
    restore_value: True

uart:
  id: tmc2209_uart
  rx_pin: GPIO1
  tx_pin: GPIO3
  baud_rate: 500000
  
tmc2209_hub:
  id: tmc_hub
  uart_id: tmc2209_uart

stepper:
  - platform: tmc2209
    id: driver
    tmc2209_hub_id: tmc_hub
    diag_pin: GPIO17
    index_pin: GPIO16
    max_speed: 50 steps/s
    acceleration: 20 steps/s^2
    deceleration: 20 steps/s^2
    address: 0x00
    # rsense: 110 mOhm
    vsense: False
    on_status:
      - logger.log:
          format: "Driver is reporting an update! (code %d)"
          args: ["code"]
      - if:
          condition:
            lambda: return code == tmc2209::DIAG_TRIGGERED;
          then:
            - logger.log: DIAG_TRIGGERED
      - if:
          condition:
            lambda: return code == tmc2209::DIAG_TRIGGER_CLEARED;
          then:
            - logger.log: DIAG_TRIGGER_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::RESET;
          then:
            - logger.log: RESET
      - if:
          condition:
            lambda: return code == tmc2209::RESET_CLEARED;
          then:
            - logger.log: RESET_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::DRIVER_ERROR;
          then:
            - logger.log: DRIVER_ERROR
      - if:
          condition:
            lambda: return code == tmc2209::DRIVER_ERROR_CLEARED;
          then:
            - logger.log: DRIVER_ERROR_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::CP_UNDERVOLTAGE;
          then:
            - logger.log: CP_UNDERVOLTAGE
      - if:
          condition:
            lambda: return code == tmc2209::CP_UNDERVOLTAGE_CLEARED;
          then:
            - logger.log: CP_UNDERVOLTAGE_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::OVERTEMPERATURE_PREWARNING;
          then:
            - logger.log: OVERTEMPERATURE_PREWARNING
      - if:
          condition:
            lambda: return code == tmc2209::OVERTEMPERATURE_PREWARNING_CLEARED;
          then:
            - logger.log: OVERTEMPERATURE_PREWARNING_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::OVERTEMPERATURE;
          then:
            - logger.log: OVERTEMPERATURE
      - if:
          condition:
            lambda: return code == tmc2209::OVERTEMPERATURE_CLEARED;
          then:
            - logger.log: OVERTEMPERATURE_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::TEMPERATURE_ABOVE_120C;
          then:
            - logger.log: TEMPERATURE_ABOVE_120C
      - if:
          condition:
            lambda: return code == tmc2209::TEMPERATURE_BELOW_120C;
          then:
            - logger.log: TEMPERATURE_BELOW_120C
      - if:
          condition:
            lambda: return code == tmc2209::TEMPERATURE_ABOVE_143C;
          then:
            - logger.log: TEMPERATURE_ABOVE_143C
      - if:
          condition:
            lambda: return code == tmc2209::TEMPERATURE_BELOW_143C;
          then:
            - logger.log: TEMPERATURE_BELOW_143C
      - if:
          condition:
            lambda: return code == tmc2209::TEMPERATURE_ABOVE_150C;
          then:
            - logger.log: TEMPERATURE_ABOVE_150C
      - if:
          condition:
            lambda: return code == tmc2209::TEMPERATURE_BELOW_150C;
          then:
            - logger.log: TEMPERATURE_BELOW_150C
      - if:
          condition:
            lambda: return code == tmc2209::TEMPERATURE_ABOVE_157C;
          then:
            - logger.log: TEMPERATURE_ABOVE_157C
      - if:
          condition:
            lambda: return code == tmc2209::TEMPERATURE_BELOW_157C;
          then:
            - logger.log: TEMPERATURE_BELOW_157C
      - if:
          condition:
            lambda: return code == tmc2209::OPEN_LOAD;
          then:
            - logger.log: OPEN_LOAD
      - if:
          condition:
            lambda: return code == tmc2209::OPEN_LOAD_CLEARED;
          then:
            - logger.log: OPEN_LOAD_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::OPEN_LOAD_A;
          then:
            - logger.log: OPEN_LOAD_A
      - if:
          condition:
            lambda: return code == tmc2209::OPEN_LOAD_A_CLEARED;
          then:
            - logger.log: OPEN_LOAD_A_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::OPEN_LOAD_B;
          then:
            - logger.log: OPEN_LOAD_B
      - if:
          condition:
            lambda: return code == tmc2209::OPEN_LOAD_B_CLEARED;
          then:
            - logger.log: OPEN_LOAD_B_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::LOW_SIDE_SHORT;
          then:
            - logger.log: LOW_SIDE_SHORT
      - if:
          condition:
            lambda: return code == tmc2209::LOW_SIDE_SHORT_CLEARED;
          then:
            - logger.log: LOW_SIDE_SHORT_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::LOW_SIDE_SHORT_A;
          then:
            - logger.log: LOW_SIDE_SHORT_A
      - if:
          condition:
            lambda: return code == tmc2209::LOW_SIDE_SHORT_A_CLEARED;
          then:
            - logger.log: LOW_SIDE_SHORT_A_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::LOW_SIDE_SHORT_B;
          then:
            - logger.log: LOW_SIDE_SHORT_B
      - if:
          condition:
            lambda: return code == tmc2209::LOW_SIDE_SHORT_B_CLEARED;
          then:
            - logger.log: LOW_SIDE_SHORT_B_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::GROUND_SHORT;
          then:
            - logger.log: GROUND_SHORT
      - if:
          condition:
            lambda: return code == tmc2209::GROUND_SHORT_CLEARED;
          then:
            - logger.log: GROUND_SHORT_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::GROUND_SHORT_A;
          then:
            - logger.log: GROUND_SHORT_A
      - if:
          condition:
            lambda: return code == tmc2209::GROUND_SHORT_A_CLEARED;
          then:
            - logger.log: GROUND_SHORT_A_CLEARED
      - if:
          condition:
            lambda: return code == tmc2209::GROUND_SHORT_B;
          then:
            - logger.log: GROUND_SHORT_B
      - if:
          condition:
            lambda: return code == tmc2209::GROUND_SHORT_B_CLEARED;
          then:
            - logger.log: GROUND_SHORT_B_CLEARED
    on_stall:
      - logger.log: "Motor stalled!"
      - if:
          condition:
            lambda: |-
              return !id(has_homed);
          then:
            - stepper.stop: driver
            - stepper.report_position:
                id: driver
                position: 0
            - stepper.set_target:
                id: driver
                target: 0
            - globals.set:
                id: has_homed
                value: "true"
            - logger.log: "Homed"

button:
  - platform: template
    entity_category: DIAGNOSTIC
    id: home_button
    name: "Home"
    on_press:
      - logger.log: "Going home!"
      - globals.set:
          id: has_homed
          value: "false"
      - stepper.set_target:
          id: driver
          target: -9999999

  - platform: template
    entity_category: DIAGNOSTIC
    id: set_bottom_pos_button
    name: "Set bottom position"
    on_press:
      then:
        - globals.set: 
            id: blinds_bottom_pos
            value: !lambda 'return id(driver)->current_position;'
        - lambda: |-
            ESP_LOGD("Blinds", "Setting bottom position to: %d", id(driver)->current_position);

  - platform: template
    entity_category: DIAGNOSTIC
    id: set_top_pos_button
    name: "Set top position"
    on_press:
      then:
        - globals.set: 
            id: blinds_top_pos
            value: !lambda 'return id(driver)->current_position;'
        - lambda: |-
            ESP_LOGD("Blinds", "Setting top position to: %d", id(driver)->current_position);

  - platform: template
    entity_category: DIAGNOSTIC
    name: "10 Steps forward"
    on_press:
      - stepper.set_target:
          id: driver
          target: !lambda 'return id(driver)->current_position +10;'

  - platform: template
    entity_category: DIAGNOSTIC
    name: "10 Steps backward"
    on_press:
      - stepper.set_target:
          id: driver
          target: !lambda 'return id(driver)->current_position -10;'

  - platform: template
    entity_category: DIAGNOSTIC
    name: "100 Steps forward"
    on_press:
      - stepper.set_target:
          id: driver
          target: !lambda 'return id(driver)->current_position +100;'

  - platform: template
    entity_category: DIAGNOSTIC
    name: "100 Steps backward"
    on_press:
      - stepper.set_target:
          id: driver
          target: !lambda 'return id(driver)->current_position -100;'

  - platform: template
    entity_category: DIAGNOSTIC
    name: "1000 Steps forward"
    on_press:
      - stepper.set_target:
          id: driver
          target: !lambda 'return id(driver)->current_position +1000;'

  - platform: template
    entity_category: DIAGNOSTIC
    name: "1000 Steps backward"
    on_press:
      - stepper.set_target:
          id: driver
          target: !lambda 'return id(driver)->current_position -1000;'

  - platform: template
    name: Stop
    on_press:
      - stepper.stop: driver

  - platform: restart
    name: "Restart"

cover:
  - platform: template
    device_class: blind
    id: blinds
    name: "Blinds"
    optimistic: False
    has_position: True

    lambda: |-
      #define MAP_FLOAT(x, in_min, in_max, out_min, out_max) ((float)(x - in_min) * (out_max - out_min) / (float)(in_max - in_min) + out_min)
      return MAP_FLOAT(id(stepper_position_sensor)->state, id(blinds_top_pos), id(blinds_bottom_pos), 1.0, 0.0);
    open_action:
      - if:
          condition:
            lambda: |-
              return id(has_homed);
          then:
            - stepper.set_target: 
                id: driver
                target: !lambda 'return id(blinds_top_pos);'
    close_action:
      - if:
          condition:
            lambda: |-
              return id(has_homed);
          then:
            - stepper.set_target: 
                id: driver
                target: !lambda 'return id(blinds_bottom_pos);'
    stop_action: 
      then:
        - stepper.stop: driver
    position_action: 
      - if:
          condition:
            lambda: |-
              return id(has_homed);
          then:
            - stepper.set_target: 
                id: driver
                target: !lambda |-
                  #define MAP_FLOAT(x, in_min, in_max, out_min, out_max) ((float)(x - in_min) * (out_max - out_min) / (float)(in_max - in_min) + out_min)
                  int pos_to_set = MAP_FLOAT(pos, 0.0, 1.0, id(blinds_bottom_pos), id(blinds_top_pos));
                  ESP_LOGD("Blinds", "Rolling blinds to: %d (%f)", pos_to_set, pos);
                  return pos_to_set;
        
binary_sensor:
  - platform: template
    id: homed_status_sensor
    entity_category: DIAGNOSTIC
    name: "Homed status"
    lambda: |-
      return id(has_homed);

sensor:
  - platform: template
    entity_category: DIAGNOSTIC
    id: top_position_sensor
    name: "Top position"
    accuracy_decimals: 0
    unit_of_measurement: "steps"
    filters:
      - delta: 1
    lambda: |-
      return id(blinds_top_pos);

  - platform: template
    entity_category: DIAGNOSTIC
    id: bottom_position_sensor
    name: "Bottom position"
    accuracy_decimals: 0
    unit_of_measurement: "steps"
    filters:
      - delta: 1
    lambda: |-
      return id(blinds_bottom_pos);

  - platform: template
    entity_category: DIAGNOSTIC
    id: stepper_position_sensor
    name: "Position"
    unit_of_measurement: "steps"
    accuracy_decimals: 0
    update_interval: 1s
    filters:
      - delta: 1
    lambda: |-
      return id(driver)->current_position;

  - platform: tmc2209
    entity_category: DIAGNOSTIC
    internal: True
    type: stallguard_result
    name: Driver stallguard
    update_interval: 250ms
    filters:
      - delta: 1

  # - platform: tmc2209
  #   entity_category: DIAGNOSTIC
  #   type: motor_load
  #   name: Motor load
  #   update_interval: 250ms

  # - platform: tmc2209
  #   type: actual_current
  #   name: Actual current
  #   update_interval: 250ms

  # - platform: tmc2209
  #   type: pwm_scale_sum
  #   name: PWM Scale Sum
  #   update_interval: 250ms

  # - platform: tmc2209
  #   type: pwm_scale_auto
  #   name: PWM Scale Auto
  #   update_interval: 250ms

  # - platform: tmc2209
  #   type: pwm_ofs_auto
  #   name: PWM OFS Auto
  #   update_interval: 250ms

  # - platform: tmc2209
  #   type: pwm_grad_auto
  #   name: PWM Grad Auto
  #   update_interval: 250ms

```
