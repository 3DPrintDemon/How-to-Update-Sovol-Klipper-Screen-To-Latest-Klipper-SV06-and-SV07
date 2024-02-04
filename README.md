# How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-SV06+-SV07-SV07+

[<img width="171" alt="kofi_s_tag_dark" src="https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/ace919af-8fd3-412a-96a5-fae422ab4758">](https://ko-fi.com/3dprintdemon)

## Don't forget if you like & use this project you can buy me a beer/coffee to say thanks. [https://ko-fi.com/3dprintdemon](https://ko-fi.com/3dprintdemon)

## IF THIS PROCESS IS DONE INCORRECTLY IT WILL BRICK YOUR PRINTER! BEFORE YOU ATTEMPT THIS THE OUTCOME IS TOTALLY ON YOU! 

## I HIGHLY RECOMMEND YOU MAKE A WORKING BACKUP OF YOUR PRINTER &/OR A CLONE OF YOUR EMMC!
## YOU HAVE BEEN WARNED! 

### If you need some macros be sure to check out these!
https://github.com/3DPrintDemon/Demon_KLIPPER_Essentials
***********************************************************************************************************************************************************

To update anything on the Sovol Klipper screen you absolutely MUST freeze or lock the kernel to stop it being updated when you run any updates or installers.
Also note this is NOT for beginners, it requires some knowledge of the system you're working on to achieve success! Please be careful!

First you have to log into your Sovol Klipper Screen using SSH. Use `Terminal` on a Mac or `Putty` on a PC.

once logged in paste in:
```
sudo armbian-config
```
It will bring up this blue background menu:



![armbian-config 1](https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/5efbc383-2470-496f-9916-b085c5ea5c59)

- Press enter to confirm choices & the keyboard arrow keys to navigate. 
- Choose the first option `System and Security`
- Press enter

Now it will load a red background menu BE CAREFUL HERE YOU CAN BREAK THINGS!!! Thats why its red!

![armbian-config 2](https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/d2fec019-74d1-4878-8814-f28c887d23fa)

- Press enter on the top line to change it so the screen reads like the image above.
- YOUR KERNEL IS NOW FROZEN!
- DON'T PRESS ON ANYTHING ELSE
- Move down to `OK` to confirm the choice & go back the blue menu.
- Move over to `EXIT` on the blue menu.
- Press Enter to exit

I find its good to do it this way as you can see its done, just don't be tempted to click other stuff!


Now you "should" be able to run the following commands WITHOUT Bricking your printer! Keep 'em crossed!

## BE SURE YOU WANT TO DO THIS! THIS IS THE POINT OF NO RETURN!
This will update all your system components to the latest available versions for this image.

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
Here you need your rabbits foot & to keep your fingers crossed as the new updated system attempts to start! 

If it fails to start you'll need to look at unbricking & reinstalling your image.
If it works like mine did GREAT well done, the tricky bit is over!

## UPDATING KLIPPER, MAINSAIL & MOONRAKER

Open your web browser & head over to your printer. Then on Mainsail open the `Machine` tab & find your `moonraker.conf` file & paste in at the bottom

moonraker.conf
```
[update_manager] 
channel: dev 
refresh_interval: 168  

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
Now `SAVE & RESTART` klipper & go back to the `Machine` tab, you should new have access to the `UPDATE MANAGER`
- Click the circle arrow button in the top of that section the get the latest update info.
- Click update on your components
- NOTE THIS CAN TAKE A LONG TIME - 10-20 minutes in some cases! Wait for it to complete

## CROWSNEST
A quick note on `Crowsnest` before we go further, the latest version is NOT compatible with this old Buster image used on this T300 printer. DO NOT UPDATE IT! IT WILL NOT WORK HERE!
It is very importatnt to leave `Crowsnest` exactly as it is!!

## ERROR TIME 

Your version of klipper will only be partially updated & you'll probably get a big red warning saying `PROTOCOL ERROR!`
& saying you need to update your MCU's!

Don't worry we're doing that now!

## UPDATING THE HOST MCU RPI & MCU FIRMWARE

Now your system is updated we need to clear those MCU errors, you need to update the rest of the printer.
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
STM32F103 with a "28KiB bootloader" and serial (on USART1 PA10/PA9) communication.
```
![Config](https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/875f9509-38ce-425c-bf77-f986d0709eb5)

Save & exit

now simply type 
```
make
```
This will now build your new MCU firmware

Now use a `FTP CLIENT` to log onto the printer & pull the `Klipper.bin` file from the `klipper/out` folder & download it to your computer.

RENAME THE FILE to something random, think cat walking across your keyboard random. This MUST be something different on each & every flash attempt. The board will not flash the same file name twice.


SV07 & SV07+ 
- Copy the file to a FULL SIZE SD CARD!!! Or at least a micro SD in a full size adapter!
- SHUTDOWN the printer & remove the mains power plug
- REMOVE THE POWER & USB cables from the screen
- Remove the draw of the printer & the full front plastic panel using the 4 large bolts on the front.
- This will expose the sd card slot on the mainboard

![IMG_3568](https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/ac32b6e8-d6e5-4463-9a71-ce905b9ffc07)

- Insert the full size sd card upside down into the reader until it clicks in

![IMG_3569](https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/28bdd324-46fe-43f3-85b0-3bfeec39a6c5)
![IMG_3570](https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/22015966-ce98-4d7f-b41e-2d46b39d0df5)

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
- You're all done. WELL DONE!!

Your `Machine` tab should now look like this!

![Version](https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/a57e33fe-b0f5-48a1-84d8-a266112d10df)


I hope this helps you update your printer/s! Happy printing!

### If you need some macros be sure to check out these!
https://github.com/3DPrintDemon/Demon_KLIPPER_Essentials


[<img width="171" alt="kofi_s_tag_dark" src="https://github.com/3DPrintDemon/How-to-Update-Sovol-Klipper-Screen-To-Latest-Klipper-SV06-and-SV07/assets/122202359/ace919af-8fd3-412a-96a5-fae422ab4758">](https://ko-fi.com/3dprintdemon)

## Don't forget if you like & use this project you can buy me a beer/coffee to say thanks. [https://ko-fi.com/3dprintdemon](https://ko-fi.com/3dprintdemon)

