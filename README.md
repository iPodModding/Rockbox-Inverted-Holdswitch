# Rockbox Inverted Holdswitch

- This will make the stock OS boot when the hold switch is unlocked, and Rockbox boot when the switch is locked. Make sure to save your config file in Rockbox, as there’s a good chance that your settings will be reset on boot. I’m working on figuring out how to fix this.

- This was only tested on 2 iPod Classic 6th gens, and may or may not work on any other iPod

-  I did this on Ubuntu 20.04, but there should be ways to do it on any other OS. See https://www.rockbox.org/wiki/DevelopmentGuide for detailed instructions for your specific OS.

- This should be used at your own risk. We take absolutely *no* responsibility for a damaged iPod.

## FAQ
1. **Will this damage my iPod?**
    It has the potential to make your iPod not boot, but there's almost a 100% chance that DFU mode will let iTunes restore it and get it working again. 

2. **How do I uninstall this?**
    As far as I know, the only way to uninstall this is to erase the iPod using iTunes. This *will* remove all music and other files.

3. **Does this work with other iPods?**
Following these instructions won't work for a different iPod, say an iPod Video for example. To get this working on a different iPod, you'll need to dig through the correct bootloader file and find a line similar to the one we change in this tutorial. I'm slowly working on this.

## Instructions

*Note*: The files in this repository are already patched, these instructions are for the unmodified rockbox files

1. **Install the required dependancies**
    In a terminal window, type `sudo apt-get install automake bison build-essential ccache flex libc6-dev libgmp3-dev libmpfr-dev libsdl1.2-dev libtool-bin texinfo zip gawk wget libmpc-dev gettext libusb-1.0-0-dev libarchive-zip-perl`. This may take a while to complete.

2. **Clone the source code**
    Once the installations finish, type `git clone git://git.rockbox.org/rockbox`. This will create a new folder in your home directory called “rockbox”.

3. **Build the cross compiler**
    In the same terminal window, type `cd rockbox/tools`. cd stands for “Change Directory”, so this will change the working directory of the terminal instance is. 
    Type `chmod +x rockboxdev.sh ; sudo ./rockboxdev.sh`. It may prompt you for your computer password, this will not show up as you type it in.
    After a second, you should be presented with several choices for the architecture of the target. Select `a` for ARM. The cross compiler should build, and in my experience takes a while. Go make a coffee. Once this finishes, type `arm-elf-eabi-gcc` to make sure it’s working. The output should be something like this: `arm-elf-eabi-gcc: fatal error: no input files compilation terminated.`

4. **Create Makefile**
Move back to the root rockbox directory by typing `cd ~/rockbox` (If you cloned rockbox in a different location, just `cd` to wherever that is). Type `mkdir build-ipod6g ; cd build-ipod6g`. Now type `../tools/configure`. If everything worked, a list of every device rockbox supports should show up. Type `29` for an iPod Classic, or the corresponding number for whatever device you’re using. After that, you should be asked to select the build type. We only want to build the bootloader, so type `b`.
󠀀󠀀 󠀀󠀀
5. **Modify the bootloader code**
Either in your terminal or in the GUI, open up ~/rockbox/bootloader/ipod6g.c. Line 412 should read `if (button_hold()) {`. Change that to `if (!button_hold()) {` (Adding an exclamation mark). Save the file, and close it.

6. **Build the Bootloader**
In your terminal, `cd ~/rockbox/build-ipod6g` and run `make`. This should only take a few seconds. Once it’s done, type `ls` (Lowercase L), and you should see a file called bootloader-ipod6g.ipod. This is the file we need.

7. **Build the bootloader flash utility**
Type `cd ~/rockbox/rbutil/mks5lboot ; make`. This should produce a file called mks5lboot. Run `cp mks5lboot ~/rockbox/build-ipod6g ; cd ~/rockbox/build-ipod6g`

8. **Verify the bootloader, and write it to the iPod**
Run `crc32 bootloader-ipod6g.ipod`. The output should be `39f920f4` Now unmount (NOT eject) the iPod by running `umount /media/{username}/{nameoftheiPod}`. Now, run `clear ; lsusb` (again, lowercase L). It should show your iPod. Hold Menu + Select on the iPod, and on the computer press the up arrow to bring up the last command, and press enter. Do this a few times without letting go of the iPod buttons, but the terminal should show the iPod listed in DFU mode. It should take roughly 12 seconds of holding the buttons. Once it shows up in DFU mode, run `sudo ./mks5lboot --bl-inst ./bootloader-ipod6g.ipod`. The iPod should beep once, and then beep again a few seconds later.

The rockbox bootloader is now installed! You can install the files needed for rockbox by following the tutorial on rockbox.org. 

If you have any questions, please contact @Cobre#3424 on Discord and I’ll try my best to help you
