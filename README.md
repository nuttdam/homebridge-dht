# homebridge-dht

Supports integration of a DHT11/DHT21/DHT22/DHT33/DHT44 Temperature/Humidity
Sensor into hombridge via the pigpio library on a Raspberry PI.   I have tried
numerous other interface methods for the DHT22, and found that this was least
problematic.  Also includes optional reporting of the RaspBerry PI CPU Temperature.

Also support use of multiple DHT22's, see config.json fragment.

# Installation

1. Install homebridge using: npm install -g homebridge
2. Install homebridge-dht using: npm install -g homebridge-dht
3. Install the pigpiod library via these commands
    sudo apt-get update
    sudo apt-get install pigpio python-pigpio python3-pigpio
4. Download the DHT22 Sample program from here
    http://abyz.co.uk/rpi/pigpio/code/DHTXXD.zip
5. Copy the patch file `test_DHTXXD.patch` from /usr/local/lib/node_modules/node_modules/homebridge-dht/

cp /usr/local/lib/node_modules/node_modules/homebridge-dht/test_DHTXXD.patch .

6. Then issue this command to patch test_DHTXXD.c

patch < ../test_DHTXXD.patch

7. Compile with this command

```
gcc -Wall -pthread -o DHTXXD test_DHTXXD.c DHTXXD.c -lpigpiod_if2
```

8. Copy DHTXXD to /usr/local/bin/dht22, and make executable.
9. Follow one of the numerous guides to wire up a DHT22 to a Raspberry PI.
   Default GPIO pin to connect to is GPIO4 
10. Optional - Create a file in /usr/local/bin/cputemp containing

```
#!/bin/bash
cpuTemp0=$(cat /sys/class/thermal/thermal_zone0/temp)
cpuTemp1=$(($cpuTemp0/1000))
cpuTemp2=$(($cpuTemp0/100))
cpuTempM=$(($cpuTemp2 % $cpuTemp1))

echo $cpuTemp1" C"
#echo GPU $(/opt/vc/bin/vcgencmd measure_temp)
```

10. Update your configuration file. See sample-config.json in this repository for a sample.
Optional parameters includes

* dhtExec - Full command including path to read dht22 sensor.  Not needed
unless dht22 is installed in a location not on the path.  Defaults to dht22
ie "dhtExec": "/usr/local/bin/dht22"

* cputemp - Full command including path to read cpu temp sensor.  Not needed
unless cputemp is installed in a location not on the path.  Defaults to cputemp
ie "cputemp": "/usr/local/bin/cputemp"

* gpio - Gpio pin to read for dht22 sensor.  Defaults to 4
ie "gpio": "4"


# Configuration - with cputemp

```
{
    "bridge": {
        "name": "Penny",
        "username": "CC:22:3D:E3:CD:33",
        "port": 51826,
        "pin": "031-45-154"
    },

    "description": "HomeBridge Heyu Status Control",

 "platforms": [],

   "accessories": [
	{ "accessory":        "Dht",
	"name":               "cputemp",
	"service":            "Temperature" },
	{ "accessory":        "Dht",
        "name":               "Temp/Humidity Sensor",
        "service":            "dht22" }
	]
}
```
# Configuration - without cputemp
```
{
    "bridge": {
        "name": "Penny",
        "username": "CC:22:3D:E3:CD:33",
        "port": 51826,
        "pin": "031-45-154"
    },

    "description": "HomeBridge Heyu Status Control",

 "platforms": [],

   "accessories": [
	{ "accessory":        "Dht",
        "name":               "Temp/Humidity Sensor",
        "service":            "dht22" }
	]
}
```
# or with multiple DHT22's
```
{ "accessory":   "Dht",
  "name":        "Temp/Humidity Sensor - Indoor",
  "gpio":        "4",       
  "service":     "dht22" },
{ "accessory":   "Dht",
  "name":        "Temp/Humidity Sensor - Outdoor",
  "gpio":        "5",   
  "service":     "dht22" }

```

# ToDo

Stop using the external program call, and call the pigpio library directly using
npm module instead.
