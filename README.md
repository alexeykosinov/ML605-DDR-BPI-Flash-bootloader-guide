# Enviroment
- Windows 7
- ISE Desgn Suite 14.7
- ML605 Development board

# Instruction
1. Create EDK project using BSB. Select your stuff that must includes: UART, DDR, EMC controllers as well. All settings as you wish nothing special.
2. Build project, export design, open Xilinx SDK
3. Create two project:
   - Application (stored in DDR memory: generate linker script -> in Basic all in DDR)
   - Bootloader (stored in BRAM)
4. Optionally you can reduce size of bootloader by choosing in C/C++ Build Settings -> Optimization -> Optimization Level : Optimize for size (-Os) and comment #define VERBOSE in bootloader.c
5. In blconfig.h set FLASH_IMAGE_BASEADDR with address your EMC controller with offset:
```sh
#define FLASH_IMAGE_BASEADDR  (XPAR_LINEAR_FLASH_S_AXI_MEM0_BASEADDR + 0x01200000)
```
6. Build all
7. Now you have two .[elf] : for example app.elf and bootloader.elf
8. Return to dir of your EDK project open bitgen.ut in etc folder and add at the bottom:
```sh
-g Compress
-bd ..\SDK\SDK_Export\bootloader\Debug\bootloader.elf
```
   when:
   - 1st command reduces the size of generated bit file
   - 2nd command store bootloader in your bitsream

9. Re-run Generated BitStream
10. Finally you have .[bit] file with bootloader in it stored in implementation folder
11. Convert this .[bit] file into .[mcs] using promgen for example:
```sh
promgen -w -p mcs -c FF -o system -s 131072 -u 00000000 system.bit -bpi_dc parallel -data_width 16
```
12. In ML605:
   - Set switch S2[6:1] to 001010 (select's Numonyx JS28F256P30 BPI flash, 32MB)
   - Set switch S1[4:1] to 0XXX
   - Connect JTAG and USB cable to host
13. Power on & using iMPACT or batch program flash with your .[mcs] file
14. After succesfull programming open Xilinx SDK
15. Selct Xilinx Tools -> Program Flash
16. Image file: Debug/Release .[elf] file of your builded application
17. Program at offset 0x01200000 (choosen in 4) and click Program
18. Restart board and see result's in terminal












































