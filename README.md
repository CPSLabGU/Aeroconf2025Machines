# The `BatteryMonitor` LLFSM

This repository contains the example machines discussed within the paper titled
"Efficient Runtime Verification of Energy Properties within Hardware / Software Co-Design" presented at
Aeroconf 2025.

The files contained within this repository define the `BatteryMonitor` LLFSM and the accompanying formal
verification using Computation Tree Logic (CTL). The files of interest are contained within the structure below.

```shell
├── BatteryMonitor.machine
│   ├── data.dat
│   ├── model.json
│   ├── output.json
│   └── spec.tctl
```

- The `data.dat` file contains the binary-encoded Kripke structure transferred from the FPGA.
- The `model.json` file contains the machine definition which is consumed by our [code generator](https://github.com/CPSLabGU/LLFSMGenerate) to generate the respective VHDL files.
- The `output.json` file contains the parsed Kripke structure after it has been decoded from the binary representation within `data.dat`. This format is defined within [this repository](https://github.com/CPSLabGU/VHDLKripkeStructures).
- The `spec.tctl` file contains the CTL formulae for defining the requirements formally verified using our [model checker](https://github.com/CPSLabGU/VHDLModelChecker). The format of this file is defined within our [CTL parser](https://github.com/CPSLabGU/TCTLParser).

## Installing the LLFSM Tools

If you would like to generate the VHDL source files or perform the formal verification yourself, you may do so from the command-line tools we provide. Please install the [llfsmgenerate](https://github.com/CPSLabGU/LLFSMGenerate/releases/tag/2.1.4) and [llfsm-verify](https://github.com/CPSLabGU/VHDLModelChecker/releases/tag/0.1.0) binaries onto your system. You will need to compile these programs from source using the [Swift compiler](https://www.swift.org).

## Generating the VHDL source files

To generate the VHDL source files for this machine, you first need to invoke some preliminary steps using the `llfsmgenerate` binary.

```shell
llfsmgenerate model BatteryMonitor.machine
```

You then have a choice of generating the VHDL for Kripke structure generation, or just the standard VHDL sources for the machine.

### Kripke Structure Program

```shell
llfsmgenerate vhdl --include-kripke-structure BatteryMonitor.machine
```

### Standalone LLFSM

```shell
llfsmgenerate vhdl BatteryMonitor.machine
```

Your generated `.vhd` files will be located within `BatteryMonitor.machine/build/vhdl`.

## Performing the Formal Verification

Performing the formal verification is as simple as invoking the `llfsm-verify` binary.

```shell
llfsm-verify --machine BatteryMonitor.machine BatteryMonitor.machine/spec.tctl
```

You will then see the output from the model checker satisfying the requirements.

```
Verifying Requirement (1/3):
    A G battery = "000" -> {A F mode = "00"}_{t <= 2 us}
Finished Requirement 1 in 0.029007014 seconds.


Verifying Requirement (2/3):
    A G mode = "00" -> A mode = "00" W battery /= "000"
Finished Requirement 2 in 0.028276522 seconds.


Verifying Requirement (3/3):
    A G mode = "00" -> E mode = "00" U battery /= "000"
Finished Requirement 3 in 0.044123716 seconds.


Verification completed in 0.106220544 seconds (± 1e-09 seconds).
```

You may modify `spec.tctl` to include additional requirements of your choosing.
