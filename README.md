# RaspberryPi Info Display Raspbian

## Epiphany Browser 

1. Install Raspbian 


2. `sudo raspi-config`
	- Enable SSH


3. Now start the terminal and update your system:
	```
	sudo apt-get update
	sudo apt-get upgrade
	```

4. Install Epiphany (browser), x11 server utils, xautomation and unclutter (hide the cursor from the screen)
	```
	sudo apt-get install epiphany-browser x11-xserver-utils xautomation unclutter
	```


5. Add  shell script: /home/pi/fullscreen.sh
	
  ```
  nano /home/pi/fullscreen.sh
  ```
 
 Copy this :
	
  
  ```
  sudo -u pi epiphany-browser -a --profile ~/.config [URL] --display=:0 &
  sleep 15s;
  xte "key F11" -x:0
  ``` 

	
	
 

Make the file executable:

  ```
  chmod +rx fullscreen.sh
  ```
 
	


6. Edit the autostart file to run the script:

   ```
   nano ~/.config/lxsession/LXDE-pi/autostart
   ```
	

The autostart files needs to look like this:
	
  
  ```
  
	@xset s off
	@xset -dpms
	@xset s noblank
	@/home/pi/fullscreen.sh
	
  ```






7. Reload the page (if needed) every hour:
	
  `
	crontab -e
	`

Add this:
  
  `
	0 */1 * * * xte -x :0 "key F5"
  `

  





## Clean up the boot process ##


Disable the Raspberry Pi ‘color test’ by adding to

  `
  sudo nano /boot/config.txt
	`
  
 the line:
 
  `
  disable_splash=1
	`
  
  
Disable the Raspberry Pi logo in the corner of the screen  by adding to
  
  `
  sudo nano /boot/cmdline.txt
	`
 
 the line:

  `
 logo.nologo
  `

Disable the various bits of output from the kernel and friends by adding in the same file  
  
  `
  consoleblank=0 loglevel=1 quiet
	`
  
Disable the login prompt by running as root 

``
systemctl disable getty@tty1
`` 
 
 
 

 










 
 
 ### Splashscreen ### 

1. Install FBI:

	`
	sudo apt-get install fbi
	`

2. Download splash.png:
	
  `
	sudo wget [image url] & mv [image name] splash.png
	`

3. Move splash image to /etc/:

	`
	sudo mv splash.png /etc/
	`

4. Add script:

	```
	sudo nano /etc/asplashscreen
	```

	```
	do_start () {
		/usr/bin/fbi -T 1 -noverbose -a /etc/splash.png
		exit 0
	}
	case "$1" in
		start|"")
	do_start
	;;
	restart|reload|force-reload)
	echo "Error: argument '$1' not supported" >&2
	exit 3
	;;
	stop)
	# No-op
	;;
	status)
	exit 0
	;;
	*)
	echo "Usage: asplashscreen [start|stop]" >&2
	exit 3
	;;
	esac
	:
	```

5. Run:

	```
	sudo mv asplashscreen /etc/init.d/asplashscreen
	```

6. Run:

	```
	sudo chmod a+x /etc/init.d/asplashscreen
	sudo insserv /etc/init.d/asplashscreen
	```
  
7. Reboot
  
  ``
	sudo reboot
	``
