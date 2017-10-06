---
layout: page
title: "Elk M1 Alarm Control Panel"
description: "Instructions how to integrate Elk M1 alarm control panel into Home Assistant."
date: 2017-10-10 23:00
sidebar: true
comments: false
sharing: true
footer: true
logo: elk.png
ha_category: Alarm
ha_iot_class: "Local Push"
---

The `elkm1` platform allows you to control your [Elk M1](https://www.elkproducts.com/m1_controls.html) from within Home Assistant, as well as react to changes in their state from other sources.

Each Area (also known as partition) configured on your Elk M1 will be represented as an `alarm_control_panel` allowing you to arm and disarm, as well as react to changes in state (including triggering automations based on who has entered a code)

They will be automatically discovered if the `elkm1` component is loaded.

Attributes:

- **Readiness** : Indiciates the 'arm up' / arming readiness condition of the Area (i.e., whether ready to arm and if zones are violated)
- **Status** : Indicates the status of the Area (i.e., armed, exit timer running, etc - this will reflect the full variety of statuses the Area can be in on the Elk M1) - may eventually be removed and additional `STATE_ALARM_*` definitions added to HASS
- **State** : Indicates the state of the Area (i.e., armed, triggered, etc - limited to standard states defined by Home Assistant)
- **Alarm** : Indicates whether the Area is in alarm
- **friendly_name** : Name of the Area as reported by Elk M1
- **last_armed_at** : Last time Area was armed (seconds since Unix Epoch)
- **last_disarmed_at** : Last time Area was disarmed (seconds since Unix Epoch)
- **last_user_at** : Last time a user code was entered, whether it be to arm, disarm, or access an Area (seconds since Unix Epoch)
- **last_user_name** : User name of last user code to be entered in Area, as reported by Elk M1
- **last_user_num** : User number of last user code to be entered in Area
- **last_keypad_name** : Name of last keypad used to arm, disarm, or access Area, as reported by Elk M1
- **last_keypad_num** : Last Keypad number used to arm, disarm, or access Area
