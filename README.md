# TI-Sensor-Tag-with-Raspberry-Pi

Muhammad Irfan Saleem, Senior Software Engineer, Virtual Force Pvt. Ltd.

In this writeup, we will learn how to Connect TI Sensor Tag with Raspberry Pi via bluetooth to get Ti Sensor tag's sensor readings.

# Things needed
 
   Raspbery Pi 3 and TI SimpleLink SensorTag.
   
# Get started

I'm using Raspbery Pi 3 in which bluethooth is build-in, so I didn't need 'Bluetooth CSR 4.0 Dongle'.

For the instalation of Bluez we folow the IMB IoT R[[ecepie: https://developer.ibm.com/recipes/tutorials/ti-sensor-tag-and-raspberry-pi/[{{;kj
as following:

First of all we need to setup Bluez setup in the Raspbery Pi and enable it for connecting bluetooth devices.

#1. Install Bluez

 1. Get the latest version of Bluez (the Bluetooth Stack for Linux). One can use wget utility to download the latest tar ball as shown      below, on the Raspberry Pi terminal:
 wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.32.tar.xz
 
 2. Before installing Bluez, the Raspberry Pi environment needs to be updated to have the following set of libraries installed on it:
	•	glib
	•	d-bus
	•	libudev
	•	libical
	•	readline
	•	libusb
	Use the following set of commands to perform the installation of the said libraries:
 
 sudo apt-get install libglib2.0-0 libglib2.0-dev 
		
	.	sudo apt-get install libdbus-1-dev 
	.	
	.	sudo apt-get install libudev-dev 
	.	
	.	sudo apt-get install libical-dev 
	.	
	.	sudo apt-get install libreadline-dev
	.	
	.	sudo apt-get install libusb-1.0-0-dev
 
 3. Extract the tar file downloaded earlier. Under the extracted directory structure, navigate to the main directory, whose name should read similar to bluez-5.32. You should find the configuration file configure under the directory mentioned above.
 
 While configuring the bluez, we need to ensure that the following options are included:
 --with-systemdsystemunitdir  --with-systemduserunitdir   --enable-library
 
 4. Execute the following command to configure the Bluez
 sudo ./configure --prefix=/usr --mandir=/usr/share/man --sysconfdir=/etc --localstatedir=/var --with-systemdsystemunitdir --with-systemduserunitdir --enable-library
 
 5. Post successful configuration, now perform make to install the Bluez
 
 sudo make
 
 Of the tools available with the Bluez package, hcitool and gatttool are the two tools that will be of our interest, during the scope of this recipe. We shall use these tools to discover bluetooth addresses of the bluetooth enabled devices, that are physically close to the Raspberry Pi device and use bluetooth services published by the devices (in our case the TI SensorTag).
 
 6. While in you are still in the main directory, copy the gatttool binary that got created in the earlier step, to ‘/usr/local/bin‘ folder
 sudo cp attrib/gatttool /usr/local/bin

#2. Scanning to discover the TI Sensor Tag

Just run the below command in terminal it enable the port:
  
      sudo hciconfig hci0 up
 
To find out the near by activated bluetooth devices list, run: 

      sudo hcitool lescan
      
A list of bluetooth devices should come, find out the device with name "CC2650 Sensor Tag", something like this:

      C4:BE:95:A3:09 CC2650 Sensor Tag
## Note   
If you didn't find any device in the list which matches the above format, kindly press any button on the Ti Sensor Tag, there should be a green blinking which means Tag is ready to connect but if there no light is blinking then it means it already connected or paired to some other system. First then figure out Tag's paired system and disconnect it from it, then it will be availabe. 
If this didn't work then try rebot the Ti Sensor Tag or restart the Raspbery Pi.

#3. Pair Raspberry Pi with TI sensor Tag







