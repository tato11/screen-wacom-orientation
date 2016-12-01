What is it?
===========
This is a small script to easily rotate your screen along all the devices that depends on the screen orientatio, like and Stylus or a touch screen. And like with any script, please be careful, remember it is your responsability from here on.


How to configure
================
Copy the sample file from "./screen_wacom_orientation/doc/screen-wacom-orientation.sample" to "./screen_wacom_orientation/bin/screen-wacom-orientation", we are now ready to modify the file. On this file there are 2 important segments, the Device Name Variable Section and the Rotation Screen Section, I will show you how to easily edit both to make it work.

The first step is to find and set your devices that depends on your screen orientation like an Stylus or your Touch screen. To do this we need to open the script file that we just copy from the sample "screen-wacom-orientation" and search for these lines:

  # Use xinput command to find out your device names and replace the names here
  device_stylus="Stylus device name here"
  device_eraser="Eraser device name here"
  device_touch="Touch device name here"


These variables will contain your device names that needs to be rotated along your screen. To get these device names just type "xinput" on the console and search for your device names, for example, I had a Lenovo Thinkpad Yoga 14 at the moment so when I type "xinput" I got this:

[my_user@PC ~]$ xinput
⎡ Virtual core pointer                          id=2    [master pointer  (3)]
⎜   ↳ Virtual core XTEST pointer                id=4    [slave  pointer  (2)]
⎜   ↳ Logitech K520                             id=10   [slave  pointer  (2)]
⎜   ↳ Logitech M310/M310t                       id=11   [slave  pointer  (2)]
⎜   ↳ Wacom Co.,Ltd. Pen and multitouch sensor Finger touch     id=12   [slave  pointer  (2)]
⎜   ↳ Wacom Co.,Ltd. Pen and multitouch sensor Pen stylus       id=13   [slave  pointer  (2)]
⎜   ↳ AlpsPS/2 ALPS DualPoint TouchPad          id=16   [slave  pointer  (2)]
⎜   ↳ AlpsPS/2 ALPS DualPoint Stick             id=17   [slave  pointer  (2)]
⎜   ↳ Wacom Co.,Ltd. Pen and multitouch sensor Pen eraser       id=19   [slave  pointer  (2)]
⎣ Virtual core keyboard                         id=3    [master keyboard (2)]
    ↳ Virtual core XTEST keyboard               id=5    [slave  keyboard (3)]
    ↳ Power Button                              id=6    [slave  keyboard (3)]
    ↳ Video Bus                                 id=7    [slave  keyboard (3)]
    ↳ Video Bus                                 id=8    [slave  keyboard (3)]
    ↳ Sleep Button                              id=9    [slave  keyboard (3)]
    ↳ Integrated Camera                         id=14   [slave  keyboard (3)]
    ↳ AT Translated Set 2 keyboard              id=15   [slave  keyboard (3)]
    ↳ ThinkPad Extra Buttons                    id=18   [slave  keyboard (3)]

From here we can see that there are 3 devices that need to be rotated:

  Wacom Co.,Ltd. Pen and multitouch sensor Finger touch
  Wacom Co.,Ltd. Pen and multitouch sensor Pen stylus
  Wacom Co.,Ltd. Pen and multitouch sensor Pen eraser

So we just need to replace the variable values with these device names, like this:

  # Use xinput command to find out your device names and replace the names here
  device_stylus="Wacom Co.,Ltd. Pen and multitouch sensor Pen stylus"
  device_eraser="Wacom Co.,Ltd. Pen and multitouch sensor Pen eraser"
  device_touch="Wacom Co.,Ltd. Pen and multitouch sensor Finger touch"

In case you don't have some of these devices the you will need to remove those non existing devices from both the Device Name Variable Section and the Rotation Screen Section. For example, lets say you don't have an Stylus nor an Erarser but do have a Touch Screen, then your Device Name Variable Section will look something like this:

  # Use xinput command to find out your device names and replace the names here
  device_touch="Wacom Co.,Ltd. Pen and multitouch sensor Finger touch"

Note that we removed both "device_stylus" and "device_eraser" since we don't have those on this example, and we should do the same for the Rotation Screen segment, this section is located at the end of the script, and it will look like this after we remove those devices:

  # Rotate screen and input devices to the selected orientation
  case "$orientation" in
    0|4)
    # rotate to normal
    xrandr -o normal
    xsetwacom set "$device_touch" rotate none
    echo "Rotate to normal orientation... Done"
    ;;
    1|5)
    # rotate to the left
    xrandr -o left
    xsetwacom set "$device_touch" rotate ccw
    echo "Rotate to left orientation... Done"
    ;;
    2|-2)
    # rotate to inverted
    xrandr -o inverted
    xsetwacom set "$device_touch" rotate half
    echo "Rotate to inverted orientation... Done"
    ;;
    3|-1)
    # rotate to the right
    xrandr -o right
    xsetwacom set "$device_touch" rotate cw
    echo "Rotate to right orientation... Done"
    ;;

Notice that we have removed any line that contained "device_stylus" or "device_eraser".

Once you have finished configuring it, add it execute rights and try it on the command line:

  To display the help use:                ./screen-wacom-orientation -h
  To rotate your screen to the left use:  ./screen-wacom-orientation
  To rotate your screen to the right use: ./screen-wacom-orientation -ccw
  To invert your screen orientation use:  ./screen-wacom-orientation -upside

If everything worked as spected then you are ready add the global keyboard shortcuts you like. For example, I use these on my laptop, just remember to add the full path to your script on the command:

  Shortcut                     | Command                          | Action
  =============================================================================================
  Meta + Shift + Right Arrow   | screen-wacom-orientation         | Rotate to counterclockwise
  Meta + Shift + Left Arrow    | screen-wacom-orientation -ccw    | Rotate to clockwise
  Meta + Shift + Up Arrow      | screen-wacom-orientation -upside | Invert current orientation

If you prefer to have it as a global command like I do, then you can do it by copy the file "profile.d/screen_wacom_orientation.sh" to "/etc/profile.d/screen_wacom_orientation.sh", and copy the script file we just modify to "/opt/screen_wacom_orientation/bin/screen-wacom-orientation", you will need admin rights to do that, but once done you will be able to use your script as a command. The file we copy to profile.d will add "/opt/screen_wacom_orientation/bin/" directory to the global command lookup allowing you to execute it anywhere, like for example to rotate to the right:

  [my_user@PC ~]$ ./screen-wacom-orientation -ccw
