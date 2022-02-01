# Ultimaker Cura Print Settings for Stock Ender3 v2  
  
Not long ago I purchased a new Creality Ender3v2 3d printer on sale for about $150.00 USD.  It took quite a bit of trial and error to start getting reasonable prints on the stock unit so here are some notes from my experiments.  
  
## Tightening the Frame  
  
One of the first things to check is whether the frame is snugged up together.  Specifically, the X-axis arm which can droop easily if the ends aren't tightened up.  You'll know if you got the tension right if (gentle) upward lift on the end opposite the stepper motor produces very little movement before meeting strong resistance.  Remember that tolerances in 3d printing are measured in millimeters so small variances can produce large effects.  
  
## Bed Leveling  
  
People seem to make this out to be more difficult than it really is.  You don't need any fancy tools, and this is actually one of the few calibrations that works if the leveling is "good enough".  

!!! tip "Bed Quality"
    Out of the box, many 3D printers have beds that are not the best quality.  While Creality has done a lot to improve the glass bed that ships with the Ender3v2, I still ended up with a warped bed.  In my case, the center of the bed bows upwards ever-so-slightly, which means I tend to position all my prints on a Y-axis offset to avoid this raised area as much as possible.  If you have a similar issue you may need to employ a similar strategy.

1. Turn ON your 3D printer and set it to auto-home
1. Set the printer to preheat ~10 degrees hotter than your normal printing temperature (e.g. if you are printing PLA at 180c, preheat to 190c)
1. Use the menus to disable the stepper motor
1. Gently position the extruder nozzle above the center point of one of the bed's tension knobs
1. Raise the bed using the knob and just visually watch until there is about a 1/8" gap between the nozzle and the bed
1. Repeat for each corner of the bed, moving in a clockwise (or counter-clockwise) motion
1. Grab a piece of paper
1. Once again, position the extruder nozzle above the center point of one of the bed's tension knobs
1. Insert the paper between the nozzle and the bed
1. Gently adjust the bed position upwards until you feel the faintest rubbing from the nozzle
1. Repeat the previous two steps for each corner, but this time move diagonally opposite (so if you start at front-left, move to back-right, then back-left to front-right)
1. While doing this you may start to move the print head and notice the nozzle is hitting the bed -- when this happens STOP and lower the bed a little bit, using the knobs that the print head is positioned between (e.g. if the print head hits the back of the board, between the back-left and back-right knobs, lower the bed using both of those knobs)
1. Repeat the paper test in caddy-corner fashion until you are feeling just a little rubbing in each corner
1. When all seems good, run the print head over the middle of the board front-to-back and side-to-side.  If you collide with the bed somewhere in the middle of the board use all four knobs to slowly lower the board an equal amount all around
  
After 2-3 passes you should be "good enough".  Given enough time you will probably be able to visually do bed leveling.  Like I said above it doesn't need to be perfect -- feeling a little resistance, or positioning the bed a hair lower than the resistance point will be good enough.  
  
Calibrate once every couple prints, especially if you apply pressure to the bed when trying to unstick finished prints from it.  
  
## Choosing the Right Software  
  
I use Ultimaker Cura for all of my prints.  It doesn't have a profile for the Ender3v2 though we can borrow many of the defaults from the original Ender3 profile:  
  
1. Open Cura
1. Open Preferences > Configure Cura...
1. Open Printers
1. Choose "+ Add"
1. Choose Add a non-networked printer
1. Expand Creality3D, Select Creality Ender-3, and change the name to Creality Ender-3 V2
1. Match the "Machine Settings" to the tables below:  
  
	| Printer Settings    | Value       |
	| ------------------: | :---------- |
	| X (Width)           | 220.0 mm    |
	| Y (Depth)           | 220.0 mm    |
	| Z (Height)          | 250.0 mm    |
	| Build plate shape   | Rectangular |
	| Origin at center    | OFF         |
	| Heated bed          | ON          |
	| Heated build volume | OFF         |
	| G-code flavor       | Marlin      |
  
	| Printhead Settings              | Value       |
	| ------------------------------: | :---------- |
	| X min                           | -26 mm      |
	| Y min                           | -32 mm      |
	| X max                           | 32 mm       |
        | Y max                           | 34 mm       |
        | Gantry Height                   | 25.0 mm     |
        | Number of extruders             | 1           |
        | Apply Extruder Offsets to GCode | ON          |
  
1. Press CLOSE to save the changes  
  
## Print Settings  
  
Once the printer is set up we'll want to dial in the print settings.  Some of this will depend on the filament you use, so here we'll be assuming PLA:  
  
!!! warning
    These are simply the settings that work best for me, so they may or may not need tweaking if you decide to use them.  Print quality can be affected by anything from ambient temperature to humidity levels.  Generally speaking though, I've found these tweaks tend to produce more reliable prints than the stock settings.
  
1. Printing Temperature: 195.0c
1. Printing Temperature Initial Layer: 200.0c
1. Build Plate Temperature: 65c
1. Print Speed: 40-45mm/s
1. Build Plate Adhesion Type: Raft for small prints, Brim for large prints  
  
## Clean-Up and Maintenance  
  
Like any other physical maker-material clean-up is just as important as the print itself.  I recommend the following routines:  
  
### After Every Print  
  
1. Clear any debris from the bed, wipe down with a cloth
1. Examine the extruder nozzle for any signs of filament leakage, clean up any you might see with something heat-insulated (I have a very nice pair of needlepiont Tweezerman tweezers that do a great job of this)
  
### After Every Third or Fourth Print  
  
1. Clean the bed with Isopropyl Alcohol
1. Auto-Home the nozzle and visually inspect the bed leveling
  
Any other issues will be clearly noticeable during printing.  Oozing can be common, which may require replacement nozzles or just a cleaning of the heater block which can be done by removing the rubber sock covering it.  
  
