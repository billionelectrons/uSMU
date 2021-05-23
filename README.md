
<p align="center">
  <img src="https://github.com/joeltroughton/uSMU/raw/main/Hardware/Version%2010/uSMU_v10.png" width="650" title="μSMU">
</p>

# μSMU

μSMU is a small [source-measure unit](https://en.wikipedia.org/wiki/Source_measure_unit) designed for the very low-cost electrical characterisation of photovoltaic cells.

## Background

SMUs are "4-quadrant" devices, meaning they can both source **and** sink current at both positive **and** negative voltages. This makes them very useful for semiconductor device characterisation - including LEDs, transistors and solar cells.

In photovoltaic research laboratories, a SMU is typically used to vary the voltage applied to an illuminated solar cell, whilst simultaneously measuring the current. This voltage sweep allows us to plot the solar cell's I-V characteristics, and calculate its light-to-power conversion efficiency.

SMUs are generalised pieces of test equipment, designed to be highly sensitive over vast current & voltage ranges. For example, the workhorse [Keithley 2400](https://uk.tek.com/keithley-source-measure-units/keithley-smu-2400-series-sourcemeter) has a voltage range between 100 nV and 200 V, and a current range between 1 pA to 10 A. This is likely overkill for most research and education applications concerning solar cells, which tend to operate between 0-5 V and μA to mA. The μSMU doesn't intend to replace precision SMUs, rather to supplement them in cost-sensitive areas where such precision is not required.

The μSMU is a USB-powered SMU with a +/- 5 V voltage range and +/- 50 mA source/sink capability. The PCB is only 70mm x 43mm



## Function
The μSMU is largely inspired by [Linear Technology's DC2591A evaluation board](https://www.analog.com/media/en/dsp-documentation/evaluation-kit-manuals/855-DC2591A_REV01_DEMO_MANUAL.PDF), which demonstrates an I2C address translator IC to interface up to 8 modules containing several I2C devices with an Arduino-style board. Somewhat consequentially, these boards also contain fantastic SMU circuits! 

The voltage applied to the device-under-test (DUT) is supplied by a LT1970 opamp driven by a 16-bit DAC on the non-inverting input and a 2.048V reference on the inverting input. The current flowing through the DUT is measured by amplifying the voltage drop through a high-side 50 Ohm shunt resistor. Both the DUT voltage and shunt resistor voltage drop are measured using a 16-bit ADC. The whole system is controlled using a STM32F072 microcontroller, which presents a USB virtual communications port for interfacing.



## Capabilities 

| Parameter                  |               |
| -------------------------- | ------------- |
| Voltage range              | -5 to +5 V    |
| Voltage measure resolution | ~0.6 mV       |
| Minimum voltage step size  | <1 mV        |
| Current limit              | -50 to +50 mA |
| Current resolution         | ~10 nA     |

## Changelog
### Version 10 (release 1.0)
- Voltage DAC changed from a 12-bit Microchip MCP4725 to a 16-bit TI DAC8571
    - Can now achieve sub-mV voltage steps
- Current sense amplifier changed from an Analog LT1991 to a TI PGA281
    - PGA281's gain can be programmed using 5 GPIOs ranging from 0.125 to 176. This allows low currents to be gained more than high currents, improving current resolution.
- Current shunt resistor increased from 10 to 50 Ohms.
    - The programmable current sense amplifier (PGA) means we can use a high value shunt resistor and decrease the gain when measuring high currents.
    - Voltage drop across a 10 Ohm shunt is sensed by the power amp (U10) to impart programmable current limiting. Measuring this across the main 50 Ohm shunt would limit the current output to ~10 mA due to limitations in the LT1970
    - The increase in current shunt value along with the new PGA means we can now sense currents on the order of ~10 nA
- Buffer amplifier changed from a quad Maxim MAX44252 to 4x Gainsill GS8331
    - Less expensive and lower V<sub>OS</sub>
- Electrostatic discharge protection added to USB port (U5)
- Isolated DC-DC converter replaced with bipolar switching regulator TI TPS65131 supplying ±9.7V
    - Extra headroom for power amplifier to push higher currents through 50 Ohm current shunt
- 4.5V LDO added for DAC and ADC
- USB-C port replaced with lower cost 2.0-pinned version
