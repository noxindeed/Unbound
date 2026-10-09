---
title: "Unbound"
github: "https://github.com/noxindeed/Unbound"
description: "this is a tiny [:3] nRF52840 devboard designed specifically to fix the headaches of custom keyboard builds. Unbound ditches the onboard USB-C port, has dedicated driver modules to offload RGB and Power management. Fitting all of this, and plenty of GPIOs in a tiny footprint; aiming to get a near utopian keyboard building experience"
created_at: "2026-10-09"
total_time: "4h"
---

# October 10, 2026: researched for scope and parts (finalised most stuff!!)
<!-- fabricate:entry 231 -->

NOTE: Since i initially started this for a different program, and only made a project here few minutes ago, the init timeline might not matchup (i decided to switch programs mid way)

First of all, i thought about the scope of the project, i made an idea dump in plan.md (will keep this updated but very rough, planning on deleting it by the end of the project) 
a bit more cleaned-up version of the project scope:
- tiny footprint (& no on-board usb-c)
- BLE support
- strong memory and processing power for RGBs
- battery management 
- shit ton of GPIOs

i used the primitive method for looking for parts, read reddit threads, google searches and also looked at a devboards made for the same chip by others.
i feel slightly under-confident working with antennas and RF packages so -
initially i thought of using [Raytac MDBT50Q-1MV2](https://www.raytac.com/product/ins.php?index_id=24) but that would clash with my size constraints, and felt like a workaround, so i decided to go with [NRF52840-QIAA](https://jlcpcb.com/partdetail/NordicSemicon-NRF52840_QIAAR/C190794), so i read through the sheets a tad bit on the recommended ways to manage the RF antenna and prevent it from messing up because of ground planes and tracks 
![image](https://cdn.hackclub.com/01a121e5-ddeb-7fa3-95b2-4663b14dab49/paste-1791570073242.png)
so referencing and the other ref circuits in the [doc](https://docs.nordicsemi.com/r/bundle/ps_nrf52840/page/ref_circuitry.html) i finalised this barebone qfn, with a 4-layer pcb, following the 4-component LC matching network: 
- 4.7 nH inductor (L1)
- 0.8 pF cap (C1)
- 3.9 nH inductor (L2)
- 1.0 pF cap (C2) (and more)
![image](https://cdn.hackclub.com/01a121ea-3844-7390-a099-2c3affd6f2cd/paste-1791570359572.png)
tho this means ill have to work with a tighter placement against the ANT pin ig i'll figure it out (using a 3216 ceramic chip antenna)
the other thing i researched and finalised was the power architecture logic :
- For the RGB level shifting, since the LEDs you tend to use operate on 3.5V logic at 5V rail would need to add a single gate buffer 
- a cap at 100mH for Li-Po charging

also checked the availability of parts on JLCPCB, specifically for those i researched about, onto the preliminary schematic next (?)

**Total time spent: 4h**
