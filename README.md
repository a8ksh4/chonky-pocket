# chonky-pocket
An almost pocket-sized portable computer!  I wanted a pocket-sized computer... This is not quite it.  It fits in the cargo pocket of one pair of my shorts, but I'm not going to walk around with it like that.  This is better sized for a handbag.  It has a run-all-day sized battery and an ethernet port, as a propper computer should!  

A couple of goals for this project
* Implement a chording keyboard in software using the raspberry pi gpio pins https://github.com/a8ksh4/gpio-keyboard
* Get the battery to show up like a laptop battery https://github.com/a8ksh4/rpi-integrated-battery-module
* Learn new techniques in onshape - used a surface to build the angled keyboard from, and extruding multiple parts from the same sketches.

And in retrospect, a few things I want to include in the next build:
* A display that can turn off when idle and save power
* External gpio, i2c, etc pins from the pi for interacting with project devices.
* Pi SD card more accessible.
* Still need to make a smaller build - this probably means using a Pi Zero 2W or trying to strip all the ports off of a Pi 3 or 4 to slim it down, and getting more creative with battery layout to compress things. TBD!

# Case Design and Stl files
All of the current STLs are at https://github.com/a8ksh4/chonky-pocket/tree/main/stl_files.
And you can get the cad designs in OnShape (they have free accounts for non-commercial use) here: https://cad.onshape.com/documents/f3ba133b606f4645057c2510/w/c53c420633cef7709b7d72dc/e/867bf425f6e139860c26cd65?renderMode=0&uiState=64308961ce06995598eb5201

I try to model stuff like this to minimize use of supports and make sure that all of that visible surfaces are either on the build plate (bottom) or top of the print.  And assembly needs to be considered in the model, too.  Like I can't add features that will block me from sliding in the display.  So there are pop-in panels to go around the raspberry pi ports and the volume and brightness buttons for the display:

<img src="images/usb_panel.png" width="200" /><img src="images/volume_panel.png" width="200" />

The top and bottom of the case sort of friction snap together with a few screws where I can fit them to keep it secure:

<img src="images/top_case.png" width="400" /><img src="images/bottom_case.png" width="400" />

# Keyboard and Input
This build uses a few input methods:
* Mechanical keyboard with chording and mouse emulation.
* Rotary encoder to emulate a mouse wheel (at least)
* Touch screen

It's hard to fit a full-sized keyboard, or even a 40% keyboard into a small build, but one-handed chording keyboards fill the gap where we want a do-anything mechanical keyboard but don't have the space.  This one is based on the [artsey.io](https://www.artsey.io) layout, with a couple extra keys to more derectly access some programming related symbols. 

<img src="images/keyboard_artsey_layout.png" width="400" />


## Keyboard Wiring
The keyboard and encoder are wired directly to the pi gpio pins.  One wire, the copper zig-zag, goes to ground, and the rest go to digital pins. 

<img src="images/keyboard_wiring.jpg" width="400" />

### Encoder
The Encoder has a button built in, sw1 ans sw2, and a rotary encoder with com (common), A, and B.  Com and one of the sw# pins can be connected to ground.  The other sw# pin, A, and B, go to their own gpio pins.  There are encoder libraries that can tell you rotation direction, or you can look at the sequence of pin activation to tell rotation direction.  Each rotaton of the rotary wheel, first one pin will activate, then the other pin, and then they'll both go back to inactive.  The order of activaton indicates direction. Here's the encoder pinout:
<img src="images/encoder_pinout.jpg" width="400" />

It turns out that if you clip off the nubs and stuff from the bottom of these encoders, you can stuff the pins through a perfboard and use a bit of super-glue to secure them, and then wire one into your project without any custom pcb!

# Power System
By far, the easiest way to power a cyberdeck is with a rechargable usb battery that the deck just plugs into.  There are a few good ones that will power a Pi 4 without under-voltage warnings, and a Pi Zero, 2, or 3 can be run from a wider variety of usb bricks.  Most usb bricks don't support pass-through charging though, so you need to turn the deck off to charge the battery, and the battery could just be more integrated...

Another option is to use a li-ion cell, or multiple cells, to make a battery, and connect it to a charge controller like the Amp Ripper or Retro PSU to handle charging and voltage boost to the 5v needed to run the deck.  This build uses an Amp Ripper 4000 which has more than ehough power for a Pi 4 and conveniently can report battery charge level and charging current over I2C, so we can report the battery level to the OS for a laptop-like feel for power mgmt.

## Software
I adapted an existing kernel module and wrote a simple service to query the battery and report level of charge to the OS. This is documented here:
https://github.com/a8ksh4/rpi-integrated-battery-module

## PSU
## Battery
The pack is 1s6p, so one cell in series and 6 parallel, with fuses on the posetive terminals of each cell to protect against shorts.  Photos are from initial test-fit to finished test-fit.  Voltage sums in series, and amp hours sum in parallel, so this is a 3.7v pack with about 6*3ah -> 18 amp hour. It lasts all day.
<img src="images/battery0.jpg" width="400" /
<img src="images/battery1.jpg" width="400" />
<img src="images/battery2.jpg" width="400" />
<img src="images/battery3.jpg" width="400" />
<img src="images/battery4.jpg" width="400" />
# Chassis Design
# Materials List

Misc Stuff
* Raspberry Pi 4: https://rpilocator.com/
* 5 inch hdmi touchscreen with speaker: https://www.amazon.com/gp/product/B08343QX67
* Filament - "Design White" and "Galaxy Black": https://www.printedsolid.com/collections/jessie/ - Note that the galaxy black might be dialectric because of mica content, blocking wifi, so I wouldn't print an entire case out of it. 
* 1/4" Screws: https://www.amazon.com/gp/product/B00GDYNHL6/
* 3/8" Screws: https://www.amazon.com/gp/product/B00GDYNJNM/

Input Stuff:
* Choc Switches: https://www.littlekeyboards.com/collections/keyboard-switches/products/kailh-choc-pro-low-profile-switches?variant=32328459681859
* Keycaps: 
* EVQWGD001 Rotary Encoder: Buy on aliexpress
* Jumper wire for keyboard: https://www.amazon.com/gp/product/B006C4A1WU/
* Breadboard for GPIO connections
* Header for GPIO Connections

Power System
* Amp Ripper 4000 PSU: https://www.kickstarter.com/projects/ksd/ampripper-4000-next-gen-battery-charger-and-boost-module
* 6 x 18650 Li-Ion cells
* Nickel strip: https://www.amazon.com/gp/product/B07PQP55CM
* Cell level 18650 Fusing: https://batteryhookup.com/collections/accessories/products/nickel-fuse-2p-wide-continuous-roll-by-the-foot-18650-cell-level-fusing
* 18650 Cell holders: https://batteryhookup.com/collections/accessories/products/18650-cell-holders
* Power Wire: https://www.amazon.com/gp/product/B073RDBW7L

# Outtakes
These builds always take a few print iterations to sort out how things fit together - between errors in measurements, changes to the printer that affect sizes of stuff, and oversights.  

<img src="images/print_iterations.jpg" width="400" />

Accidentally wired a few gpio pins that weren't available and had to re-route the wires around.

<img src="images/oops0.jpg" width="400" />

And I busted a surface mount button for the LCD brighness and had to replace it.  Of coures I lifted the pad wehn I removed the old button... A little solder bridge to the big ground plane fixed it.

<img src="images/oops1.jpg" width="400" />
<img src="images/oops2.jpg" width="400" />
