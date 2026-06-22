---

title: "Transmit Power Part 1: APs, Clients, EIRP and Why More Power Is Not Always Better"
date: 2026-06-22
draft: false
author: "Nick Cuypers"
description: "A practical explanation of Wi-Fi transmit power, AP and client power, EIRP, antenna gain, receive sensitivity and the loud AP problem."
tags: ["Wi-Fi", "RF", "CWNA", "CWDP", "CWNE", "Transmit Power", "EIRP"]
categories: ["Wireless Engineering"]
---

Transmit power is one of those wireless settings that looks simple at first.

More power should mean better Wi-Fi, right?

Not always.

In Wi-Fi, increasing the transmit power of an access point can improve what the client hears from the AP. But that does not automatically improve what the AP hears from the client. Wi-Fi is bidirectional. Both sides of the conversation matter.

This post looks at AP transmit power, client transmit power, EIRP, antenna gain, receive sensitivity, and why a "loud AP" can sometimes create more problems than it solves.

This is part 1 of a small transmit power series:

* **Part 1:** APs, clients, EIRP, and the loud AP problem
* **Part 2:** Single-AP lab test
* **Part 3:** Three-AP cell overlap and roaming test

This first post focuses on the theory and design concepts. The lab results will follow in the next parts.

---

## The Basic Problem

When users complain about wireless coverage, it is tempting to increase AP transmit power.

The logic seems reasonable:

> The client has weak signal, so make the AP louder.

Sometimes that helps. But it only helps in one direction.

When we increase AP transmit power, we are changing how loudly the AP transmits. That may improve the RSSI seen by the client. The client may show more signal bars. The operating system may report a stronger connection. The user may appear to have better coverage.

But the client did not become louder.

The AP still needs to hear the client's transmissions. If the client has lower transmit power, a small antenna, poor radio design, or is located behind attenuation, the uplink can still be weak.

This is the classic loud AP problem.

---

## Wi-Fi Is Bidirectional

Wi-Fi is not a one-way technology where the AP simply talks and the clients listen.

A working Wi-Fi connection requires communication in both directions:

* The client must hear the AP.
* The AP must hear the client.

This applies to management frames, control frames, and data frames. Association, authentication, roaming, acknowledgements, retries, rate selection, and application traffic all depend on two-way communication.

If only the downlink is strong, the client may believe it has good coverage while the AP struggles to receive frames from that client.

That can result in:

* Higher retries
* Lower data rates
* Poor roaming behavior
* Unstable voice or video calls
* Slow application performance
* Clients staying associated too long
* Inconsistent user experience

A strong signal indicator on the client does not always mean the link is healthy in both directions.

---

## AP Transmit Power vs Client Transmit Power

An access point and a client device usually do not transmit at the same power level.

Enterprise APs may support relatively high transmit power, depending on the band, channel, regulatory domain, antenna type, and platform. Client devices are often more limited.

A laptop may perform well. A modern phone may perform well. But a small handheld scanner, voice handset, payment terminal, medical device, or IoT sensor may have a smaller antenna and lower transmit capability.

This is one reason wireless design cannot be based only on the best client in the environment.

A design that works for a high-end laptop may not work well for a business-critical handheld device.

---

## The Loud AP Problem

The loud AP problem happens when the AP transmits at a much higher effective power than the client.

The client hears the AP clearly, but the AP does not hear the client as well.

From the client side, things may look fine:

* RSSI looks acceptable.
* The SSID is visible.
* The client can associate.
* The client may show good signal bars.

From the AP side, the situation may be different:

* AP-reported client RSSI may be weak.
* Retries may increase.
* The client may use lower data rates.
* Roaming may become less predictable.
* Uplink-sensitive applications may suffer.

This is why "more power" is not always better. It can create an unbalanced cell where the downlink looks better than the uplink.

---

## Understanding dBm

Transmit power and received signal strength are commonly expressed in dBm.

dBm is an absolute power level referenced to 1 milliwatt.

A few useful reference points:

|  Power | Meaning       |
| -----: | ------------- |
|  0 dBm | 1 mW          |
| 10 dBm | 10 mW         |
| 20 dBm | 100 mW        |
| 30 dBm | 1000 mW / 1 W |

A 3 dB increase is approximately double the power.
A 3 dB decrease is approximately half the power.
A 10 dB increase is ten times the power.

However, RF design is not only about raw transmit power. Antennas, cable loss, path loss, wall attenuation, noise floor, receive sensitivity, and client behavior all matter.

---

## What Is EIRP?

EIRP stands for Equivalent Isotropically Radiated Power.

In simple terms, EIRP is the effective radiated power after considering transmitter output power, antenna gain, and cable loss.

A simplified formula is:

```text
EIRP = Transmit Power + Antenna Gain - Cable Loss
```

For example:

```text
20 dBm transmit power
+ 5 dBi antenna gain
- 1 dB cable loss
= 24 dBm EIRP
```

This matters because regulatory limits are usually based on EIRP, not only the configured transmit power of the radio.

If an AP uses external antennas, antenna gain and cable loss must be included. With internal antenna APs, vendors often abstract some of this away, but the concept still matters.

The important point is this:

> The configured transmit power is not always the same thing as the effective radiated power.

---

## Antenna Gain Does Not Create Free Power

Antenna gain can be misunderstood.

An antenna does not magically create extra RF energy. Instead, antenna gain changes how energy is focused in space.

A higher-gain antenna focuses energy more in some directions and less in others. This can be useful, but it also changes the coverage pattern.

For example:

* An omnidirectional antenna spreads energy around a wider area.
* A directional antenna focuses energy in a specific direction.
* A high-gain antenna may reduce coverage in some areas while improving it in others.

This is one reason antenna selection matters. It is not only about making the signal "stronger". It is about putting RF energy where it is needed.

---

## Receive Sensitivity Also Matters

Transmit power is only one side of the link budget.

Receive sensitivity describes how weak a signal can be while still being successfully received and decoded by a radio.

Different data rates and modulation types require different signal quality. Higher data rates generally require better signal-to-noise ratio.

This means a client may still "hear" the AP, but not well enough to use higher rates reliably.

The same applies in the other direction. The AP may hear the client, but if the signal is weak or noisy, the AP may receive frames at lower rates or with more retries.

Good Wi-Fi design is about the complete RF link, not only the AP transmit power setting.

---

## Matching AP Power to Client Capabilities

When planning AP transmit power, we should think about the clients that actually matter in the environment.

A useful way to approach this is the LCMI principle.

LCMI stands for **Least Capable, Most Important** device.

The idea is to design not only for the best client in the environment, but for the least capable client that is still important to the business.

A modern laptop may perform well, but a barcode scanner, voice handset, payment terminal, IoT device, older client, or medical device may struggle in the same location.

When planning transmit power, the question should not only be:

> Can my best client hear the AP?

The better question is:

> Can my least capable, most important client hear the AP, and can the AP hear that client back?

The correct transmit power depends on many factors, including:

* The LCMI device
* Client transmit power
* Client antenna design
* Supported bands and channels
* Required applications
* AP density
* Channel plan
* Wall attenuation
* Roaming requirements
* Regulatory domain
* Antenna type
* Minimum data rates

This is especially important in environments such as warehouses, healthcare, retail, manufacturing, and logistics, where the most business-critical device may not be the most capable Wi-Fi client.

---

## Why Full Power Can Hurt Wi-Fi Design

Running every AP at full power can create several problems.

### Larger Cells

Higher transmit power increases the apparent cell size. Clients may stay connected to an AP from farther away, even when a closer AP would provide a better connection.

This can hurt roaming behavior.

### Sticky Clients

Some clients are reluctant to roam. If the AP is very loud, the client may keep hearing it well enough to stay associated, even as the uplink becomes poor.

The client may not roam when we want it to.

### More Co-Channel Contention

Wi-Fi is a shared medium.

If AP cells are too large, more devices may hear each other on the same channel. This can increase contention and reduce airtime efficiency.

More transmit power does not create more airtime. It may actually make airtime sharing worse.

### Unbalanced Links

The AP may have a strong downlink to the client, but the client may have a weak uplink back to the AP.

This can lead to retries, lower rates, and poor application performance.

---

## What Comes Next

This post covered the theory behind Wi-Fi transmit power, EIRP, AP/client power differences, and the loud AP problem.

In **Part 2**, I will test this in the lab with a single AP. The goal is to see whether increasing AP transmit power improves both sides of the link, or only what the client hears from the AP.

In **Part 3**, I will use three lab APs to look at cell overlap, roaming behavior, and why too much transmit power can become a design problem in multi-AP environments.
