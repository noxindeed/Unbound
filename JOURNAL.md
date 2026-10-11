---
title: "Unbound"
github: "https://github.com/noxindeed/Unbound"
description: "this is a tiny [:3] nRF52840 devboard designed specifically to fix the headaches of custom keyboard builds. Unbound ditches the onboard USB-C port, has dedicated driver modules to offload RGB and Power management. Fitting all of this, and plenty of GPIOs in a tiny footprint; aiming to get a near utopian keyboard building experience"
created_at: "2026-10-09"
total_time: "5h 42m"
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

# October 11, 2026: started the schematic, completed power block!!
<!-- fabricate:entry 343 -->

spent today drawing out the core power architecture 
since unbound needs to operate smoothly between battery and external source, with the constraint of keeping a tight footprint so i researched a bit (for about 20 minutes, yt video not included) and decided to break the block into three parts, a LTC4054 Charger, PMOS mosfet and a schottky

most of the time went into cross ref-ing datasheets and keeping pinouts in check (also component availability </3)

first i did the battery charger,
![image](https://cdn.hackclub.com/01a12b3e-7879-7896-afc3-7f55f7fea8e8/paste-1791726874552.png) so since this is my first ever devboard i looked around at possible solutions and the most optimum was using the `LTC4054Es5-4.2` IC (in its SOT-23-5 package), the reason why i chose this is because it keeps the peripheral count to the minimum compared to other bulkier and linear options

- the 10kOhm resistor on Pin 5 to ground is for current limiting, according to the datasheet formula [I(chg) = 1000V/R(prog)], hence this provides a steady 100mA charge rate which is safe for a small capacity battery which is commonly used in custom keebs
- there's a 4.7uF decap on both pin 3 and 4 for stability and i marked pin 1 as no charge since i dont plan on adding a status LED as of now, to save space ofc

secondly, 
![image](https://cdn.hackclub.com/01a12b9f-5502-764b-859a-f95433b2324c/paste-1791733222582.png)the auto power path switching,
to ensure smooth transitions between usb and battery, i've wired an AO3401A MOSFET as the main power gate
this is my first time working with a MOSFET so i read documentation, not for too long tho, it was mostly understanding the gate logic part that took time
- pulled the Gate (pin1) to GND usimg a 100kOhm PDR and tied the line directly to VBUS
- when USB is unplugged (i.e VBUS is 0) the 100kOhm PDR pulls the gate low relative to the Source which connects VBAT directly to VDDH


finally, 
![image](https://cdn.hackclub.com/01a12ba1-f7e9-7de0-a5ef-23005543a735/paste-1791733397192.png)the schottky diode, so this is the part where i was most comfortable in lol since i know how these work beforehand 
- i chose the B5819W in a SOD-123 package instead of a standard diode because it has a low forward voltage drop which means minimal power loss and minimal heat generation

didnt run drc yet and next up is the bare package and its decoupling network

another conflict im having is that PCBA will eat up all of my budget and there is no way in god's sweet earth that i can hand solder this, i dont even have a hot plate

**Total time spent: 1h 42m**
