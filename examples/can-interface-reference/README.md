# CAN Mapper interface reference

> [!WARNING]
> **Untested reference design only.** This circuit and PCB have not been built, bench-tested, or validated in a vehicle. They are provided as an editable KiCad example for experienced users. Do not treat this project as a finished product or a production-ready automotive design.

This example shows one simple way to connect an existing CAN bus and a nominal 12 V supply to the Reforge CAN Mapper plugin. It uses individual components rather than a prebuilt module and matches the plugin's current expansion assignments:

- Reforge connector pin 1 / `EXP_IO1`: CAN receive (`twai.rx`)
- Reforge connector pin 2 / `EXP_IO2`: controller transmit pin held recessive for listen-only operation (`twai.listen.txIdle`)
- Reforge connector pin 3 / `EXP_IO3`: not connected
- Reforge connector pin 4 / `EXP_GND`: shared logic ground

The project targets only the **1st-gen DCC Advanced Rev B 1.0** board. Basic, Advanced Rev A, and 2nd-gen boards are not supported.

## Included files

- `Reforge_CAN_Interface_Reference.zip` - downloadable bundle of the editable project and reference files
- `Reforge_CAN_Interface_Reference.kicad_pro` - KiCad project
- `Reforge_CAN_Interface_Reference.kicad_sch` - complete schematic
- `Reforge_CAN_Interface_Reference.kicad_pcb` - routed two-layer PCB
- `Reforge_CAN_Interface_Reference.kicad_sym` - project symbol library
- `Reforge_Reference.pretty/` - project footprint library
- `BOM.csv` - example bill of materials
- `Reforge_CAN_Interface_Reference.pdf` - schematic preview
- `Reforge_CAN_Interface_Reference.svg` - schematic preview
- `Reforge_CAN_Interface_Reference_PCB.svg` - top-side PCB/copper preview
- `Reforge_CAN_Interface_Reference_PCB.png` - 3D board preview
- `erc.rpt` and `drc.rpt` - current KiCad check reports

Open `Reforge_CAN_Interface_Reference.kicad_pro` in KiCad 10 or later to inspect or modify the design.

## Input connector J1

| Pin | Signal | Connection |
| ---: | --- | --- |
| 1 | `CAN_L` | Existing CAN bus low |
| 2 | `CAN_H` | Existing CAN bus high |
| 3 | `+12V` | Nominal 12 V supply input |
| 4 | `GND` | Supply/CAN reference ground |

Verify the source vehicle's CAN pinout and polarity before connecting it. This example assumes a common ground and is not isolated.

## Reforge connector J2

| Pin | Reforge signal | Circuit connection |
| ---: | --- | --- |
| 1 | `EXP_IO1` / GPIO18 | SN65HVD230 receiver output (`R`) |
| 2 | `EXP_IO2` / GPIO15 | SN65HVD230 driver input (`D`), pulled up to 3.3 V |
| 3 | `EXP_IO3` / GPIO7 | Not connected |
| 4 | `EXP_GND` | Circuit ground |

The four-pin Reforge connector does not supply accessory power. This reference board derives its own 3.3 V rail from the separate 12 V input on J1.

## Termination jumper JP1

JP1 controls the onboard 120 ohm resistor:

- **Jumper open:** no termination is added. Use this when attaching to an already terminated CAN bus at a non-endpoint location.
- **Jumper installed:** R2 places 120 ohms between CAN-H and CAN-L. Use this only when the reference board is located at a bus end that needs termination.

Do not add a third 120 ohm terminator to a normally terminated bus. With vehicle power off, a healthy bus with two 120 ohm end terminators commonly measures about 60 ohms between CAN-H and CAN-L.

## How it works

U1 is a fixed 3.3 V MCP1799 high-voltage-input linear regulator. C1 and C2 provide the regulator input and output capacitance. U2 is a 3.3 V SN65HVD230 CAN transceiver, with C3 placed across its supply.

The SN65HVD230 receiver output feeds `EXP_IO1`. Its driver input is connected to the host-owned `EXP_IO2` signal and pulled high by R1 so it defaults to the recessive state. The current CAN Mapper plugin configures the controller for listen-only operation. This circuit is therefore intended for passive monitoring, but it is not a physical transmit-disable circuit; correct listen-only firmware behavior is still required.

The SN65HVD230 `RS` pin is tied low for high-speed operation. R2 and JP1 provide optional bus termination.

## Important limitations

This intentionally simple reference omits features that may be required for a reliable vehicle installation, including:

- input fuse or resettable fuse
- reverse-polarity protection
- load-dump and surge protection
- CAN-bus TVS protection or common-mode filtering
- galvanic isolation
- connector strain relief, enclosure, conformal coating, and environmental sealing
- thermal validation across vehicle voltage and temperature ranges
- electromagnetic compatibility testing

The MCP1799 can accept the nominal 12 V input used here, but a linear regulator dissipates the input-to-output voltage difference as heat. Confirm the real supply range, current draw, package temperature, capacitor voltage ratings, and fault behavior before building or installing a derivative.

## Verification status

KiCad 10 ERC and DRC currently report zero violations and zero unconnected pads. Those checks verify the design files, not the electrical behavior, physical connector fit, component sourcing, CAN compatibility, or safety of a completed assembly.

Before using a derivative, independently review the [TI SN65HVD230 datasheet](https://www.ti.com/lit/ds/symlink/sn65hvd230.pdf) and [Microchip MCP1799 datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/MCP1799-Data-Sheet-20006248A.pdf), then prototype and test it on a current-limited bench supply and an isolated CAN test setup before considering any vehicle connection.
