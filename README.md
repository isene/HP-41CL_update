# HP-41CL_update
A Linux tool to update ROMS on the HP-41CL calculator via HP-IL.

## DEPENDENCIES
You need the following to run this update solution:
- A PC with Linux, MAC OS/X or BSD system
- An HP-41CL calculator (http://www.systemyde.com/hp41/index.html)
- A PILbox (http://www.jeffcalc.hp41.eu/hpil/#pilbox)
- Lifutils (https://github.com/bug400/lifutils)
- The pyILPer program (https://github.com/bug400/pyilper)
- The "41CL Extreme Functions" module aka "YFNX" (http://www.systemyde.com/hp41/software.html)
- The "41CL Update Functions -3B" module (http://www.systemyde.com/hp41/software.html)
- The HEPAX module - for running the "Initialization process" (see below) or for reading HEPAX compressed ROMs
- The AMC_OS/X module if you import modules via the HEPAX READROM function (optional)

## DESCRIPTION
There is one program on the PC side, "HP-41CL_update.rb" and two FOCAL programs on the HP-41CL side, "FUPDATE" and "HFUPDAT". HP-41CL_update.rb uses Lifutils to format and put ROMs and an index file into the LIF file. FUPDATE takes uncompressed 16-bit roms from the LIF image file and loads them onto your HP-41CL. The HFUPDAT takes HEPAX compressed roms and loads them onto your HP-41CL.

HP-41CL_update.rb takes HP-41 ROM files from a folder named "roms" and adds those to a LIF file ("cl_update.lif") that can be mounted by pyILPer. The pyILPer is a Java program that can mount LIF files so that an HP-41 can access that file via a PILbox. The "roms" folder must reside in the same folder as the HP-41CL_update.rb program unless you specify another folder via the "-r" (or "--romdir") option. The file names of the ROMs to be updated must be prefixed with the HP-41CL target address so that the FOCAL program knows where in flash to update the ROMs (only the first three hex numbers are added at the beginning of the file name).

 Example: You want to update the ISENE.ROM on your HP-41CL. The ISENE.ROM should go to the location "0C9", therefore you rename the rom to 0C9ISENE.ROM and drop it into the folder called "roms". You run HP-41CL_update.rb and you get a LIF image called cl_update.lif in the same directory as the HP-41CL_update.rb program. Mount this LIF file in pyILPer and run the FUPDATE program on the HP-41CL. See example rom directory, index.txt and cl_update.lif. There is also a file, "z.txt" that that only contains the size of the index.txt file (in number of HP-41 XM registers).

Included is also a ROM (CLILUP.ROM) that includes two routines from Håkan Thörngren; RDROM16 (to read 16 bits roms from Mass Storage, i.e. LIF files) and WRROM16 (to write 16 bit roms to Mass Storage). These functions are needed to read and write "modern" HP-41CL roms such as the IMDB. Old roms only used 10 bits and HEPAX compressed these roms by stripping the other 6 bits. You can also read and write old 10-bit roms to and from Mass Storage LIF files using HEPAX's READROM and WRTROM).

Running the HP-41CL_update.rb will...

![Alt text](docs/2017-11-30-150349_956x304_scrot.png?raw=true "Top Dir")

...take all the ROMs in the "roms" directory, and add them to the LIF image, "cl_update.lif", including the "index.txt" file which contains the needed info used by the HP-41 FOCAL program, "FUPDATE.41" to actually update the CL flash. You can also see the file, "z.txt" (containing the size of the index file). The "index.txt" looks like this:

![Alt text](docs/2017-11-30-150423_956x231_scrot.png?raw=true "roms Dir")

## GETTING YOU UP AND RUNNING IN THE FIRST PLACE
Now what do you do if you have no serial cable to your HP-41CL and only a PILbox connection? How do you even get the CLILUP module loaded onto your calculator? Fear not, there is a simple initialization procedure. Just do these simple steps:

1. Clone this Git repository: 'git clone https://github.com/isene/hp-41cl_update.git'
2. Hook up your HP-41CL to your PC via the PILbox
3. Make sure the HEPAX module is loaded onto a page in your HP-41CL
4. Run the pyILPer program. Click the "Drive1" tab, select the file "cl_init.lif" in the cloned directory and check "Device enabled" on the bottom left.
5. Key in (or upload if you know how) and run the program "CLILINI" on your HP-41CL (this program loads the two needed modules, "CLILUP" and "UPDAT-3B" into CL RAM - pages 840 and 841)
6. You can now use the setup to update any roms you want by prefixing them with the address where you want them and dropping them into the "roms" directory, run './HP-41CL_update.rb' on your PC and 'FUPDATE' on your calculator.

## FUPDATE and HFUPDAT
The FOCAL programs FUPDATE and HFUPDAT loads roms from the LIF image, "cl_update.lif" (mounted via the PILbox) and loads them onto your HP-41CL.

The FUPDATE program loads uncompressed 16-bit roms (the roms put into the LIF image file when you run HP-41CL_update.rb without any options). FUPDATE will first copy the flash sector to be updated into HP-41CL RAM pages #830 - #837 and then read roms and write them directly to the specified RAM page. 

The HFUPDAT program loads HEPAX compressed 10-bit roms (the roms put into the LIF image file when you run HP-41CL_update.rb with the "-x" (or "--hepax") option. HFUPDAT will first format HP-41CL RAM pages #830 - #837 with the HEPAX template (from flash page #0B9) and plug the ones needed into page 15 of your HP-41CL and then use the HEPAX function READROM to get the rom from the LIF image file. To skip the initial copying of the HEPAX template (if those pages are already HEPAX pages from before), you can set flag 02 before XEQ "HFUPDAT".

## USAGE

### SYNOPSIS
HP-41CL_update.rb [-hvrx] [--help, --version, --romdir, --hepax]

### OPTIONS
-x, --hepax  Add the ROM(s) into the LIF image as a HEPAX SDATA file. Must be read into the HP-41CL using HFUPDAT function. To skip the formatting

-r, --romdir  Specify the "roms" directory/folder for the rom files. Default is the "roms" folder where the HP-41CL_update.rb resides.

-h, --help	Show help text.

-v, --version  Show the version of HP-41CL_update.rb

## THANKS
Thanks and great appreciation goes to Håkan Thörngren for the MCODE routines and for assembling the CLILUP.ROM and to Joachim Siebold for adding features to his LIFUTILS making it possible to add 16-bit roms to LIF image files.

## COPYRIGHT:
Copyright 2017, Geir Isene (www.isene.com).  This program is released under the GNU General Public lisence v2.  For the full lisence text see: http://www.gnu.org/copyleft/gpl.html.

