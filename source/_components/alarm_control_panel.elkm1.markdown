---
layout: page
title: "Elk M1 Alarm Control Panel"
description: "Instructions how to integrate Elk M1 alarm control panel into Home Assistant."
date: 2018-05-18 00:00
sidebar: true
comments: false
sharing: true
footer: true
logo: elk.png
ha_category: Alarm
ha_iot_class: "Local Push"
---

The `elkm1` platform allows you to control your [Elk M1](https://www.elkproducts.com/m1_controls.html) from within Home Assistant, as well as react to changes in their state from other sources.

Each Area (alternatively known as a partition) configured on your Elk M1 will be represented as an `alarm_control_panel` allowing you to arm and disarm, as well as react to changes in state (including triggering automations based on who has entered a code)

They will be automatically discovered if the `elkm1` component is loaded.

Additional Attributes:
- **Alarm** : Indicates whether the Area is in alarm
- **Arm Status** : Indicates the status of the Area (i.e., armed, exit timer running, etc - this will reflect the full variety of statuses the Area can be in on the Elk M1)
- **Last Armed At** : The time Area was last armed
- **Last Disarmed At** : The time Area was last disarmed
- **Last User At**: The time a user last accessed this area
- **Last User Name**: The user name of the last user to access this area
- **Last User Number**: The user number of the last user to access this area
- **Last Keypad Name** : The keypad name of the last keypad used to arm, disarm, or access Area
- **Last Keypad Number** : The keypad number of the last keypad used to arm, disarm, or access Area
- **Readiness** : Indicates the 'arm up' / arming readiness condition of the Area (i.e., whether ready to arm and if zones are violated)