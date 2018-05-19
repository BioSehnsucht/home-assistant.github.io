---
layout: page
title: "Elk M1 Switch"
description: "Instructions how to integrate Elk M1 switch into Home Assistant."
date: 2018-05-18 00:00
sidebar: true
comments: false
sharing: true
footer: true
logo: elk.png
ha_category: Switch
ha_iot_class: "Local Push"
---

The `elkm1` platform allows you to control outputs and activate tasks on your [Elk M1](https://www.elkproducts.com/m1_controls.html) from within Home Assistant, as well as react to changes in their state from other sources.

They will be automatically discovered if the `elkm1` component is loaded.

### Outputs

Outputs can be turned on or off.

### Tasks

Tasks can be activated (triggered), but not turned off. The UI will reflect a momentary state change to show that it has been activated, but they do not have any state.

Additional attributes:
- **Last Activated**: Time the task was last activated. This can be used with a template sensor to trigger an automation when it changes.