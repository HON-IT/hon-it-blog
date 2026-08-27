---
title: "Wi-Fi Transmit Power Part 1: Why More Is Not Always Better"
date: 2026-06-22
lastmod: 2026-08-27
draft: false
ShowToc: true
author: "Nick Cuypers"
description: "A practical explanation of Wi-Fi transmit power, including dBm, EIRP, antenna gain, receive sensitivity, the LCMI, and the loud AP problem."
tags: ["Wi-Fi", "RF", "CWNA", "CWDP", "CWNE", "Transmit Power", "EIRP"]
categories: ["Wireless Engineering"]
---

**Updated 27 August 2026:** This article was substantially rewritten as part of my CWNE journey.

Transmit power is one of those wireless settings that looks simple at first. More power should mean better Wi-Fi, right? Not always.

In Wi-Fi, increasing the transmit power of an access point can improve what the client hears from the AP, but that doesn't automatically improve what the AP hears from the client. Wi-Fi is bidirectional, which means both sides of the conversation matter.

This post looks at AP and client transmit power, EIRP, antenna gain, receive sensitivity, and why a "loud AP" can sometimes create more problems than it solves.

This is part 1 of a small transmit power series:

* **Part 1:** Why more power is not always better
* **Part 2:** Single-AP lab test
* **Part 3:** Three-AP cell overlap and roaming test

This first post focuses on the theory and design concepts. The lab results will follow in the next two parts.

---

## The Loud AP Problem

When users complain about wireless coverage, one of the first things that comes to mind is to increase AP transmit power.

The logic seems reasonable:

* The client has weak signal, so make the AP louder.

It might look like it helps, but it only improves one direction of the link.

When we increase AP transmit power, we are changing how loudly the AP transmits, which leads to:

* Improved RSSI (Received Signal Strength Indicator) on the client side.
* More signal bars on the client.
* The operating system shows a better connection.
* Coverage seems to increase.

So, problem solved, right? WRONG!

The problem is that the client did NOT become louder.

Think of the AP as someone speaking through a megaphone and the client as someone in the audience without a microphone. Turning up the megaphone helps the audience hear the speaker, but it does NOT help the speaker hear someone in the audience talking back.

The AP still needs to receive the client's transmissions.

If the client transmits at lower power, has a small antenna or poor radio design, or is located behind an obstacle, the uplink can still be weak.

This is the classic loud AP problem.

---

## Wi-Fi Is Bidirectional

Wi-Fi is not a one-way technology where the AP simply talks and the clients listen.

A working Wi-Fi connection requires communication in both directions:

* The client must hear the AP (downlink).
* The AP must hear the client (uplink).

This applies to management frames, control frames, and data frames. Association, authentication, roaming, acknowledgements, retries, rate selection, and application traffic all depend on two-way communication.

If only the downlink is strong, the client may believe it has good coverage, while the AP struggles to receive frames successfully from that client.

That can result in:

* Higher retries.
* Lower data rates.
* Poor roaming behaviour.
* Unstable voice or video calls.
* Slow application performance.
* Clients staying associated too long.
* Inconsistent user experience.

A strong signal indicator on the client does not always mean the link is healthy in both directions. It only shows how well the client hears the AP, not how well the AP hears the client.

---

## AP vs Client Transmit Power

Out of the box, an AP and a client usually do not transmit at the same power.

Many APs start at their highest allowed transmit power when they first boot. In an enterprise network, this can later be adjusted manually or automatically by transmit power control. The maximum power available to the AP depends on:

* The regulatory domain.
* The band and channel.
* The AP model and radio capabilities.
* The antenna type and gain.

Client devices follow many of the same regulatory limitations, but they also have something an AP normally does not have to worry about: battery life.

The actual transmit power and power-saving behaviour are controlled by the client's hardware, firmware, drivers, and operating system. This means the behaviour can differ between client types and can even change depending on the device state and wireless conditions:

* Laptops usually have more room for multiple antennas, better antenna separation, and a larger battery. They can still use power-saving features, but generally have fewer physical and energy constraints than smaller clients.
* Mobile phones have less room for antenna placement and a smaller battery. They use Wi-Fi power-saving mechanisms to reduce energy consumption, and their Wi-Fi behaviour can change when power-saving modes are active.
* Smaller devices, such as handheld scanners, voice handsets, payment terminals, and IoT sensors, are often designed for long battery life. They may use older radios, fewer spatial streams, a more constrained antenna design, lower transmit power, and more aggressive power-saving behaviour.

Power saving does not always mean that a client simply transmits at a lower dBm value. It can also mean sleeping longer, scanning less often, or keeping the radio active for a shorter period. The exact behaviour is decided by the client.

These are general examples, NOT fixed rules. A modern scanner may perform better than an old laptop. The only way to know is to check the specifications and test the actual device.

This is why a wireless design cannot be based only on the best client in the environment. We need to identify the LCMI: the Least Capable, Most Important client the network still needs to support. We will look at how to identify the LCMI later in this post.

---

## Understanding dBm

To properly compare AP and client transmit power, we first need to understand dBm.

dBm is an absolute power measurement referenced to 1 milliwatt. It is commonly used to show transmit power and received signal strength, which is often displayed as RSSI.

In this case, RSSI shows how strongly the client receives the AP. The AP measures the client's signal separately, so the client-side RSSI does NOT show how strongly the AP receives the client.

A few useful reference points are:

|  Power | Meaning       |
| -----: | ------------- |
|  0 dBm | 1 mW          |
| 10 dBm | 10 mW         |
| 20 dBm | 100 mW        |
| 30 dBm | 1000 mW / 1 W |

Transmit power is usually shown as a positive value, while RSSI is normally shown as a negative value. With negative values, a number closer to zero represents a stronger signal:

* -50 dBm is stronger than -60 dBm.
* -60 dBm is stronger than -70 dBm.

The important thing to understand is that dBm uses a logarithmic scale. The difference between 17 dBm and 20 dBm may look small, but 20 dBm is approximately twice the transmit power of 17 dBm.

This is where the difference between dB and dBm becomes important:

* dBm represents an absolute power level.
* dB represents a relative change between two power levels.

Some useful rules to remember are:

* An increase of 3 dB approximately doubles the power.
* A decrease of 3 dB approximately halves the power.
* An increase of 10 dB multiplies the power by ten.
* A decrease of 10 dB reduces the power to one tenth.

For example, lowering an AP from 20 dBm to 17 dBm cuts its transmit power approximately in half. Lowering it further to 14 dBm cuts it in half again, leaving approximately one quarter of the original transmit power.

---

## What Is EIRP?

The transmit power configured on an AP does not always tell the complete story.

The radio provides the transmit power, but the antenna adds gain and any cables or connectors introduce loss. EIRP combines these values and represents the effective power radiated by the antenna.

EIRP stands for Equivalent Isotropically Radiated Power.

A simplified formula is:

> EIRP = Transmit Power + Antenna Gain - Cable Loss

For example:

```text
20 dBm transmit power
+ 5 dBi antenna gain
- 1 dB cable loss
= 24 dBm EIRP
```

In this example, the radio transmits at 20 dBm, but the resulting EIRP is 24 dBm after adding the antenna gain and subtracting the cable loss.

This matters because regulatory limits are often based on EIRP, not only on the configured transmit power of the radio. Adding a higher-gain antenna can therefore require the transmit power to be lowered to remain within the allowed limit.

One important detail is that vendors do not always display transmit power in the same way. The value shown in a controller may represent the radio transmit power, or the vendor may already account for antenna gain. Always verify what the displayed value represents before calculating EIRP yourself.

The important point is that configured radio transmit power and EIRP are NOT automatically the same.

---

## Antenna Gain Does Not Create Free Power

In the EIRP formula, antenna gain is added to the transmit power. This can make it look like the antenna creates additional power, but it does NOT.

The easiest way to explain antenna gain is by using a flashlight. The light can be spread across a wide area or focused into a narrow beam. The focused beam appears brighter and reaches farther, but the flashlight itself does not produce more power. A passive antenna does the same thing with RF energy by changing the shape of the radiation pattern.

Antenna gain is expressed in dBi and compared with an isotropic antenna, which is a theoretical antenna that radiates equally in every direction. An antenna with 5 dBi gain provides 5 dB more signal in its strongest direction than an isotropic antenna using the same transmit power. It does NOT provide 5 dB more signal in every direction.

Different antenna types create different radiation patterns:

* An omnidirectional antenna typically creates a doughnut-shaped pattern around the antenna. A higher-gain omnidirectional antenna usually creates a flatter pattern, extending the signal farther horizontally while providing less coverage above and below the antenna.
* A directional antenna focuses the signal towards a specific area while providing less coverage towards the sides and behind the antenna.

This makes antenna type, orientation, and mounting position important parts of the wireless design. The best antenna is not automatically the one with the highest gain, but the one with a radiation pattern that matches the required coverage area.

---

## Receive Sensitivity Also Matters

Transmit power determines how loudly a device speaks. Receive sensitivity determines how quietly the other device can speak before it can no longer be understood.

Receive sensitivity is the weakest signal level that a radio can reliably demodulate and decode. It is normally expressed in dBm. When comparing the same conditions, a receive sensitivity of -90 dBm is better than -75 dBm because the radio can decode a weaker signal.

RSSI shows the signal level that is actually received, while receive sensitivity is the minimum level required to decode it. If the RSSI drops below the required receive sensitivity, the radio can no longer use that data rate reliably.

Receive sensitivity is not one fixed value. Manufacturers specify different values depending on the channel width and MCS. MCS stands for Modulation and Coding Scheme and determines how data is encoded and transmitted. A lower MCS is slower but can be decoded at a weaker signal level. A higher MCS provides more speed but requires a stronger and cleaner signal.

This is also why RSSI does NOT tell the complete story. SNR stands for Signal-to-Noise Ratio and shows the difference between the received signal and the noise floor.

For example:

* An RSSI of -60 dBm with a noise floor of -65 dBm results in only 5 dB SNR.
* An RSSI of -70 dBm with a noise floor of -95 dBm results in 25 dB SNR.

The second signal has a weaker RSSI but provides a much cleaner signal for the receiver to decode.

Both the AP and the client have their own receive sensitivity. One side may be able to decode a weak signal that the other side cannot. A usable Wi-Fi connection therefore depends on both devices being able to receive and decode each other at the data rate required by the application.

---

## How Do You Identify the LCMI?

Identifying the LCMI starts with understanding which client devices the business actually depends on.

A device may have limited Wi-Fi capabilities, but if it is rarely used or not important to the business, it should not automatically influence the entire wireless design. Start by listing the important devices and the workflows that depend on them.

Once the important devices have been identified, we can compare their wireless capabilities:

| Check | Why it matters |
| ----- | -------------- |
| Supported bands | A 2.4 GHz-only device has different design requirements than a client that supports 5 GHz or 6 GHz. |
| Supported channels and channel widths | Some clients do not support every channel in a band, and client behaviour on DFS channels can vary. Supported channel widths can also differ by client and band. |
| Radio capabilities | The supported Wi-Fi generation, MCS values, and number of spatial streams affect performance. |
| Antenna design | Small handheld and embedded devices often have more constrained antenna designs than laptops. |
| Client transmit power | A client with lower transmit power may hear the AP while the AP struggles to hear the client. |
| Roaming behaviour | Some clients roam quickly, while others remain connected to an AP for too long. |
| Application requirements | Voice, scanning, payment, and medical applications may be more sensitive to packet loss, latency, or interruptions than normal web browsing. |

Specifications are a useful starting point, but they do not always show how a device will perform in the actual environment. The best way to identify the LCMI is to test the candidate devices under the same conditions, especially near the expected cell edge.

For each device, compare:

* Client-side RSSI and SNR.
* Client RSSI measured by the AP.
* Tx and Rx data rates or MCS values.
* Retry rate, if available.
* Roaming behaviour.
* Whether the actual application continues to work reliably.

Client-side and AP-side RSSI are measured by different radios, so the values should not be expected to match exactly. The important client that reaches its acceptable performance limit first is the LCMI.

---

## Matching AP Transmit Power to the LCMI

Once the LCMI has been identified, its transmit power can be used as a starting point for selecting the AP transmit power.

For example, imagine that the LCMI is a barcode scanner that transmits at 14 dBm while the AP transmits at 20 dBm.

The difference is:

> 20 dBm - 14 dBm = 6 dB

Assuming the other parts of the link budget remain equal, the client may receive the AP approximately 6 dB stronger than the AP receives the client. Because a difference of 3 dB represents approximately twice the power, 6 dB represents approximately four times the power.

A simple starting point is therefore:

> AP transmit power ≈ LCMI transmit power

In this example, lowering the AP transmit power from 20 dBm to around 14 dBm would create a more balanced starting point for the link.

This does NOT make the client transmit more strongly or improve the uplink by itself. It reduces the area in which the AP appears usable to the client. In a multi-AP network, this can help prevent the downlink cell from extending much farther than the usable uplink and encourage clients to move to a closer AP sooner.

The values should not be copied blindly. Before selecting the AP transmit power, also consider:

* Whether the AP displays radio transmit power or EIRP.
* AP and client antenna characteristics.
* AP and client receive sensitivity.
* The band and channel being used.
* Regulatory and EIRP limits.
* The required RSSI, SNR, and data rates.
* AP placement and density.
* Application and roaming requirements.

The final transmit power should be validated near the intended cell edge. The client must still hear the AP with sufficient RSSI and SNR, the AP must still receive the client reliably, and the actual application must continue to work.

The goal is not to make both sides report identical RSSI values. The goal is to prevent the AP downlink from extending much farther than the client uplink.

---

## So, Is More Transmit Power Better?

Sometimes, but not automatically.

Increasing AP transmit power can improve what the client hears and extend the apparent coverage area. It does NOT strengthen the client's return path or guarantee that the application will work better.

In a single-AP environment, higher transmit power may be useful if the client can still communicate reliably in both directions. In a multi-AP environment, however, higher transmit power also increases the apparent cell size of each AP.

The client can continue to hear an AP from farther away and may remain connected even when another AP would provide a better connection. Because the client ultimately decides when and where to roam, a larger cell can delay that decision.

Larger cells also create more overlap. Some overlap is required for roaming, but too much overlap can make cell boundaries less clear. A client may hear several APs at similar signal levels and may not always select the AP we would prefer.

There can also be an airtime impact. Wi-Fi is a shared medium. When APs and clients using the same channel can hear each other across a larger area, they may need to wait for each other before transmitting. This increases the size of the contention domain and reduces channel reuse.

More transmit power does NOT create more airtime. In some designs, it causes the available airtime to be shared by more devices over a larger area.

This does not mean that full transmit power is always wrong. It may be appropriate for a specific environment, AP placement, or coverage requirement.

The correct transmit power is the level that allows the AP and the LCMI to communicate reliably in both directions while creating the appropriate cell size for the design. That may be full power, but it often is not.

---

## What Comes Next

This post covered the theory behind AP and client transmit power, dBm, EIRP, antenna gain, receive sensitivity, and designing around the LCMI.

In **Part 2**, I will repeat the single-AP lab with different AP transmit power levels. I will compare what the client hears from the AP with what the AP hears from the client to see how changing AP transmit power affects both sides of the link.

In **Part 3**, I will use three APs to look at cell size, overlap, roaming behaviour, and the effect of transmit power in a multi-AP environment.