# OpenWebNet (BTicino/Legrand) Binding

This binding integrates BTicino / Legrand MyHOME&reg; BUS and Zigbee wireless (MyHOME_Play&reg;) devices using the [OpenWebNet](https://en.wikipedia.org/wiki/OpenWebNet) protocol.

The binding supports:

- both wired BUS/SCS (MyHOME) and wireless setups (MyHOME Zigbee). The two networks can be configured simultaneously
- auto discovery of BUS/SCS IP and Zigbee USB gateways; auto discovery of devices
- commands from openHAB and feedback (events) from BUS/SCS and wireless network

![MyHOMEServer1 Gateway](doc/MyHOMEServer1_gateway.jpg)
![F454 Gateway](doc/F454_gateway.png)
![Zigbee USB Gateway](doc/USB_gateway.jpg)

## Supported Things

In order for this binding to work, a **BTicino/Legrand OpenWebNet gateway** is needed in your home system to talk to devices.

These gateways have been tested with the binding:

- **IP gateways** or scenario programmers, such as BTicino
[F454](https://catalogue.bticino.com/BTI-F454-EN),
[MyHOMEServer1](https://catalogue.bticino.com/BTI-MYHOMESERVER1-EN),
[MyHOME_Screen10 (MH4893C)](https://catalogue.bticino.com/BTI-MH4893C-EN),
[MyHOME_Screen3,5 (LN4890)](https://catalogue.bticino.com/BTI-LN4890-EN),
[MH201](https://catalogue.bticino.com/BTI-MH201-EN),
[MH202](https://catalogue.bticino.com/BTI-MH202-EN),
[F455](https://www.homesystems-legrandgroup.com/home?p_p_id=it_smc_bticino_homesystems_search_AutocompletesearchPortlet&p_p_lifecycle=0&p_p_state=normal&p_p_mode=view&_it_smc_bticino_homesystems_search_AutocompletesearchPortlet_journalArticleId=2481871&_it_smc_bticino_homesystems_search_AutocompletesearchPortlet_mvcPath=%2Fview_journal_article_content.jsp),
[MH200N](https://www.homesystems-legrandgroup.com/home?p_p_id=it_smc_bticino_homesystems_search_AutocompletesearchPortlet&p_p_lifecycle=0&p_p_state=normal&p_p_mode=view&_it_smc_bticino_homesystems_search_AutocompletesearchPortlet_journalArticleId=2469209&_it_smc_bticino_homesystems_search_AutocompletesearchPortlet_mvcPath=%2Fview_journal_article_content.jsp),
[F453](https://www.homesystems-legrandgroup.com/home?p_p_id=it_smc_bticino_homesystems_search_AutocompletesearchPortlet&p_p_lifecycle=0&p_p_state=normal&p_p_mode=view&_it_smc_bticino_homesystems_search_AutocompletesearchPortlet_journalArticleId=2703566&_it_smc_bticino_homesystems_search_AutocompletesearchPortlet_mvcPath=%2Fview_journal_article_content.jsp),  etc.

- **Zigbee USB Gateways**, such as [BTicino 3578](https://catalogo.bticino.it/BTI-3578-IT), also known as Legrand 088328

**NOTE** The new BTicino Living Now&reg; and Livinglight Smart&reg; wireless systems are not supported by this binding as they do not use the OpenWebNet protocol.

The following Things and OpenWebNet `WHOs` are supported:

### For BUS/SCS

| Category                      |     WHO     |                       Thing Type IDs                       | Description                                                      | Status                                                                                                                                                                                                     |
|-------------------------------|:-----------:|:----------------------------------------------------------:|------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Gateway Management            |    `13`     |                       `bus_gateway`                        | Any IP gateway supporting OpenWebNet protocol should work        | Successfully tested: F452, F453, F453AV,F454, F455, MyHOMEServer1, MyHOME_Screen10, MyHOME_Screen3,5, MH201, MH202, MH200N. Some connection stability issues/gateway resets reported with MH202            |
| Lighting                      |     `1`     |             `bus_on_off_switch`, `bus_dimmer`              | BUS switches and dimmers                                         | Successfully tested: F411/2, F411/4, F411U2, F422, F429. Some discovery issues reported with F429 (DALI Dimmers)                                                                                           |
| Automation                    |     `2`     |                      `bus_automation`                      | BUS roller shutters, with position feedback and auto-calibration | Successfully tested: LN4672M2                                                                                                                                                                              |
| Temperature Control           |     `4`     |  `bus_thermo_zone`, `bus_thermo_sensor`, `bus_thermo_cu`   | Thermo zones management and temperature sensors (probes)         | Successfully tested: H/LN4691, HS4692, KG4691; thermo sensors: L/N/NT4577 + 3455; Central Units (4 or 99 zones) are not fully supported yet. See [Channels - Thermo](#configuring-thermo) for more details |
| Alarm                         |     `5`     |  `bus_alarm_system`, `bus_alarm_zone`                      | BUS Alarm system and zones                                       | Successfully tested: Burglar-alarm Unit 3486  |
| Auxiliary (AUX)               |     `9`     |  `bus_aux`                                                 | AUX commands                                                     | Successfully tested: AUX configured for Burglar-alarm Unit 3486. **Only sending AUX commands is supported**    |
| Basic, CEN & CEN+ Scenarios   | `0`, `15`, `25` | `bus_scenario_control`, `bus_cen_scenario_control`, `bus_cenplus_scenario_control` | Basic and CEN/CEN+ Scenarios events and virtual activation                 | Successfully tested: CEN/CEN+ scenario control: HC/HD/HS/L/N/NT4680 and basic scenario modules F420/IR3456 + L4680 (WHO=0)      |
| Dry Contact and IR Interfaces |    `25`     |                    `bus_dry_contact_ir`                    | Dry Contacts and IR Interfaces                                   | Successfully tested: contact interfaces F428 and 3477; IR sensors: HC/HD/HS/L/N/NT4610      |
| Energy Management             |    `18`     |                     `bus_energy_meter`                     | Energy Management                                                | Successfully tested: F520, F521. Partially tested: F522, F523                               |

### For Zigbee (Radio)

| Category             | WHO    | Thing Type IDs                                        | Description                                                           | Status                               |
| -------------------- | :----: | :---------------------------------------------------: | :-------------------------------------------------------------------: | ------------------------------------ |
| Gateway Management   | `13`   | `zb_gateway`                                          | Zigbee USB Gateway (models: BTI-3578 / LG 088328)                     | Tested: BTI-3578 and LG 088328       |
| Lighting             | `1`    | `zb_dimmer`, `zb_on_off_switch`, `zb_on_off_switch2u` | Zigbee dimmers, switches and 2-unit switches                          | Tested: BTI-4591, BTI-3584, BTI-4585 |
| Automation           | `2`    | `zb_automation`                                       | Zigbee roller shutters                                                |                                      |

## Discovery

Gateway and Things discovery is supported by this binding.

### BUS/SCS Discovery

- BUS Gateway automatic discovery will work only for newer gateways supporting UPnP: F454, MyHOMEServer1, MH201, MH202, MH200N, MyHOME_Screen 10.
For other gateways you can add them manually, see [Thing Configuration](#thing-configuration) below.
- After gateway is discovered and added a connection with default password (`12345`) is tested first: if it does not work the gateway will go offline and an error status will be set. A correct password must then be set in the gateway Thing configuration otherwise the gateway will not become online.
- Once the gateway is online, a second Inbox Scan will discover BUS devices
- BUS/SCS Dimmers must be ON and dimmed (30%-100%) during a Scan, otherwise they will be discovered as simple On/Off switches
  - _KNOWN ISSUE_: In some cases dimmers connected to a F429 Dali-interface are not automatically discovered
- Basic Scenario modules and CEN/CEN+ Scenario Control devices will be discovered by activation only. See [discovery by activation](#discovery-by-activation) for details. After confirming a discovered CEN/CEN+ scenario device from Inbox, activate again its buttons to add button channels automatically

#### Discovery by Activation

BUS devices can also be discovered if they are activated while an Inbox Scan has been started: start a new Scan, wait 15-20 seconds and then _while the Scan is still active_, activate the physical device (for example dim the dimmer) to have it discovered by the binding.
Setting the parameter `discoveryByActivation=true` for a BUS gateway Thing makes discovery by activation always working also when an Inbox Scan hasn't been started.

If a device cannot be discovered automatically from Inbox it's always possible to add it manually, see [Configuring Devices](#configuring-devices).

### Zigbee Discovery

- The Zigbee USB Gateway must be inserted in one of the USB ports of the openHAB computer before a discovery is started
- _**IMPORTANT NOTE:**_ As for other openHAB bindings using the USB/serial ports, on Linux the `openhab` user must be member of the `dialout` group to be able to use USB/serial port; set the group with the following command:

```shell
sudo usermod -a -G dialout openhab
```

- The user will need to logout and login to see the new group added. If you added your user to this group and still cannot get permission, reboot Linux to ensure the new group permission is attached to the `openhab` user.
- Once the Zigbee USB Gateway is added and online, a second Inbox Scan will discover devices connected to it. Because of the Zigbee radio network, device discovery will take ~40-60 sec. Be patient!
- Wireless devices must be part of the same Zigbee network of the Zigbee USB Gateway to discover them. Please refer to [this video by BTicino](https://www.youtube.com/watch?v=CoIgg_Xqhbo) to setup a Zigbee wireless network which includes the Zigbee USB Gateway
- Only powered wireless devices part of the same Zigbee network and within radio coverage of the Zigbee USB Gateway will be discovered. Unreachable or not powered devices will be discovered as _GENERIC_ devices and cannot be controlled
- Wireless control units cannot be discovered by the Zigbee USB Gateway and therefore are not supported

## Thing Configuration

### Configuring BUS/SCS Gateway

Configuration parameters are:

- `host` : IP address / hostname of the BUS/SCS gateway (`String`, _mandatory_)
  - Example: `192.168.1.35`
- `port` : port (`int`, _optional_, default: `20000`)
- `passwd` : gateway password (`String`, _required_ for gateways that have a password. Default: `12345`)
  - Example: `abcde` or `12345`
  - if the BUS/SCS gateway is configured to accept connections from the openHAB computer IP address, no password should be required
  - in all other cases, a password must be configured. This includes gateways that have been discovered and added from Inbox: without a password configured they will remain OFFLINE
- `discoveryByActivation`: discover BUS devices when they are activated also when a device scan hasn't been started from Inbox (`boolean`, _optional_, default: `false`). See [Discovery by Activation](#discovery-by-activation).

Alternatively the BUS/SCS Gateway thing can be configured using the `.things` file, see `openwebnet.things` example [below](#full-example).

### Configuring Wireless Zigbee USB Gateway

Configuration parameters are:

- `serialPort` : the serial port where the Zigbee USB Gateway is connected (`String`, _mandatory_)
  - Examples: `/dev/ttyUSB0` (Linux/RaPi), `COM3` (Windows)

Alternatively the Zigbee USB Gateway thing can be configured using the `.things` file, see `openwebnet.things` example [below](#full-example).

### Configuring Devices

Devices can be discovered automatically using an Inbox Scan after a gateway has been configured and connected.

For any manually added device, you must configure:

- the associated gateway Thing (`Parent Bridge` menu)
- the `where` configuration parameter (`OpenWebNet Address`): this is the OpenWebNet address configured for the device in the BTicino/Legrand system. This address can be found either on the device itself (Physical configuration, using jumpers in case of BUS) or through the MyHOME_Suite software (Virtual configuration). The address can have several formats depending on the device/system:
  - example for BUS/SCS system:
    - light device A=`2` (Area 2), PL=`4` (Light-point 4) --> `where="24"`
    - light device A=`03`, PL=`11` on local bus `01` --> `where="0311#4#01"`
    - scenario control module address `53` --> `where="53"`
    - CEN scenario A=`05`, PL=`12` --> `where="0512"`
    - CEN+ scenario `5`: add `2` before --> `where="25"`
    - dry Contact or IR Interface `99`: add `3` before --> `where="399"`
    - energy meter F520/F521 numbered `1`: add `5` before  --> `where="51"`
    - energy meter F522/F523 numbered `4`: add `7` before and `#0` after --> `where="74#0"`
    - alarm zone `2` --> `where="2"`
  - example for Zigbee devices: `where=765432101#9`. The ID of the device (ADDR part) is usually written in hexadecimal on the device itself, for example `ID 0074CBB1`: convert to decimal (`7654321`) and add `01#9` at the end to obtain `where=765432101#9`. For 2-unit switch devices (`zb_on_off_switch2u`), last part should be `00#9`.

#### Configuring Thermo

In BTicino MyHOME Thermoregulation (WHO=4) each **zone** has associated a thermostat, additional temperature sensors (optional), actuators and heating/conditioning valves. A zone is associated to at least one thermostat and one actuator.

Thermo zones can be configured defining a `bus_thermo_zone` Thing for each zone with the following parameters:

- the `where` configuration parameter (`OpenWebNet Address`):
  - example BUS/SCS zone `1` --> `where="1"`
- the `standAlone` configuration parameter (`boolean`, default: `true`): identifies if the zone is managed or not by a Central Unit (4 or 99 zones). `standAlone=true` means no Central Unit is present in the system.

Temperature sensors can be configured defining a `bus_thermo_sensor` Thing with the following parameters:

- the `where` configuration parameter (`OpenWebNet Address`):
  - example sensor `5` of external zone `00` --> `where="500"`
  - example: slave sensor `3` of zone `2` --> `where="302"`

The (optional) Central Unit can be configured defining a `bus_themo_cu` Thing with the `where` configuration parameter (`OpenWebNet Address`) set to `where="0"`.

##### Thermo Central Unit integration missing points

- Read setPoint temperature and current mode
- Holiday activation command (all zones)
- Discovery

#### Configuring Alarm and Auxiliary (AUX)

**NOTE 1** Receiving AUX messages originating from the BUS is not supported yet, only sending messages to the BUS is supported

**NOTE 2** Alarm messages on BUS are not sent by MyHOMEServer1, therfore this gateway cannot be used to integrate the BTicino Alarm system

BUS Auxiliary commands (WHO=9) can be used to send on the BUS commands to control, for example, external devices or a BTicino Alarm system.

The BTicino Alarm system **cannot** be controlled directly via the OpenWebNet protocol: the only possibility is to use AUX commands and configure your Burglar-alarm Unit (`Automations` section) to execute some commands (e.g. Arm alarm) when it receives a particular AUX OpenWebNet command.
Alarm Automations allow you to run an OpenWebNet command when a particular event occurs; in this case, the events are changes of state of the AUX device (WHO=9) and the command to be performed is a burglar alarm command (WHO=5).

To configure Alarm Automations go to the menu:

`Antitheft -> Automations`

##### Example configuration Automation 1: when AUX-4 goes ON, then ARM all zones

With this configuration when AUX `where=4` goes ON, the Alarm will execute the automation and send command `*5*8##` to ARM all zones:

```text
Name: Arm all zones
Event: command OPEN = *9*1*4##
OPEN command to execute: *5*8##
```

##### Example configuration Automation 2: when AUX-4 goes OFF, then DISARM all zones

```text
Name: Disarm all zones
Event: command OPEN = *9*0*4##
OPEN command to execute: *5*9##
```

##### Example configuration Automation 3: when AUX-5 goes ON, then ARM zones 1, 3, 4

```text
Name: Arm zones 1 3 4
Event: command OPEN = *9*1*5##
OPEN command to execute: *5*8#134##
```

## Channels

### Lighting, Automation, Basic/CEN/CEN+ Scenario Events, Dry Contact / IR Interfaces, Power and Aux channels

| Channel Type ID (channel ID)            | Applies to Thing Type IDs                                     | Item Type     | Description                                                                                                           | Read/Write  |
|-----------------------------------------|---------------------------------------------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------|:-----------:|
| `switch` or `switch_01`/`02` for Zigbee | `bus_on_off_switch`, `zb_on_off_switch`, `zb_on_off_switch2u` | Switch        | To switch the device `ON` and `OFF`                                                                                   |     R/W     |
| `brightness`                            | `bus_dimmer`, `zb_dimmer`                                     | Dimmer        | To adjust the brightness value (Percent, `ON`, `OFF`)                                                                 |     R/W     |
| `shutter`                               | `bus_automation`                                              | Rollershutter | To activate roller shutters (`UP`, `DOWN`, `STOP`, Percent - [see Shutter position](#shutter-position))               |     R/W     |
| `scenario`                              | `bus_scenario_control`                                        | String        | Trigger channel for Basic scenario events [see possible values](#scenario-channels)                                   | R (TRIGGER) |
| `button#X`                              | `bus_cen_scenario_control`, `bus_cenplus_scenario_control`    | String        | Trigger channel for CEN/CEN+ scenario events [see possible values](#scenario-channels)                                | R (TRIGGER) |
| `sensor`                                | `bus_dry_contact_ir`                                          | Switch        | Indicates if a Dry Contact Interface is `ON`/`OFF`, or if an IR Sensor is detecting movement (`ON`), or not  (`OFF`)  |      R      |
| `power`                                 | `bus_energy_meter`                                            | Number:Power  | The current active power usage from Energy Meter                                                                      |      R      |
| `aux`                                   | `bus_aux`                                                     | String        | Possible commands: `ON`, `OFF`, `TOGGLE`, `STOP`, `UP`, `DOWN`, `ENABLED`, `DISABLED`, `RESET_GEN`, `RESET_BI`, `RESET_TRI`. Only `ON` and `OFF` are supported for now |     R/W     |

### Alarm channels

| Channel Type ID (channel ID) | Applies to Thing Type IDs              | Item Type   | Description                                                           | Read/Write  |
|------------------------------|----------------------------------------|-------------|-----------------------------------------------------------------------|:-----------:|
| `state`                      | `bus_alarm_system`, `bus_alarm_zone`   | Switch      | Alarm system or zone is active (`ON`) or inactive (`OFF`)             |      R      |
| `network`                    | `bus_alarm_system`                     | Switch      | Alarm system network state (`ON` = network ok, `OFF` = no network)    |      R      |
| `battery`                    | `bus_alarm_system`                     | String      | Alarm system battery state (`OK`, `FAULT`, `UNLOADED`)                |      R      |
| `armed`                      | `bus_alarm_system`                     | Switch      | Alarm system is armed (`ON`) or disarmed (`OFF`)                      |      R      |
| `alarm`                      | `bus_alarm_zone`                       | String      | Current alarm for the zone  (`SILENT`, `INTRUSION`, `TAMPERING`, `ANTI_PANIC`) |      R      |

### Thermo channels

**NOTE** Channels marked in the table with `Advanced = Y` can be shown on the UI from Thing configuration > Channels tab > check `Show advanced`.

| Channel Type ID (channel ID) | Applies to Thing Type IDs              | Item Type          | Description                                                                                                                           | Read/Write | Advanced |
| ---------------------------- | -------------------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------- | :--------: | :------: |
| `temperature`                | `bus_thermo_zone`, `bus_thermo_sensor` | Number:Temperature | The zone currently sensed temperature                                                                                                 | R          | N        |
| `setpointTemperature`        | `bus_thermo_zone`, `bus_thermo_cu`     | Number:Temperature | The zone or Central Unit setpoint temperature                                                                                         | R/W        | N        |
| `function`                   | `bus_thermo_zone`, `bus_thermo_cu`     | String             | The zone set thermo function (`COOLING`, `HEATING`, `GENERIC`) or the Central Unit thermo function (`COOLING`, `HEATING`)             | R/W        | N        |
| `mode`                       | `bus_thermo_zone`, `bus_thermo_cu`     | String             | The zone set mode (`MANUAL`, `PROTECTION`, `OFF`) or the Central Unit set mode ( `MANUAL`, `PROTECTION`, `OFF`, `WEEKLY`, `SCENARIO`) | R/W        | N        |
| `speedFanCoil`               | `bus_thermo_zone`                      | String             | The zone fancoil speed: `AUTO`, `SPEED_1`, `SPEED_2`, `SPEED_3`                                                                       | R/W        | N        |
| `actuators`                  | `bus_thermo_zone`                      | String             | The zone actuator(s) status: `OFF`, `ON`, `OPENED`, `CLOSED` , `STOP`, `OFF_FAN_COIL`, `ON_SPEED_1`, `ON_SPEED_2`, `ON_SPEED_3`, `OFF_SPEED_1`, `OFF_SPEED_2`, `OFF_SPEED_3`            | R          | Y        |
| `heatingValves`              | `bus_thermo_zone`                      | String             | The zone heating valve(s) status: `OFF`, `ON`, `OPENED`, `CLOSED` , `STOP`, `OFF_FAN_COIL`, `ON_SPEED_1`, `ON_SPEED_2`, `ON_SPEED_3`, `OFF_SPEED_1`, `OFF_SPEED_2`, `OFF_SPEED_3`            | R          | Y        |
| `conditioningValves`         | `bus_thermo_zone`                      | String             | The zone conditioning valve(s) status: `OFF`, `ON`, `OPENED`, `CLOSED` , `STOP`, `OFF_FAN_COIL`, `ON_SPEED_1`, `ON_SPEED_2`, `ON_SPEED_3`, `OFF_SPEED_1`, `OFF_SPEED_2`, `OFF_SPEED_3`            | R          | Y        |
| `localOffset`                | `bus_thermo_zone`                      | String             | The zone local offset status: `OFF`, `PROTECTION`, `MINUS_3`, `MINUS_2` , `MINUS_1`, `NORMAL`, `PLUS_1`, `PLUS_2`, `PLUS_3`           | R          | Y        |
| `remoteControl`              | `bus_thermo_cu`                        | String             | The Central Unit Remote Control status: `ENABLED`, `DISABLED`                                                                         | R          | Y        |
| `batteryStatus`              | `bus_thermo_cu`                        | String             | The Central Unit Battery status: `OK`, `KO`                                                                                           | R          | Y        |
| `weeklyProgram`              | `bus_thermo_cu`                        | Number             | The program number (`1`, `2`, `3`) when Central Unit mode is `WEEKLY`                                                                 | R/W        | N        |
| `scenarioProgram`            | `bus_thermo_cu`                        | Number             | The program number (`1`, `2`, ... ,  `16`) when Central Unit mode is `SCENARIO`                                                       | R/W        | N        |
| `failureDiscovered`          | `bus_thermo_cu`                        | Switch             | Indicates if a Failure was discovered by the Central Unit: `ON`, `OFF`                                                                | R          | Y        |
| `atLeastOneProbeOff`         | `bus_thermo_cu`                        | Switch             | Indicates if at least one probe is in OFF mode: `ON`, `OFF`                                                                           | R          | Y        |
| `atLeastOneProbeProtection`  | `bus_thermo_cu`                        | Switch             | Indicates if at least one probe is in PROTECTION mode: `ON`, `OFF`                                                                    | R          | Y        |
| `atLeastOneProbeManual`      | `bus_thermo_cu`                        | Switch             | Indicates if at least one probe is in MANUAL mode: `ON`, `OFF`                                                                        | R          | Y        |

### Notes on channels

#### `shutter` position

For Percent commands and position feedback to work correctly, the `shutterRun` Thing config parameter must be configured equal to the time (in ms) to go from full UP to full DOWN. It's possible to enter a value manually or set `shutterRun=AUTO` (default) to calibrate `shutterRun` automatically.

_Automatic calibration of `shutterRun`_

If `shutterRun` is set to AUTO a _UP >> DOWN >> Position%_ cycle will be performed automatically the first time a Percent command is sent to the shutter. The binding then relies on the _STOP_ command sent by the actuator at the stop time set in the actuator's advanced configuration in the My Home Suite. Therefore the binding's automatic calibration of `shutterRun` only works correctly if the stop time is set to the time the shutters need to go from full UP to full DOWN. 

Older actuators that come without an ID and do not support advanced configuration via the My Home Suite have a fixed stop time of 60 seconds. In this case auto calibration of the binding cannot be used and `shutterRun` has to be set manually.

_Notes on `shutter` position_

- if `shutterRun` is not set, or is set to `AUTO` but calibration has not been performed yet, then position estimation will remain `UNDEF` (undefined)
- if `shutterRun` is wrongly set higher than the actual runtime, then position estimation will remain `UNDEF`: try to reduce shutterRun until you find the right value
- before adding/configuring roller shutter Things it is suggested to have all roller shutters `UP`, otherwise the Percent command won’t work until the roller shutter is fully rolled up
- if OH is restarted the binding does not know if a shutter position has changed in the meantime, so its position will be `UNDEF`. Move the shutter all `UP`/`DOWN` to synchronize again its position with the binding
- the shutter position is estimated based on UP/DOWN timing: an error of ±2% is normal

#### Scenario channels

Basic Scenarios and CEN/CEN+ Scenarios channels are [TRIGGER channels](https://www.openhab.org/docs/configuration/rules-dsl.html#channel-based-triggers]): they handle events and do not have a state.

A powerful feature is to detect scenario activations and CEN/CEN+ buttons press events to trigger rules in openHAB: this way openHAB becomes a very powerful scenario manager activated by BTicino scenario control modules or by CEN/CEN+ scenarios physical buttons.
See [openwebnet.rules](#openwebnetrules) for examples on how to define rules that trigger on scenarios and on CEN/CEN+ button press events.

It's also possible to send _virtual scenario activation_ and _virtual press_ events on the BUS, for example to enable the activation of MH202 or F420 scenarios from openHAB..
See [openwebnet.sitemap](#openwebnetsitemap) & [openwebnet.rules](#openwebnetrules) sections for examples on how to use the `activateScenario` and `virtualPress` actions connected to a pushbutton on a sitemap.

- basic scenario channels are named `scenario` and possible events are: `SCENARIO_01` ... `SCENARIO_16` (or up to `SCENARIO_20` in case of module IR3456) when a scenario is activated
- CEN/CEN+ channels are named `button#X` where `X` is the button number on the CEN/CEN+ Scenario Control device
  - in the .thing file configuration you can specify the `buttons` parameter to define a comma-separated list of buttons numbers [0-31] configured for the scenario device, example: `buttons=1,2,4`
  - possible events are:
    - for CEN:
      - `START_PRESS` - sent when you start pressing the button
      - `SHORT_PRESS` - sent if you pressed the button shorter than 0,5sec (sent at the moment when you release it)
      - `EXTENDED_PRESS` - sent if you keep the button pressed longer than 0,5sec; will be sent again every 0,5sec as long as you hold pressed (good for dimming rules)
      - `RELEASE_EXTENDED_PRESS` - sent once when you finally release the button after having it pressed longer than 0,5sec
    - for CEN+:
      - `SHORT_PRESS` - sent if you pressed the button shorter than 0,5sec (sent at the moment when you release it)
      - `START_EXTENDED_PRESS` - sent once as soon as you keep the button pressed longer than 0,5sec
      - `EXTENDED_PRESS` - sent after `START_EXTENDED_PRESS` if you keep the button pressed longer; will be sent again every 0,5sec as long as you hold pressed (good for rules involving dimming/volume)
      - `RELEASE_EXTENDED_PRESS` - sent once when you finally release the button after having it pressed longer than 0,5sec

#### `mode` for values WEEKLY and SCENARIO (thermo Central Unit)

There are three WEEKLY and sixteen SCENARIO programs defined for the thermo Central Unit.

In order to activate one of them you have to use two different channels:

- with `mode` you can set the mode (`WEEKLY` or `SCENARIO`)
- with `weeklyProgram` (if `WEEKLY` was set) or with `scenarioProgram` (if `SCENARIO` was set) you can set the program number

Example: if you want to activate SCENARIO number 9 on the thermo Central Unit you have to set `mode` = `SCENARIO` and `scenarioProgram` = `9`.

## Full Example

### openwebnet.things:

BUS gateway and things configuration:

```java
Bridge openwebnet:bus_gateway:mybridge "MyHOMEServer1" [ host="192.168.1.35", passwd="abcde", port=20000, discoveryByActivation=false ] {
      bus_on_off_switch             LR_switch            "Living Room Light"        [ where="51" ]
      bus_dimmer                    LR_dimmer            "Living Room Dimmer"       [ where="0311#4#01" ]
      bus_automation                LR_shutter           "Living Room Shutter"      [ where="93", shutterRun="10050"]      

      bus_energy_meter              CENTRAL_Ta           "Energy Meter Ta"          [ where="51" ]
      bus_energy_meter              CENTRAL_Tb           "Energy Meter Tb"          [ where="52" ]

      bus_thermo_cu                 CU_3550              "99 zones central unit"    [ where="0" ]
      bus_thermo_zone               LR_zone              "Living Room Zone"         [ where="2"]
      bus_thermo_sensor             EXT_tempsensor       "External Temperature"     [ where="500"]

      bus_scenario_control          BR_scenario          "Bedroom Scenario Module"  [ where="95" ]
            
      bus_cen_scenario_control      LR_CEN_scenario      "Living Room CEN"          [ where="51", buttons="4,3,8"]
      bus_cenplus_scenario_control  LR_CENplus_scenario  "Living Room CEN+"         [ where="212", buttons="1,5,18" ]
      bus_dry_contact_ir            LR_IR_sensor         "Living Room IR Sensor"    [ where="399" ]
      
      bus_aux                       Alarm_Control        "Alarm control"            [ where="4" ]

      bus_alarm_system              Alarm_Sys            "Alarm System"             [ where="0"  ]
      bus_alarm_zone                Alarm_Zone_3         "Alarm Zone 3"             [ where="3"  ]
}
```

Zigbee USB Gateway and things configuration - for radio devices:

```java
Bridge openwebnet:zb_gateway:myZBgateway  [ serialPort="COM3" ] {
    zb_dimmer          myZB_dimmer     [ where="765432101#9"]
    zb_on_off_switch   myZB_switch     [ where="765432201#9"]
    zb_on_off_switch2u myZB_2U_switch  [ where="765432300#9"]
}
```

### openwebnet.items:

Example items linked to BUS devices:

NOTE: lights, blinds and zones (thermostat) can be handled from personal assistants (Google Home, Alexa). In the following example `Google Assistant` (`ga="..."`) and `HomeKit` (`homekit="..."`)  were configured according to the [Google official documentation](https://www.openhab.org/docs/ecosystem/google-assistant) and [HomeKit official documentation](https://www.openhab.org/addons/integrations/homekit/)

```java
Switch              iLR_switch                  "Light"                       (gLivingRoom)     { channel="openwebnet:bus_on_off_switch:mybridge:LR_switch:switch", ga="Light", homekit="Lighting" }
Dimmer              iLR_dimmer                  "Dimmer [%.0f %%]"            (gLivingRoom)     { channel="openwebnet:bus_dimmer:mybridge:LR_dimmer:brightness", ga="Light", homekit="Lighting" }

Rollershutter       iLR_shutter                 "Shutter [%.0f %%]"           (gShutters, gLivingRoom) { channel="openwebnet:bus_automation:mybridge:LR_shutter:shutter", ga="Blinds", homekit = "Window" }

Number:Power        iCENTRAL_Ta                 "Power [%.0f %unit%]"                           { channel="openwebnet:bus_energy_meter:mybridge:CENTRAL_Ta:power" }
Number:Power        iCENTRAL_Tb                 "Power [%.0f %unit%]"                           { channel="openwebnet:bus_energy_meter:mybridge:CENTRAL_Tb:power" }

// 99 zones thermo central unit
Group               gCentralUnit                "Thermo Central Unit"
Number:Temperature  iCU_3550_manualset          "Temperature"                          (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:setpointTemperature", ga="thermostatTemperatureSetpoint" }
String              iCU_3550_remote             "Remote Control"                       (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:remoteControl" }
String              iCU_3550_battery            "Battery Status"                       (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:batteryStatus" }
String              iCU_3550_mode               "Mode"                                 (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:mode" }
Number              iCU_3550_wpn                "Weekly Program"                       (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:weeklyProgram" }
Number              iCU_3550_spn                "Scenario Program"                     (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:scenarioProgram" }
String              iCU_3550_func               "Function"                             (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:function" }
Switch              iCU_3550_at1off             "At least one probe in OFF"            (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:atLeastOneProbeOff" }
Switch              iCU_3550_at1pro             "At least one probe in PROTECTION"     (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:atLeastOneProbeProtection" }
Switch              iCU_3550_at1man             "At least one probe in MANUAL"         (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:atLeastOneProbeManual" }
Switch              iCU_3550_failure            "Failure discovered"                   (gCentralUnit)    { channel="openwebnet:bus_thermo_cu:mybridge:CU_3550:failureDiscovered" }

Group               gLivingRoomZone             "Living Room Zone"                              { ga="Thermostat" [ modes="auto=GENERIC,heat=HEATING,cool=COOLING", thermostatTemperatureRange="7,35", useFahrenheit=false ] }
Number:Temperature  iLR_zone_temp               "Temperature [%.1f %unit%]"   (gLivingRoomZone) { channel="openwebnet:bus_thermo_zone:mybridge:LR_zone:temperature", ga="thermostatTemperatureAmbient" }
Number:Temperature  iLR_zone_setTemp            "SetPoint Temperature"        (gLivingRoomZone) { channel="openwebnet:bus_thermo_zone:mybridge:LR_zone:setpointTemperature", ga="thermostatTemperatureSetpoint" }
String              iLR_zone_fanSpeed           "FanSpeed"                    (gLivingRoomZone) { channel="openwebnet:bus_thermo_zone:mybridge:LR_zone:speedFanCoil" }
String              iLR_zone_mode               "Mode"                        (gLivingRoomZone) { channel="openwebnet:bus_thermo_zone:mybridge:LR_zone:mode" }
String              iLR_zone_func               "Function"                    (gLivingRoomZone) { channel="openwebnet:bus_thermo_zone:mybridge:LR_zone:function", ga="thermostatMode" }
String              iLR_zone_actuators          "Actuators"                   (gLivingRoomZone) { channel="openwebnet:bus_thermo_zone:mybridge:LR_zone:actuators" }
String              iLR_zone_hv                 "Heating valves"              (gLivingRoomZone) { channel="openwebnet:bus_thermo_zone:mybridge:LR_zone:heatingValves" }
String              iLR_zone_cv                 "Conditioning valves"         (gLivingRoomZone) { channel="openwebnet:bus_thermo_zone:mybridge:LR_zone:conditioningValves" }

Number:Temperature  iEXT_temp                   "Temperature [%.1f %unit%]"   (gExternal)       { channel="openwebnet:bus_thermo_sensor:mybridge:EXT_tempsensor:temperature" }

String              iCENPlusProxyItem           "CEN+ Proxy Item"


Switch              iLR_IR_sensor               "Sensor"                                        { channel="openwebnet:bus_dry_contact_ir:mybridge:LR_IR_sensor:sensor" }

// alarm aux, alarm unit and a zone
String              iAlarm_Control              "Alarm Control Arm/Disarm"     (gAlarm)         { channel="openwebnet:bus_aux:mybridge:Alarm_Control:aux"}
Switch              iAlarm_System_State         "Alarm state"                  (gAlarm)         { channel="openwebnet:bus_alarm_system:mybridge:Alarm_Sys:state" }
Switch              iAlarm_System_Armed         "Alarm armed"                  (gAlarm)         { channel="openwebnet:bus_alarm_system:mybridge:Alarm_Sys:armed" }
Switch              iAlarm_System_Network       "Alarm network"                (gAlarm)         { channel="openwebnet:bus_alarm_system:mybridge:Alarm_Sys:network" }
String              iAlarm_System_Battery       "Alarm battery"                (gAlarm)         { channel="openwebnet:bus_alarm_system:mybridge:Alarm_Sys:battery" }
Switch              iAlarm_Zone_3_State         "Zone 3 state"                 (gAlarm)         { channel="openwebnet:bus_alarm_zone:mybridge:Alarm_Zone_3:state" }
String              iAlarm_Zone_3_Alarm         "Zone 3 alarm"                 (gAlarm)         { channel="openwebnet:bus_alarm_zone:mybridge:Alarm_Zone_3:alarm" }
```

Example items linked to OpenWebNet Zigbee devices:

```java
Dimmer          iDimmer             "Dimmer [%.0f %%]"                  <DimmableLight>  (gKitchen)                   [ "Lighting" ]  { channel="openwebnet:zb_dimmer:myZBgateway:myZB_dimmer:brightness" }
Switch          iSimpleSwitch       "Kitchen Switch"                    <light>          (gKitchen)                   [ "Lighting" ]  { channel="openwebnet:zb_on_off_switch:myZBgateway:myZB_switch:switch_01" }
Switch          iSwitch_01          "2U first light"                    <light>          (gKitchen)                   [ "Lighting" ]  { channel="openwebnet:zb_on_off_switch2u:myZBgateway:myZB_2U_switch:switch_01" }
Switch          iSwitch_02          "2U second light"                   <light>          (gKitchen)                   [ "Lighting" ]  { channel="openwebnet:zb_on_off_switch2u:myZBgateway:myZB_2U_switch:switch_02" }
```

### openwebnet.sitemap

```perl
sitemap openwebnet label="OpenWebNet Binding Example Sitemap"
{
    Frame label="Living Room"
    {
          Default item=iLR_switch           icon="light"
          Default item=iLR_dimmer           icon="light"
          Default item=iLR_shutter
          Switch  item=iLR_IR_sensor        mappings=[ON="Presence", OFF="No Presence"]
    }

    Frame label="Energy Meters" icon="energy"
    {
          Default item=iCENTRAL_Ta label="General"      icon="energy" valuecolor=[>3000="red"]
          Default item=iCENTRAL_Tb label="Ground Floor" icon="energy" valuecolor=[>3000="red"]
    }

    Frame label="Living Room Thermo"
    {
          Default   item=iLR_zone_temp      label="Temperature" icon="fire" valuecolor=[<20="red"] 
          Setpoint  item=iLR_zone_set       label="Setpoint [%.1f °C]" step=0.5 minValue=15 maxValue=30
          Selection item=iLR_zone_fanSpeed  label="Fan Speed" icon="fan" mappings=[AUTO="AUTO", SPEED_1="Low", SPEED_2="Medium", SPEED_3="High"]
          Switch    item=iLR_zone_mode      label="Mode" icon="settings"
          Selection item=iLR_zone_func      label="Function" icon="heating" mappings=[HEATING="Heating", COOLING="Cooling", GENERIC="Heating/Cooling"]
          Default   item=iLR_zone_actuators label="Actuators status"
          Default   item=iLR_zone_hv        label="Heating valves status"
          Default   item=iLR_zone_cv        label="Conditioning valves status"
    }
    
    Frame label="CEN+ Scenario activation"
    {
          Switch    item=iCENPlusProxyItem  label="My CEN+ scenario" icon="movecontrol"  mappings=[ON="Activate"]
    }
    
    Frame label="Alarm"
    {
         Switch  item=iAlarm_System_State       label="Alarm state"
         Switch  item=iAlarm_Control            label="Arm/Disarm alarm"   icon="shield"
         Switch  item=iAlarm_System_Armed       label="Armed"              icon="shield"
         Switch  item=iAlarm_System_Network     label="Network"            icon="network"
         Default item=iAlarm_System_Battery     label="Battery"            icon="battery"
         Switch  item=iAlarm_Zone_3_State       label="Zone 3 state"
         Default item=iAlarm_Zone_3_Alarm       label="Zone 3 alarm"       icon="siren"
    }
}
```

### openwebnet.rules

```xtend
rule "Basic scenario WHO=0"
// A "SCENARIO_02" event started from Scenario device where=95 will switch ON iLR_switch %
when
    Channel "openwebnet:bus_scenario_control:mybridge:BR_scenario:scenario" triggered SCENARIO_02
then
    sendCommand(iLR_switch, ON)  
end


rule "CEN+ virtual press from OH button"
/* This rule triggers when the proxy item iCENPlusProxyItem is activated, for example from a button on WebUI/sitemap.
When activated it sends a "virtual short press" event (where=212, button=5) on the BUS 
*/
when 
    Item iCENPlusProxyItem received command
then
    val actions = getActions("openwebnet","openwebnet:bus_cenplus_scenario_control:mybridge:212")
    actions.virtualPress("SHORT_PRESS", 5)
end


rule "CEN dimmer increase"
// A "start press" event on CEN where=51, button=4 will increase dimmer%
when
    Channel "openwebnet:bus_cen_scenario_control:mybridge:51:button#4" triggered START_PRESS
then
    sendCommand(iLR_dimmer, INCREASE)  
end


rule "CEN dimmer decrease"
// A "release extended press" event on CEN where=51, button=4 will decrease dimmer%
when
    Channel "openwebnet:bus_cen_scenario_control:mybridge:51:button#4" triggered RELEASE_EXTENDED_PRESS
then
    sendCommand(iLR_dimmer, DECREASE)  
end

```

## Notes

The OpenWebNet protocol is maintained and Copyright by BTicino/Legrand. The documentation of the protocol if freely accessible for developers on the [Legrand developer web site](https://developer.legrand.com/documentation/open-web-net-for-myhome/)

## Special thanks

Special thanks for helping on testing this binding go to:
[@m4rk](https://community.openhab.org/u/m4rk/),
[@bastler](https://community.openhab.org/u/bastler),
[@gozilla01](https://community.openhab.org/u/gozilla01),
[@enrico.mcc](https://community.openhab.org/u/enrico.mcc),
[@k0nti](https://community.openhab.org/u/k0nti/),
[@gilberto.cocchi](https://community.openhab.org/u/gilberto.cocchi/),
[@llegovich](https://community.openhab.org/u/llegovich),
[@gabriele.daltoe](https://community.openhab.org/u/gabriele.daltoe),
[@feodor](https://community.openhab.org/u/feodor),
[@aconte80](https://community.openhab.org/u/aconte80),
[@rubenfuser](https://community.openhab.org/u/rubenfuser),
[@stamate_viorel](https://community.openhab.org/u/stamate_viorel),
[@marchino](https://community.openhab.org/u/marchino)
and many others at the fantastic openHAB community!
