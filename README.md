# EasyDIY ESC

Note: 'main' branch is under development, there is a change to use KiCad as compared to v0.5.2 (current HEAD of feature/v0_5_2, i.e. git SHA1 - e55e314)

The target for this EV motor controller is to power small EVs like EBikes or EScooters like Xiaomi M365, and offer better customizability and repairability.

## Main objectives:

* **Easy to build and repair DIY**: It uses DC-DC modules, a development board instead of an uC chip, SMD passive components are all 0805 or bigger, and ICs are SOIC8 or SOIC14. No fine pitch components.

* **Popular components:** Uses the popular Bluepill STM32F103 board, meaning it is easy to develop firmware for it. Uses popular components / footprints / pinouts. Meaning you should have no problems with part availability, and if you do, you will be able to change most parts with equivalents depending on availability or prefrence, without need to change the pcb layout.

* **Possible to add custom electronics:** For reusing this motor controller on different devices, you can extend the electrocnis with a custom header on top of the Bluepill. This means you can use all available pins and access and extend with: CAN, UARTs, etc.

* **Possibility to buy the SMD parts pre-soldered:** While we went to great extend do make this board easy to DIY by humans (as opposed to robots in a factory), we acknowledge there are many who lack the skill/time/motivation to solder all SMDs themselves. So all SMDs are on the same side, in large stock at lcsc (at the time of writing), so the board can be ordered with SMDs pre-assembled by JLCPCB for a decent price.

* **Compactness:** Despite using only big DIY friendly parts, we tried to keep the board as small as possible: ~ 73mm x 49mm

* **Good Power/Size ratio:** Our initial estimation (untested) is that the upper limit for phase current should be ~78A. As for max continuous “battery amps”, it heavily depends on the cooling!  

## ES Thread:
https://endless-sphere.com/forums/viewtopic.php?f=30&t=114537



## Building Tips:

### **Bus Bars**
Bus bars are recommended for anything else than very low current. (extra copper wires soldered to the exposed copper strips of the PCB, or at very least a lot of solder on the exposed strips)

### **Bulk electrolytic capacitors (XC.... )**
We went for a total of ~1000uF, which should be enough for most usecases. 
You need more capacitance the more power you plan to use, and the longer (and thinner) your battery wires are. So you can probably also get away with less if you have short thick battery wires.  
Choose voltage rating ~ 1.5x your max battery voltage for a long lifetime. For 48v nominal (54.6V max) battery voltage, 80V or more is recommended. 
The total capacitance of the electrolytic capacitors is more important than the exact combination of values. 1 big + 3 small is almost the same as 3 medium of the same total value. More smaller capacitors have the advantage of lower ESR, lower inductance, and redundancy in case one fails)

### **Bulk ceramic/MLC capacitors:  (XC.... )**
As much as possible. These need to compensate for the high frequency weakness of the electrolytic capacitors. Overrating voltage rating is recommended. (author chose 100V for the “default” for 3.3v, 5v and 12v). Definitely fill all open footprints. 

### **Capacitors - the rest**
Values are consolidated for easy buying. Voltage rating is 25V or higher regardless if they are on the 12v, 5v, or 3v3 rail on the default design, however you can choose a smaller voltage rating for the 5v and 3v3 rails..  
All parts are 0805 mlc (2 electrolytic exceptions, C8 and C10 from the 12v and 5v rails)

### **Snubbers (RSx, CSx)**
These are RC snubbers. **The current values for the R and C are random, they are here only for the footprints. Do not fit them (yet).** The real values (if necessary at all) will be computed after we build the controller and see what ringing we get. Purpose of the snubbers is to dampen that ringing. (if you choose different parts than the one we compute the snubbers for, you might need to compute the snubbers yourself). These components are not present in the pick-and-place or BOM files.

### **Optional Capacitors (OCx)**
All parts marked OCx are optional GS Capacitors for the mosfets. If you insist on using old/bad mosfets with high Crss, you'll need to compensate for that with additional Cgs. That being said, please avoid high Crss mosfets, and you don't need to use the OCx. We expect none of the recommended FETs to need these. These are footprints only to offer flexibility if needed. These components are not present in the pick-and-place or BOM files. 

### **FETs**
The default FETs are available from aliexpress at the time of writing. They are cheap and have good specs. (low Rds on, low Crss, 100v, etc). 
You can solder them “standing” on the the throughhole/module side, or bent flat on the opposite (smd) side. In either case, it is recommended that the thick part of the leads is in contact with (soldered to) the board. The thin part of the leads can carry significantly less current.

You can use a fet of your choosing, but please be aware that old Fets (such as the famous 4110 or 3077) have huge Crss, leading to a tiny Ciss/Crss ratio. This ratio needs to be bigger than roughly your max battery voltage, so you will need to compute and add the optional Cgs capacitors (OCx). Small Ciss/Crss results in ringing / parastic turn on (which leads to failure). 
That being said, it’s best to just use Fets with Ciss/Crss > ~100, nowadays they’re easy to find. See the spreadsheet with alternative parts. 

Attaching the FETs to a heatsink: the FETs need to be electrically isolated from the heatsink, but well connected thermally. The options for this are:
* Mica glass (“traditional option”, cheap, easy to find)
* Ceramics (saw this as a new option, never tried it)
* Polymer pads 
* Kapton Tape 

Unfortunately in focusing on making the board small we crammed the FETs very close together. This means some mica or ceramic pads will not fit - and it’s very hard to cut them afaik. So for this version we’re probably stuck with polymer / kapton. 

### **Testing**

* v0.5 build has been tested by mxlemming with MESC FOC firmware on F401CC black pill board

### **Known issues/limitations:**
* Mosfet temp sensor missing - you can add it to the expansion board. 
* Many connectors are not present (example: CAN)  - you can add them to the expansion board. 


## FAQ

* **What’s with those 2 solder jumpers?**
They are power for your uart-connected board. If it’s a 5v board, solder the 5v solder jumper, if it needs 3.3v solder the 3v3 jumper. Never solder both! 

* **Can you assemble it for me?**
You can get the SMD side pre-assembled at jlcpcb (at the time of design all parts were in stock for jlcpcb SMT service). So you’ll only need to solder the through-hole parts and the modules. Currently there is no plan on selling any assembled PCBs.

* **I want more power!!!**
This might not go well. So good luck, you’ll probably need it. Please don't blame us when it goes up in smoke. But do let us know how it goes. Disclamer aside, here are some basic suggestions:
   * Stack another 1mohm shunt on top of the other. This will double the max current you can read. 
   * Add thick bus bars to the board.
   * Add more/ bigger bulk caps!
   * Use appropriate wires (thick!) 

* **I want more voltage!!!!**
Realistic limit is 15s lipo. For more than that the ESC will need serious redesign.
Some bottlenecks that cross my mind: 
   * default 12v dc module is 72v limited, 
   * some of the recommended mosfets are not capable of more voltage, 
   * voltage sense divider needs adjustment, etc. 
   * PS: I would have loved to make it 20s capable, but did not find any small 100-12v DC-DC module. If you know of one, please let us know in our ES thread, and next version might be 20s / 74v capable. Also, I added 3 through hole pads for easy mounting of any 12V module via wires :)

* **Your part X is bad, I recommend Y!**
We tried to find cheap but decent parts for this controller. Cheap almost always implies a compromise in some other areas, so you might not like our pick :)
It should be fairly easy to switch to other parts on your own: we used mostly standard and popular footprints and packages
If you know better parts or have better solutions, please let us know on our ES thread. We like learning new things. 
....except if you’re going to suggest 3077 or 4110 mosfets. Those things are dinosaurs. They were the biggest baddest thing around during their time, but nowadays belong to a museum. Or a kid’s t-shirt. Please stop recommending them. See our thread for details.

* **Where can I buy parts?**
Lcsc.com, mouser.com. Aliexpress if needed. 
I designed this with jlcpcb smt assembly in mind, so all SMDs were in stock at lcsc when I designed the PCB. Also most of the Through-hole parts. 
Some exceptions, and where to buy them:
   * Default mosfets - aliexpress. 
   * 12v module - mouser
   * 5v module - aliexpress. There is a fixed voltage one, we recommend that one! 
   * Bluepill - aliexpress. Don’t know a better source these days. 
   * Jst headers - aliexpress
   * Bluepill headers - aliexpress

* **Why do the battery wires go in seemingly random places in the middle of the board?**
If you have the battery wires at the edge, then the full battery current will need to go through a portion of the battery “traces” of the PCB. I’ve placed them near the middle switch node, so with a tiny exception, the current through the traces is ⅓ of the battery current. So you can actually get away without bus bars for low power. (For higher power - 20A from battery or more - I’d still recommend soldering extra copper wires to the exposed bus traces)

* **This whole thing is amateur work!**
Yeah, totally true. We are amateurs working on a generic low power ESC for free. If by this you refer to any specific mistake / set of mistakes, we would love to know about it! Post in the ES thread. Thanks!


## Change log v0.5 -> v0.5.2

### Schematic
* Changes:
  * changed voltage sense resistors to 10x lower value (basic jlcpcb resistors -> cheaper)
  * removed pin 20 from GND (for blackpill compatibility)
  * removed thermal reliefs
  * replaced some parts with more cost-effective equivalents (basic parts jlcpcb)
  * changed diodes to a bigger more rugged version (more inline with the philosophy of this controller)
  * changed voltage sense divider so max read voltage is 100V (20s compatibility)
  * added circuitry for a ntc temp sensor for mosfets.
  * changed the electrolytic caps from the 12V and 5V rails to MLCC (still more capacitance than manufacturer recommends)
  * swapped ntc pins: MOSFET Temp -> A0, and Motor Temp -> A1 (for compatibility with EBiCS v2)
  * changed pinout of HALL JST connector - now you can use, additionally to a 6 pin connector for motors with temp NTC sensor, a 5pin connector on pins 1-5 and it will directly be compatible with the cable of a motor without temp sensor (M365 would be a popular example)
### PCB Design 
  * added 3 through hole pads for vbat, 12v, and gnd for easy connection with wires of any dc-dc module for more flexibility
  * moved JST connectors away from bluepill for better fitting (increased board width by 1mm)
  * many minor layout changes 

## Change log v0.1 -> v0.5

### Schematic
* Fixes:
  * added voltage divider for the voltage follower op-amp
  * added connection between the 3 2k2 resistors and 3V3 on hall sensor inputs
  * changed decoupling caps of the 3 power stages from VBAT-Shunt to VBAT-GND 

* Changes
  * replaced "default" mosfets with much better model
  * replaced shunts with 1mOhm (much lower value)
  * connected bluepill pin 20 to GND
  * changed the RC from the phase wire of the power stages to snubbers
  * replaced opamp with better model
  * changed Opamp 2 to be the voltage follower for easier routing
  * replaced vattery connectors with 2 pads for wire soldering
  * removed one of the connectors for hall sensors, replaced the other one with JST PH. 
  * pinout changes for leaving CAN pins free
  * replaced mini 360 with MP1584EN (is bigger, but there is a non-adjustable 5V version, which is less error-prone to use.)
  * changed all ceramic decoupling capacitors for 3v3 and 5v to 10uF 0805
  * changed all bootstrap capacitors to  10uF 0805
  * added 1 ceramic decoupling capacitor per power stage (for a total of 2) 
  * increased gain of opamps to 10 (from 8)
  * consolidated pullup R values to 2k2
  * consolidated many other C and R values
  * removed diode and cap from the hall sensor 5v rail
  * removed extension board headers (will be mounted on top of the bluepill on the same pins)
  * replaced electrolytic caps with bigger values and bigger voltage ratings
	
	
### PCB design:

* Fixes: 
  * Moved the decoupling capacitor close to the bus bars (GND was through a long thin trace which rendered it almost useless) 	

* Changes:
  * moved to 4 layer board (Ground Planes rule!)
  * moved the opamp non-inverting input resistors near the shunt - so they get the potential of that local ground (not of the GND near the opamp) 
  * stacked DC-DC modules
  * removed expansion board headers
  * added 3 JST PH headers for basic functions
  * moved vbat rail at the edge of the board and GND rail in the middle
  * major redesign of everything
  * greatly reduced the size


