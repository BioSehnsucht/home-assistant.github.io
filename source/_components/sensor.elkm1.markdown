---
layout: page
title: "Elk M1 Sensor"
description: "Instructions how to integrate Elk M1 sensors into Home Assistant."
date: 2018-05-18 00:00
sidebar: true
comments: false
sharing: true
footer: true
logo: elk.png
ha_category: Sensor
ha_iot_class: "Local Push"
---

The `elkm1` platform allows you to get data from your [Elk M1](https://www.elkproducts.com/m1_controls.html) connected inputs from within Home Assistant.

They will be automatically discovered if the `elkm1` component is loaded.

Supported devices mapped as sensors include:
 * Counter Values (`sensor.elkm1_counter_*`)
 * Custom Settings (`sensor.elkm1_setting_*`) - aka Custom Values 
 * Keypads (`sensor.elkm1_keypad_*`) for temperature
 * Thermostats (`sensor.elkm1_thermostat_*`) for temperature
 * Zones (`sensor.elkm1_zone_*`) for regular input, analog voltage and temperature zones
 
### Counter Values

Currently read-only, although they are settable in the Elk.

### Custom Settings

Currently read-only, although they are settable in the Elk.

Additional attributes:
- **Value Format**: Reflects whether this is a `Number`, `Timer`, or `Time of Day` setting.

### Keypads

Additional attributes:
- **Area**: Area this keypad is assigned to
- **Last User At**: The time a user last accessed this keypad
- **Last User Name**: The user name of the last user to access this keypad
- **Last User Number**: The user number of the last user to access this keypad

`Last User At`, `Last User Name`, and `Last User Number` are set whenever a user arms, disarms, or merely accesses the keypad. When using RFID or similar access controls, the 'access' event will occur when someone uses their device to gain entry.

### Thermostats

For changing thermostat settings, see the Elk M1 Climate platform. This is just a sensor that reflects temperature and other attributes.

Additional attributes:
- **Humidity**: Humidity reported by thermostat

### Zones

For regular input zones, state is `Normal` or `Violated`. For analog voltage zones, state is input volts. For temperature zones, state is temperature.

Additional attributes:
- **Area**: Area the zone is assigned to
- **Definition**: Zone definition, such as "Burglar interior", "Burglar perimeter instant", etc (refer to Elk manual for possible definitions).
- **Physical Status**: Physical state of the zone ("Eol" for End-of-Line terminated, "Open" for open circuit (EOL termination cut), "Short" for shorted circuit (EOL termination bypassed), or "Unconfigured" (not configured in the Elk panel))