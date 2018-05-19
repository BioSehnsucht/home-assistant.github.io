---
layout: page
title: "Elk M1 Light"
description: "Instructions how to integrate Elk M1 lights into Home Assistant."
date: 2018-05-18 00:00
sidebar: true
comments: false
sharing: true
footer: true
logo: elk.png
ha_category: Light
ha_iot_class: "Local Push"
---

The `elkm1` platform allows you to control lighting automation systems attached to your [Elk M1](https://www.elkproducts.com/m1_controls.html) from within Home Assistant, as well as react to changes in their state from other sources.

They will be automatically discovered if the `elkm1` component is loaded.

Note that all devices (whether they be lights or switched outlets, X10 or UPB or Z-Wave, etc) will appear as lights, as the Elk doesn't differentiate between lighting and non-lighting PLC devices via it's API (even though you can specify the type in ElkRP). It also doesn't differentiate between varieties of PLC, they're all mapped into a flat 256 device space.