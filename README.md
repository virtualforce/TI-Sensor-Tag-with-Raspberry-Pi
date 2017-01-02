# TI-Sensor-Tag-with-Raspberry-Pi

Muhammad Irfan Saleem, Senior Software Engineer, Virtual Force Pvt. Ltd.

In this writeup, we will learn how to Connect TI Sensor Tag with Raspberry Pi via bluetooth to get Ti Sensor tag's sensor readings.

# Things needed
 
   Raspbery Pi 3 and TI SimpleLink SensorTag.
   
# Get started

I'm using Raspbery Pi 3 in which bluethooth is build-in, so I didn't need 'Bluetooth CSR 4.0 Dongle'.

For the instalation of Bluez we folow the [IMB IoT Recipes] (https://developer.ibm.com/recipes/tutorials/ti-sensor-tag-and-raspberry-pi/)
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

Bluez we earlier install, helps us in pairing with Ti Sensor Tag in order to read sensor data:

Establish an interactive session:

Syntax

 	gatttool -b [bluetooth_adr] --interactive

Command

	gatttool -b C4:BE:78:A6:09 interactive
	
Once the session is establish, you will see a command prompt as following:

    	[C4:BE:95:A3:09][LE]>
Connect the Raspbery Pi device to the SensorTag:

	[C4:BE:95:A3:09][LE]>connect
Following are the set of messages you will get to see, as the connect operation is being performed, to pair up the TI Sensor Tag and the Raspberry Pi device:

	Attempting to connect to C4:BE:95:A3:09

	Connection successful

	[C4:BE:95:A3:09][LE]>
Now the Ti Sensor Tag is paired with the Raspbery Pi successfully Hurray!

#4. Getting Temperature from Ti SensorTag on to Raspberry Pi

The TI Sensor Tag includes 10 low-power MEMS sensors (light, digital microphone, magnetic sensor, humidity, pressure, accelerometer, gyroscope, magnetometer, object temperature, and ambient temperature) and many more including battery percentage, external hardware button states etc. For now, we shall work on retrieving the Temperature readings from the TI Sensor Tag

To retrieve temperature data onto your Raspberry Pi, you should enable the temperature sensor(s) on TI CC2650, so that it starts measuring the temperature. This data or the reading is held in a temperature designated handle and you need to read it from this handle.

Think of a handle as a connection endpoint, to one of the many services offered by the bluetooth device. For instance, TI CC2650 has 10 different handles, one each for each of the MEMS sensors mentioned above.

Enable the Temperature Sensor(s), by issuing the command as follows:

    char-write-cmd 0x24 01

This activates the temperature sensors on TI CC2650, which were in standby mode

Now read the temperature data in hexadecimal values from this handle

    char-read-hnd 0x21
    
You should see both IR and Ambient Temperature outputs in Hexadecimal (Hex) format:

    Characteristic value/descriptor: 50 09 e4 0b
    
#Note: 
The handles specified in the read and write above are for the TI CC2650 SensorTag only.

#5. Convert Temperature from Hex to Celsius and Fahrenheit

Convert the temperatures from Hex to Degree Celsius and Fahrenheit using the appropriate conversion factor. For your convenience, the conversion of temperature from Hex to Degree Celsius and Fahrenheit has been demonstrated using a Python script.

For the TI CC2650, open a Python shell and execute the following set of lines:

	raw_temp_data = '50 09 e4 0b' # Start with raw data from SensorTag

	raw_temp_bytes = raw_temp_data.split() # Split into individual bytes

	raw_ambient_temp = int( '0x'+ raw_temp_bytes[3]+ raw_temp_bytes[2], 16) # Choose ambient temperature (reverse bytes for little 	endian)

	ambient_temp_int = raw_ambient_temp >> 2 & 0x3FFF # Shift right, based on from TI

	ambient_temp_celsius = float(ambient_temp_int) * 0.03125 # Convert to Celsius based on info from TI

	ambient_temp_fahrenheit = (ambient_temp_celsius * 1.8) + 32 # Convert to Fahrenheit
	
Printing the values for the parameters ambient_temp_celsius and ambient_temp_fahrenheit, shall display the temperature readings for you:

	print ambient_temp_celsius

	print ambient_temp_fahrenheit

#Conclusion:

In this writeup, we demonstrated how quickly you can configure Sensor Tag TI CC2650, pair it with Raspberry Pi device, retrieve the sensor data on to the Raspberry Pi device in the hexadecimal format and then use scripts ( Python – In this recipe) to have the readings from temperature sensor converted to Degree Celsius and Fahrenheit. Developers can consider the Python script as template and build on top of it, to retrieve data for the remaining set of sensors.


