This PCB was designed on EasyEda by Andrii @arozhkov2001.

On this folder are the exported project from EasyEda as also the exported files for production on JLCPCB.

Known issues:
- R31 and R32 should be connected to 3.3V but there is a mistake on the connections of the schematic. On PCB, a wire to 3.3V must be connected to R31.
- LMV324: missing voltage divider to make the 3.3V/2 voltage. (see M365 schematic)

Suggestions
- add analog input for analog signal of mosfets or motor temperature sensor. 3 pins: (5V or 3.3V configured with small soldered shunt; GND; signal, that will a voltage divisor with for instance a resistor of 1K8 and 3k3 to GND)

David M: It will be ok but if you're making your own hardware, that's a slightly crappy opamp
Low gain bandwidth
1mhz is pretty poor. It sounds like a lot but your running a gain of about 8 which reduces it to 125khz
For low side shunts you need to be able to sample in the off period
At 20khz, 80% modulation, centred sampling, you get only 2.5us to stabilise
So ideally you'd have 400khz+ bandwidth
It'll work but at high speeds your current data will start to seriously degrade
My preferred opamp for this is NCS20034
https://www.digikey.co.uk/en/products/detail/onsemi/NCS20034DR2G/5267860
0.75euro
