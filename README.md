# Internet-of-Things-Support-in-Xinu-

Abstract:
This project presents the implementation of a device driver for a temperature sensor TMP36, an LDR, 
a Button and an LED in the Internet of Things. It allows the processes in the XINU Operating
System to access and utilize the devices connected to BeagleBone Black (BBB) Device through a
computer and remotely through a network. The devices connected to the BBB can communicate to
each other as well as the devices connected to another BBB. The project is divided into two parts.
First part deals with interfacing the devices with the GPIO and writing the low-level device driver
functions. A Device Description Language (DDL) is used to specialize the device drivers to the BBB.
The DDL, when processed at compile time, and generates the high-level codes for the devices. In
the second part, we establish a high-level abstraction of the device on an Edge Device and the
Cloud. The Edge reads the DDL file to enable access and establish an edge hosted version of the
device. A web based dashboard is developed to view and monitor the status of all the devices.



Introduction:

The Internet of Things (IoT) is the internetworking of physical devices, vehicles (&quot;connected
devices/smart devices&quot;), buildings and other items—embedded with electronics, software, sensors,
actuators, and network connectivity that enable these objects to collect and exchange data. The IoT
allows objects to be sensed and/or controlled remotely across existing network infrastructure,
creating opportunities for more direct integration of the physical world into computer-based
systems, and resulting in improved efficiency, accuracy, and economic benefit. When IoT is
augmented with sensors and actuators, the technology becomes an instance of the more general
class of cyber-physical systems, which also encompasses technologies such as smart grids, smart
homes, intelligent transportation and smart cities. Each thing is uniquely identifiable through its
embedded computing system but can interoperate within the existing Internet infrastructure.



Main.c file understanding:


main.c file contains the main application.Main Application is divided basically into three parts:

1)Xinu UDP initialization:

When Xinu boots first main will Initialize the UDP connection with the Edge server.The main process will wait 
for a Init packet from the edge server INIT status  = 200 with the BBB id should be set for particular board.
So if there are many BBB board are connected with the network each board is waiting for there INIT packet with BBB ID.
As in start Edge doesn't know IP address of the BBB, Edge will just send the Broadcast INIT packet for each BBB
with the BB ID set for each board. Once the BB reaceived a INIT packet with its BBID, it will ECHO the packet as a ack  
to the Edge and stored the IP and port of the Edge server and Initialized the BBB board  otherwise just ignore the 
packet. Once the BB is initialized it will create two process UDP server and UDP client.

2) Xinu UDP server:
UDP server process will continuously listen for packet. If a packet is reacived with the BB id  and the Device ID 
if will perform the Operation. For performing any action on any Device Edge has to set the device ID , BB id and 
Msg ID and the Status for the device. Every device connected to BB has a device id which is mentioned in the XML .
every message has its ID. and status the action which has to be performed on the device. E.g for a GPIO device 
like LED status 1 will turn on  the LED and 0 will turn off the LED.


3) Xinu UDP client:

UDP client process will send the INPUT data to EDGE server in some interval or based on INPUT interupt.
If a temperature sensor is connected with the BB it will provid the temperature information to a edge in every 
1 sec. so that BB can save power and remain idle. If a INPUT button or digital sensor is connected with the BBB 
BB will triggered IRQ when a rising or falling edge is detected on the device PIN. once the iterrupt is received BB will 
read the current status of the Input pin and send the status to the Edge server using UDP connection.





Build Procedure:

Prerequisite: Please install the GSL library for DDL code generation use line for more detail https://github.com/imatix/gsl

Folder: $XinuRoot/config/
It is the configuration folder which contains driver xml file and gsl file for reading the driver
configuration from 

Step1: cd $XinuRoot/compile/

Step2: make clean

Step3: make rebuild

Step4: make


It will generate main.c and all the driver init c file, which allow us to create a abstraction layer to initialized 
driver from a user understandable xml file and can be configured easily from xml.




