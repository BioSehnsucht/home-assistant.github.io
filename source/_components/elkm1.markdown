---
layout: page
title: "Elk M1 Controller"
description: "Instructions how to setup the Elk M1 controller within Home Assistant."
date: 2017-10-06 01:10
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

Home Assistant is capable of communicating with any binary sensor, light, sensor, switch, and thermostat that is configured on the controller. Using the programs on the controller, custom binary sensors and switches can also be created.

To integrate your Elk M1 controller with Home Assistant, add the following section to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
elkm1:
  host: socket://ELKM1_ADDRESS:ELKM1_PORT
```
Configuration variables:

- **host** (*Required*): The host entry should be either a serial device (e.g. `/dev/ttyUSB0`) or IP and port specified using `socket` URL prefix (e.g. `socket://192.168.1.5:2101`) when using a ELK-M1XEP or ELK-C1M1 network interface. Currently only insecure port (default 2101) is supported.
- **area** (*Optional*): Configure settings specific to handling of Elk Areas (partitions) subdomain.
- **counter** (*Optional*): Configure settings specific to handling of Elk Counters subdomain.
- **keypad** (*Optional*): Configure settings specific to handling of Elk Keypads subdomain.
- **output** (*Optional*): Configure settings specific to handling of Elk Outputs subdomain.
- **setting** (*Optional*): Configure settings specific to handling of Elk Custom Settings subdomain.
- **task** (*Optional*): Configure settings specific to handling of Elk Tasks subdomain.
- **thermostat** (*Optional*): Configure settings specific to handling of Elk Thermostats subdomain.
- **user** (*Optional*): Configure settings specific to handling of Elk Users subdomain.
- **x10** (*Optional*): Configure settings specific to handling of Elk Lighting Control (Elk supports more than just X-10 platform, but all supported platforms are mapped into the X-10 device space) subdomain.
- **zone** (*Optional*): Configure settings specific to handling of Elk Zones (inputs) subdomain.

For the optional settings `area`, `counter`, `keypad`, `output`, `task`, `thermostat`, `setting`, `x10`, and `zone`, they can each have the following subdomain configuration added:

- **enabled** (*Optional*): If enabled is `false`, the subdomain will be skipped (this can speed up startup if you do not need it, for example you don't have any lighting controls connected to the Elk you can disable the x10 subdomain). Default is `true`.
- **autoexclude** (*Optional*): If autoexclude is `false`, all subdomain devices which pass the include/exclude filtering will appear even if they don't appear to be configured / actually exist. If autoexclude is `true`, then devices which don't appear to exist will also be excluded even if they were otherwise included. Default is `true`.
- **autohide** (*Optional*): If autohide is `false`, all subdomain devices will show up in the default view of Home Assistant even if they don't appear to actually exist / be configured on the Elk device. If `true`, the Elk platform will attempt to hide missing or otherwise non-configured devices from view, but they will still be available in the states view and to automation rules. Default is `true`.
- **include** (*Optional*): List of ranges of devices to include. Included devices override behavior of autohide functionality and include them in appearing in Home Assistant regardless of whether they appear to exist.
- **exclude** (*Optional*): List of ranges of devices to exclude. Excluded devices override behavior of autohide functionality and exclude them from appearing in Home Assistant regardless of whether they appear to exist.

Inclusions are performed first, then exclusions, so you can "hole punch" exclusions in the middle of an included range. If no include list is specified, all possible devices will be included by default (unless autoexclude removes them). If no exclude list is specified, no devices will be excluded by default (unless autoexclude removes them). For both include and exclude lists, each list item can be a range or a single item. In all cases except `x10` these are expected to be integers, for `x10` they can be either integers (i.e. a1 is 1, b1 is 17, etc) or housecodeDEVICENUMBER combinations (e.g. a1, d12, ...).

Example configuration showing how the above options can be used:
```yaml
elkm1:
  host: socket://192.168.1.5:2101
  area:
    exclude: 5-8
  zone:
    exclude: 11-16, 19-192, 199-208
  output:
    include: 1-10, 196-198
  x10:
    include: a1-e16
    exclude: b12-d5
  task:
    include: 1-4
  user:
    exclude: 30-200
```

Valid ranges for each subdomain's include and exclude are as follows:
- **area** : 1 to 8
- **counter** : 1 to 64
- **keypad** : 1 to 16
- **output** : 1 to 208
- **setting** : 1 to 20
- **task** : 1 to 32
- **thermostat** : 1 to 16
- **user** : 1 to 200
- **x10** : a1 to a16, b1 to b16, ... p1 to p16
- **zone** : 1 to 208

Once the Elk controller is configured, it will automatically import any sensors (zone inputs), lights (x10/etc), and switches (outputs) it finds, constrained by the configured include and exclude lists, and autohide behavior (if enabled).

### {% linkable_title Automation Examples %}

## Turn lights on / off by keypad button 
(credit: [lmamakos](https://community.home-assistant.io/t/elk-m1-interface/4461/155) )

Whether or not Elk is controlling some of your lighting, you can use function keys on the Elk to trigger tasks which will momentarily activate a corresponding switch in Home Assistant which can then trigger automations in response, allowing you to control lighting or other functions in response.

```yaml
- alias: elk Ext Lights On
  initial_state: True
  trigger:
    platform: state
    entity_id: switch.elk_task_009
    to: "on"
  action:
    - service: homeassistant.turn_on
      entity_id: group.outside_lights

- alias: elk Ext Lights Off
  initial_state: True
  trigger:
    platform: state
    entity_id: switch.elk_task_010
    to: "on"
  action:
    - service: homeassistant.turn_off
      entity_id: group.outside_lights
```

## Notify when user arms / disarms / accesses area/keypad

Elk doesn't have names for Areas, just numbers, but it does have names for Keypads and Users. You can thus trigger an informative notification when an area is armed, disarmed, or accessed (typically only see access for access control applications), without having to manually set up a rule for every combination of area, action, and user. You can trigger on `last_armed_at` for arming events, `last_disarmed_at` for disarming events, and `last_user_at` for access events.

```yaml
sensor:
  - platform: template
    sensors:
      elk_area_1_last_user_at:
        value_template: '{{ states.alarm_control_panel.elk_area_1.attributes.last_user_at }}'
        friendly_name: 'ELK Area 1 last accessed at'

automation:
  - alias: 'Main Area Accessed'
    trigger:
      platform: state
      entity_id: sensor.elk_area_1_last_user_at
    action:
     - service: notify.YOUR_NOTIFY_SERVICE
       data_template:
         message: "Main Area ({{ states.alarm_control_panel.elk_area_1.attributes.last_keypad_name }}) accessed by {{ states.alarm_control_panel.elk_area_1.attributes.last_user_name }}"
```

