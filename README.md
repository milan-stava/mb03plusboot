# MB03+ BOOT

This repository is a fork of the original MB03+ BOOT project.



# DIFFERENCIES IN THIS FORK AS OPPOSED TO OFFICIAL BRANCH

* dual SD initialization

* page 98 is not erased upon press of key "1"- this enables partition info in VDT table to be preserved for Easy CF 1.1





## Why page 98 is preserved on key `1`

When BOOT is started with key `1`, SRAM pages are reloaded from FLASH before EasyCF is launched.  
Page 98 contains the installed EasyCF driver and its VDT table. Rewriting this page would destroy the existing VDT contents before EasyCF 1.1 can preserve the user-defined partition names.

For this reason, the copy to SRAM page 98 is skipped. EasyCF 1.1 reads the existing VDT first and then installs the new driver into page 98 normally.





The `sd2-init` branch contains an **unofficial modification** which extends the BOOT **E** and **F** functions to initialize both SD card slots.

## What was changed

When BOOT function **E** or **F** is used, the BOOT now attempts to initialize:

1. SD2
2. SD1

If a card is not present in a slot, initialization times out and BOOT continues normally.

The modification is therefore independent of EasySD or any other software using the SD cards.

## Why SD2 is initialized first

During testing on real MB03+ hardware, the initialization order:

```

SD1 -> SD2

```

proved unreliable at higher CPU clock speeds. SD2 initialization started to fail at approximately 14 MHz and above.

Changing the order to:

```

SD2 -> SD1

```

solved the problem.

This order was successfully tested at up to **20 MHz**.

The physical SD cards were also swapped between SD1 and SD2 slots and the result remained the same, indicating that the behavior depends on the slot/init sequence rather than on a particular SD card.

For this reason, the SD2 -> SD1 initialization order is intentional and should not be changed without further hardware testing.

## SD initialization retries

Each SD slot uses the existing SD initialization routine with **20 retries**. This value was previously increased from 10 to improve cold-start reliability at high CPU speeds.

No SD card is required for BOOT to continue.

With both SD slots empty, E/F initialization takes approximately one second on the tested hardware.

## Build

The BOOT can be assembled using the included SjASMPlus:

```

sjasmplus.exe boot.a80 --lst=boot.lst --lstlab

```

A successful build currently reports:

```

Errors: 0, warnings: 0

```

and produces, among other files:

```

boot.sna

boot.tap

```

## Relevant commits

```

861a3dc  Add SD initialization and BSDOS ULAplus fallback

885baff  Increase SD init retries for cold start

38ff1ce  Initialize both SD slots on BOOT E and F

```

## Status

Tested on real MB03+ hardware with:

* SD1 only
* SD2 only
* both SD cards inserted
* both SD slots empty
* physical SD cards swapped between slots
* CPU speeds up to 20 MHz

This is an unofficial modification of the original MB03+ BOOT.



Use "_run-boot.bat" to compile the project. You receive "boot.tap", where You find the "boot.bin" which You then place and use in Reflasher.

