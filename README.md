# How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07

## IF THIS PROCESS IS DONE INCORRECTLY IT WILL BRICK YOUR PRINTER! BEFORE YOU ATTEMPT THIS THE OUTCOME IS TOTALLY ON YOU! I HIGHLY RECCOMEND YOU MAKE A WORKING BACKUP OF YOUR PRINTER &/OR A CLONE OF YOUR EMMC!
## YOU HAVE BEEN WARNED! 

To update anything on the Sovol Klipper screen you absoutly MUST freeze or lock the kernal to stop it being updated when you run any upadates or installers.

First you have to log into your Sovol Klipper Screen ussing SSH. Use `Terminal` on a Mac or `Putty` on a PC.

once logged in paste in:
```
sudo armbian-config
```
It will bring up this blue background menu:



![armbian-config 1](https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/5efbc383-2470-496f-9916-b085c5ea5c59)

- Press enter to confirm choices & the keyboard arrow keys to naviagte. 
- Choose the first option `System and Security`
- Press enter

Now it will load a red background menu BE CAREFUL HERE YOU CAN BREAK THINGS!!! Thats why its red!

![armbian-config 2](https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/d2fec019-74d1-4878-8814-f28c887d23fa)

- Press enter on the top line to change it so the screen reads like the image above.
- YOUR KERNAL IS NOW FROZEN!
- DONT PRESS ON ANYHTING ELSE
- Move down to `OK` to confirm the choice & go back the blue menu.
- Move over to `EXIT` on the blue menu.

I find its good to do it this way as you can see its done, just dont be tempted to click other stuff!


Now you "should" be able to run the following commands WITHOUT Bricking your printer! Keep 'em crossed!

## BE SURE YOU WANT TO DO THIS! THIS IS THE POINT OF NO RETURN!
This will update all your system components to the latest available versions for this image.

Here you need your rabbits foot & to keep your fingers crossed as the new updated system attempts to start! 

I HOPE YOU MADE A BACKUP OR CLONE!!!!
```
sudo apt-get update
```
```
sudo apt-get upgrade
```

After it completes enter
```
sudo reboot
```


IF it fails to start you'll need to look at unbricking & reinstalling your image.
If it works like mine did GREAT well done, the tricky bit is over!

## UPDATING KLIPPER, MAINSAIL & MOONRAKER

Open your web broswer & head over to your printer. Then on Mainsail open the `Machine` tab & find your `moonraker.conf` file & paste in at the bottom

moonraker.conf
```
[update_manager] 
channel: dev 
refresh_interval: 168 
enable_system_updates: False 

[update_manager mainsail]
type: web
channel: stable
repo: mainsail-crew/mainsail
path: ~/mainsail

[update_manager mainsail-config]
type: git_repo
primary_branch: master
path: ~/mainsail-config
origin: https://github.com/mainsail-crew/mainsail-config.git
managed_services: klipper
```
Now `RESTART` klipper & go back to the `Machine` tab, you should new have access to the `UPDATE MANAGER`
- Click the circle arrow button in the top of that section the get the latest update info.
- click update on your components
- NOTE THIS CAN TAKE A LONG TIME - 10-20 minutes in some cases! Wait for it to complete

YOUR version of klipper will be partially updated & you'll probabaly get a big red warning saying `PROTCOL ERROR!`
& saying you need to update your MCU's!

Dont worry we're doing that now!

## UPDATING THE HOST MCU RPI & MCU FIRMWARE

Now tricky bit is done & your system is updated & working you need to update the rest of the printer.
First up get back in to SSH & enter the below commands to update the MCU RPI to the same firmware version as the new Klipper.

```
cd ~/klipper/
make menuconfig
```
In the menu, set "Microcontroller Architecture" to "Linux process," then save and exit.
```
sudo service klipper stop
make flash
sudo service klipper start
```
To be safe
```
sudo reboot
```

This will probably remove the big red error you had before but we still need to update the printer's mainboard. LAST STEP!
Back into SSH again

```
cd ~/klipper/
make menuconfig
```
but this time change the options to these...
```
STM32F103 with a "28KiB bootloader" and serial (on USART1 PA10/PA9)
communication.
```
Save & exit

now simple type 
```
make
```
This will now build your new MCU firmware

Now use a `FTP CLIENT` to log onto the printer & pull the `Klipper.bin` file from the `klipper/out` folder & download it to your computer.

REMANE THE FILE to something random, think cat walking across your keyboard random.


SV07 & SV07+ 
- Copy the file to a FULL SIZE SD CARD!!! Or at least a micro SD in a full size adapeter!
- SHUTDOWN the printer & remove the mains power plug
- REMOVE THE POWER & USB cables from the screen
- Remove the draw of the printer & the full front plastic panel using the 4 large bolts on the front.
- This will expose the sd card slot on the mainboard
- Insert the full size sd card upside down into the reader until it clicks in
- Power on the printer with the screen still disconnected, making sure its safe with the front off
- Wait 20-30 seconds, longer if you wish
- Power off the printer & remove the SD card
- Reconnect both the screen's cables
- Power the printer back on
- Go to the `Machine` tab & check your MCU firmware is now the same number as the other two
- Shutdown the printer & reassemble to finish!
- WELL DONE!!

SV06/SV06+
- Copy the firmware onto a Micro SD card
- SHUTDOWN the printer
- Insert the sd card into the reader until it clicks in
- Power on the printer
- Wait 20-30 seconds, longer if you wish
- Power off the printer & remove the SD card
- Power the printer back on
- Go to the `Machine` tab & check your MCU firmware is now the same number as the other two
- Youre all done. WELL DONE!!
