---
layout: page
title: "Elk M1 Binary Sensor"
description: "Instructions how to integrate Elk M1 binary sensors into Home Assistant."
date: 2017-10-10 23:00
sidebar: true
comments: false
sharing: true
footer: true
logo: elk.png
ha_category: Binary Sensor
ha_iot_class: "Local Push"
---

The `elkm1` platform allows you to get data from your [Elk M1](https://www.elkproducts.com/m1_controls.html) connected inputs from within Home Assistant.

They will be automatically discovered if the `elkm1` component is loaded.

Supported devices mapped as binary sensors include:
 * Zones (`binary_sensor` for regular input zones, `sensor` for analog voltage and temperature zones)

