# 16-V Input 3-A Output Point-of-Load with TPS62135

## Overview

![Photo of the Fully Automated 17-V PoL](doc/pcb.png)

This design is based around the Texas Instruments TPS62135 converter.  
[IC Datasheet](https://www.ti.com/lit/ds/symlink/tps62135.pdf).
For layout, see the .kicad_pcb file in the repository.

![Schematic of the board](doc/schematic.svg)

## Use

Connect the input side to the power source, and the output side to the load. The converter powers on automatically above 3V input. Output is programmed to 5.0 volt, can be changed by replacing R2 and/or R3. Reference voltage is 0.7 V. Supported input voltage is around 16 V max., above which the D1 TVS diode will start to clamp. You can remove this to increase maximum input voltage to 18V abs max, but then you should avoid hot-plugging the board.

You can shut down the converter while input power is present: Pull EN input low. The EN pin is pulled up on-board to VIN with 10kOhm, therefore the circuit you use to discharge EN must withstand the maximum VIN you plan to apply.

Alternatively, you can disconnect the input.

To achieve 20-ish uA standby current, remove the output indicator LED D2 or its series resistor R6.

This DC/DC can run in 100% duty cycle mode, when the input voltage is lower or equals to the set output voltage.

## Properties

|                          Specifications |                                                                    |
|----------------------------------------:|--------------------------------------------------------------------|
|                             Dimensions: | 25x42x3mm (without pin headers)                                    |
|                                   Mass: | 3.3g                                                               |
|                          Mounting Hole: | M3 at (-6;-4)mm from bottom right corner                           |
|                          Input Voltage: | 3V - 16V                                                           |
|                         Output Voltage: | 5.0V (default)<br>Optionally between 0.8V and 12V                  |
|             Internal Reference Voltage: | 0.7V                                                               |
|                          Output Ripple: | 45mVpp with no load<br>30mVpp in Continuous Conduction Mode        |
|                         Output Current: | 4A peak<br>3.4A continuous                                         |
|                     Maximum Duty Cylce: | 100%                                                               |
|                            Power Stage: | Synchronous buck with nMOS switches                                |
|             Overtemperature Protection: | Yes, at 160 degrees Celsius, with 20 dC hysteresis                 |
|               Short Circuit Protection: | Yes, at 6A                                                         |
|              Fixed Switching Frequency: | No. (default) In Continuous Conduction Mode fSW is approx. 2.5 MHz <br> Yes (pseudo-static around 2.5 MHz) by pulling MODE pin high (VIN tolerant) |
|                             Soft Start: | 25ms, programmed by C3                                             |
| Transient Input Overvoltage Protection: | Yes, with on-board TVS diode                                       |
|          Output Overvoltage Protection: | No.                                                                |
|                       Hot-Plug Capable: | Yes.                                                               |
|                          Soft Shutdown: | Yes (by pulling EN pin low)                                        |
|                    Output ON Indicator: | Yes. (pink LED)                                                    |
|                      Power Good Output: | Yes.                                                               |
|                                   RoHS: | Yes, plus tantalum-free.                                           |

## Test report

I spent half a year validating this IC design before it was released to the market, and it is very robust. The only way to kill it is through overvoltage on its input or its output (or maybe using the wrong inductor and saturating it, but I have not tried that; it still works down to 0.7 uH).

Current limit is robust, and it also limits negative current when run in FPWM mode (MODE=high, see JP1) to around 1A. You can use it as a rudimentary bidirectional buck-boost if you so please!

You can even use it as an inverting buck-boost converter! (connect GND to the pin labeled VOUT, VIN is VIN, GND is -Vout) Mind that in this setup Vin+abs(Vout) still can't be higher than 17V!
