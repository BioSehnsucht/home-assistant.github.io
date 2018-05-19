---
layout: page
title: "Elk M1 Controller"
description: "Instructions how to setup the Elk M1 controller within Home Assistant."
date: 2018-05-18 00:00
sidebar: true
comments: false
sharing: true
footer: true
logo: elk.png
ha_category: Hub
ha_release: 
ha_iot_class: "Local Push"
---

The Elk M1 is a home security and automation controller that is capable of not just being an alarm control panel, but also controlling various other devices, including X10, Insteon, and other lighting systems. 
The Elk M1 controller is manufactured by [Elk Products](https://www.elkproducts.com/m1_controls.html).

### {% linkable_title Basic Configuration %}

Home Assistant is capable of communicating with any binary sensor, light, sensor, switch, and thermostat that is configured on the controller.

To integrate your Elk M1 controller with Home Assistant, add one of the following sections to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry using serial (Linux/Unix/Mac OS)
elkm1:
  host: serial:///dev/ttyUSB0
```

```yaml
# Example configuration.yaml entry using serial (Windows)
elkm1:
  host: serial://COM1
```

```yaml
# Example configuration.yaml entry using TCP
elkm1:
  host: elk://192.168.1.2
```

```yaml
# Example configuration.yaml entry using SSL TCP
elkm1:
  host: elks://192.168.1.2
  username: myname
  password: mysecret
```


When specifying the host, there are three supported connection methods: `serial` (direct attached to the host), `elk` TCP, and `elks` (SSL TCP).
- **`serial`** : Use the form `host: serial://DEVICE` (ex: `host: serial:///dev/ttyUSB0`, `host: serial://COM1`). Optionally specify baud rate if it is not the Elk default of 115200 (ex: `host: serial:///dev/ttyUSB0:38400`, `host: serial://COM1:38400`).
- **`elk`** : Use the form `host: elk://ELKM1_ADDRESS` (ex: `host: elk://192.168.1.2`). Optionally specify the port, default is the Elk default of 2101 (ex: `host: elk://192.168.1.2:2101`).
- **`elks`** : Use the form `host: elks://ELKM1_ADDRESS` (ex: `host: elks://192.168.1.2`). Optionally specify the port, default is the Elk default of 2301 (ex: `host: elks://192.168.1.2:2301`). **Must specify Username and Password settings.** (see below)

Configuration variables:

- **host** (*Required*): See above
- **username** (*Required for `elks`*): When using `elks` connection method, specify the username to connect with.
- **password** (*Optional for `elks`*): When using `elks` connection method, specify the password to connect with.
- **area** (*Optional*): Configure settings specific to handling of Elk Areas (aka partitions).
- **counter** (*Optional*): Configure settings specific to handling of Elk Counters.
- **keypad** (*Optional*): Configure settings specific to handling of Elk Keypads.
- **output** (*Optional*): Configure settings specific to handling of Elk Outputs.
- **plc** (*Optional*): Configure settings specific to handling of Elk Lighting Control.
- **setting** (*Optional*): Configure settings specific to handling of Elk Custom Settings.
- **task** (*Optional*): Configure settings specific to handling of Elk Tasks.
- **thermostat** (*Optional*): Configure settings specific to handling of Elk Thermostats.
- **user** (*Optional*): Configure settings specific to handling of Elk Users.
- **zone** (*Optional*): Configure settings specific to handling of Elk Zones (inputs).

For the optional settings `area`, `counter`, `keypad`, `output`, `plc`, `task`, `thermostat`, `setting`, and `zone`, they can each have the following subdomain configuration added:

- **enabled** (*Optional*): If enabled is `false`, the subdomain will be skipped. Default is `true`.
- **include** (*Optional*): YAML list of ranges of devices to include. Default is to include all devices. Specifying inclusion list includes only those devices listed.
- **exclude** (*Optional*): YAML list of ranges of devices to exclude. Default is to exclude no devices. Specify exclusion list excludes all devices listed.
- **show** (*Optional*): YAML list of ranges of devices to force show. 
- **hide** (*Optional*): YAML list of ranges of devices to force hide. These are still active, just have their `hidden` attribute set to `true` so that they do not appear on the frontend in Home Assistant.

Inclusions are performed first, then exclusions, so you can "hole punch" exclusions in the middle of an included range. For both include and exclude lists, each list item can be a range or a single item. In all cases except `plc` these are expected to be integers, for `plc` they can be either integers (i.e. a1 is 1, b1 is 17, etc) or X-10 style housecodeDEVICENUMBER combinations (e.g. a1, d12, ...).

Show / Hide logic is combined such that if a device is not listed in either Show or Hide, it is hidden automatically based on various device-specific factors if it does not appear to be configured as an actual device, etc. If the device is listed in Show, it is always shown, regardless of whether it appears to exist. If the device is listed in Hide, it is always hidden regardless of whether it appears to exist. Similar to Include / Exclude, show is applied first and then hide, so again you can "hole punch" the lists.

Example configuration showing how the above options can be used:
```yaml
elkm1:
  host: elk://192.168.1.5
  area:
    exclude: 
      - 5-8
  zone:
    exclude: 
      - 11-16
      - 19-192
      - 199-208
  output:
    include: 
      - 1-10
      - 196-198
  plc:
    include: 
      - a1-e16
    exclude: 
      - b12-d5
  task:
    include: 
      - 1-4
  user:
    exclude: 
      - 30-200
  keypad:
    exclude:
      - 4-16
    hide:
      - 2-3
```

Valid ranges for each subdomain's include and exclude are as follows:
- **area** : 1 to 8
- **counter** : 1 to 64
- **keypad** : 1 to 16
- **output** : 1 to 208
- **plc** : a1 to a16, b1 to b16, ... p1 to p16; alternatively 1 to 256
- **setting** : 1 to 20
- **task** : 1 to 32
- **thermostat** : 1 to 16
- **user** : 1 to 200
- **zone** : 1 to 208

Once the Elk controller is configured, it will automatically import any sensors (zone inputs), lights (plc), and switches (outputs) it finds, constrained by the configured include and exclude lists.

### {% linkable_title Automation Examples %}

#### Turn lights on / off by keypad button 
(credit: [lmamakos](https://community.home-assistant.io/t/elk-m1-interface/4461/155) )

Whether or not Elk is controlling some of your lighting, you can use function keys on the Elk to trigger tasks which will momentarily activate a corresponding switch in Home Assistant which can then trigger automations in response, allowing you to control lighting or other functions in response.

```yaml
- alias: elk Ext Lights On
  initial_state: True
  trigger:
    platform: state
    entity_id: switch.elkm1_task_009
    to: "on"
  action:
    - service: homeassistant.turn_on
      entity_id: group.outside_lights

- alias: elk Ext Lights Off
  initial_state: True
  trigger:
    platform: state
    entity_id: switch.elkm1_task_010
    to: "on"
  action:
    - service: homeassistant.turn_off
      entity_id: group.outside_lights
```

#### Notify when user arms / disarms / accesses area/keypad

Elk doesn't have names for Areas, just numbers, but it does have names for Keypads and Users. You can thus trigger an informative notification when an area is armed, disarmed, or accessed (typically only see access for access control applications), without having to manually set up a rule for every combination of area, action, and user. You can trigger on `Last Armed At` for arming events, `Last Disarmed At` for disarming events, and `Last User At` for access events. Keep in mind that for these events to work as expected, you must have the relevant keypads and areas both included (not excluded) in the configuration. You can hide them, as long as they're included.

```yaml
sensor:
  - platform: template
    sensors:
      elk_area_1_last_user_at:
        value_template: {% raw %}'{{ states.alarm_control_panel.elkm1_area_001.attributes.["Last User At"] }}'{% endraw %}
        friendly_name: 'ELK Area 1 last accessed at'

automation:
  - alias: 'Main Area Accessed'
    trigger:
      platform: state
      entity_id: sensor.elk_area_1_last_user_at
    action:
     - service: notify.YOUR_NOTIFY_SERVICE
       data_template:
         message: {% raw %}'Main Area ({{ states.alarm_control_panel.elkm1_area_001.attributes["Last Keypad Name"] }}) accessed by {{ states.alarm_control_panel.elkm1_area_001.attributes["Last User Name"] }}'{% endraw %}
```
