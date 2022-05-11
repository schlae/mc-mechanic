# MC Mechanic - POST card for Micro Channel Computers
If you have an IBM PS/2 computer or one of the rare non-IBM Micro Channel computers, you know how hard it can be to debug boot issues. The IBM BIOS designs often do not use the PC speaker as extensively as other BIOSes, and a POST card is pretty much a requirement. However, not many were made back in the day and they are quite rare.

Fortunately, there is now a solution, the MC Mechanic. Not only can you get the standard PS/2 checkpoint codes at address 680h, but you can also spy on other addresses, which can be useful for troubleshooting. An additional bonus feature is the PS/2 info panel display. This lets you to see additional messages on PS/2 systems whose BIOS supports it but do not have the display itself. The PS/2 model 77 is one example, but there are probably others.

![MC Mechanic PCB photo](https://github.com/schlae/mc-mechanic/blob/main/images/MCMechanic.png)

The components are reasonably available. The bill of materials lists Mouser part numbers, but the devices are all pretty common and can be purchased from a variety of overseas sources.

Here are the design files.

[Schematic](https://github.com/schlae/mc-mechanic/blob/main/mcapostcard.pdf)

[Bill of Materials](https://github.com/schlae/mc-mechanic/blob/main/mcapostcard.csv)

The 0.1" header pins are *not* listed on the schematic. They are standard breakaaway headers. You don't even have to install them since they are for advanced debugging with HP logic analyzer pods.

If you do not need the info panel display, then do not populate U10 and U8.

The design uses three GAL devices; two for driving the hex displays and one for decoding the address of the info panel (U8). The BOM specifies ATF16V8 devices because these are easy to get. The speed grade doesn't matter.

[Fab Files](https://github.com/schlae/mc-mechanic/blob/main/fab/mcapostcard-rev2.zip)

## Fabrication Notes
This is a 2-layer board sized for the lowest cost. The board dimensions are 3.925" x 3.475" (100mm x 88mm).

When ordering the board, you **must** specify a card edge bevel of 20 degrees (details of the bevel are included in the Dwgs.User layer). This is **not** optional! The contacts in the Micro Channel socket are easily bent if you attempt to insert a card with no bevel. [Don't believe me? See this page.](https://www.ardent-tool.com/RS6000/RS6000_9-K.html#ECA265)

Ideally you should use selective gold plating (hard gold) for the edge fingers, but this can get expensive for small orders. ENIG will work but the gold will rub off fairly quickly.

For the soldermask color, pick whatever you want!

## Assembly Notes

![J4 jumper photo](https://github.com/schlae/mc-mechanic/blob/main/images/j4.png)

After you solder in the components, be sure to solder two jumper wires in the J4 position with the two wires aligned vertically (see the photo above). This configures the board for using the "normal" version of the hex rotary switches. If you decided to use the "complemented" version, then solder two jumper wires aligned horizontally.

You probably want to install the two (or three) GAL devices in sockets, just in case. The popular Minipro device programmer can be used to flash the ATF16V8 devices.

The command line for that (under Linux) looks like

`minipro -p "ATF16V8B" -w hexlatch.jed -P`

Here are the JEDEC files:

[hexlatch.jed](https://github.com/schlae/mc-mechanic/blob/main/pld/hexlatch.jed) - U6 and U7

[addrlatch.jed](https://github.com/schlae/mc-mechanic/blob/main/pld/addrlatch.jed) - U8

## Using the Card
Be sure to dial in an address using the four hex rotary switches. Unlike ISA bus machines, Micro Channel machines typically use address 0680h, so set SW4=0, SW3=6, SW2=8, and SW1=0. Plug the card in the computer, being careful to make sure it is pointed the right way around (there is an arrow underneath the two electrolytic capacitors pointing to the rear of the case of the computer.) Turn on the computer, and you should see three green LEDs (for the power rails) and a two-digit code that increments as the computer boots up.

For the meaning of the codes, see [this list of checkpoint codes](https://ardent-tool.com/trouble/cp_codes.html).

Messages also show up at the printer port address, 03BC. But feel free to explore and snoop any address you want using the hex rotary switches. As long as it is an IO port address that the computer *writes* to during standard IO transfers (not setup transfers), you will see data.

### Troubleshooting for Engineers
If you have an HP logic analyzer, like a 1670 or 16700 series, you can connect two cables to the card using standard HP 01650-63203 termination adapters. The pin assignments are as follows:

| HP | J2   |    J3   |
|----|------|---------|
| CLK | CMD# | ADL# |
| D0 | CHRESET | A0  |
| D1 | MIO  | A1  |
| D2 | S0# | A2 |
| D3 | CD\_SETUP# | A3 |
| D4 | LED\_SEL# | A4 |
| D5 | SEL1\_CMD# | A5 |
| D6 | n/c | A6 |
| D7 | A0 (latched) | A7 |
| D8 | D0 | A8 |
| D9 | D1 | A9 |
| D10 | D2 | A10 |
| D11 | D3 | A11 |
| D12 | D4 | A12 |
| D13 | D5 | A13 |
| D14 | D6 | A14 |
| D15 | D7 | A15 |

## License

This work is licensed under a Creative Commons Attribution-ShareAlike 4.0
International License. See [https://creativecommons.org/licenses/by-sa/4.0/](https://creativecommons.org/licenses/by-sa/4.0/).






