Project Title:  Indoor asset tracking using BLE based real time location system

Abstract:
Most of the asset tracking systems are non real time. Real time asset tracking system 
becomes necessary when an asset is of high value and critical to be located in time.
Examples of such high value assets could be manufacturing parts lying in various locations on shop floor,  
critical medical equipments lying in various locations in hospitals etc.
This system would enable users to locate critical assets in real time with an accuracy of about 3 feet 
without having to spend time searching for the same.
The BLE based real time location system consists of BLE receivers, BLE asset tags , a location engine server
and UI server. The BLE beacon receivers are placed at known locations. BLE tag which is connected to the asset 
emits wireless BLE beacons periodically. The BLE receivers within range receive the BLE beacons and relay 
the BLE beacon message and its RF signal strength
to the location engine server. The location engine server determines the absolute location of the asset using 
trilateration. The UI server uses this information to provide a visual representation
of the asset location on a UI client which shows the asset location as a pin superimposed on an indoor static map

The following would be the solution components of the project
1. BLE Beacon Receiver:
    Requirements:
	   1.Keep its clock synchronized with server clock at all times in 5 second intervals
	   2.Each BLE receiver shall have a unique identifier name and a predefined location
	   4.In a continuos loop -- Actively listen to BLE (Eddystone) Beacons. Once the Beacon 
	      is received spawn a new thread to extract the Beacon ID, RSSI value(RF signal strength), add the received timestamp, beacon unique ID and
		  BLE receiver unique id 
          and send it to location engine server via Wi-Fi/UDP
    
    References:
       a. https://github.com/taka-wang/py-beacon
       
2. BLE Beacon Generator:
     Requirements:
       1. Each BLE Beacon Generator shall have a unique identifier name
       2. The RF transmit power shall be configurable
       3. The BLE Beacon advertising interval shall be configurable from 100ms to 1000ms

    References:
       a. https://hackaday.io/project/10314-raspberry-pi-3-as-an-eddystone-url-beacon
	   
3. Location Engine Server:
     Requirements:
	    1. A UDP server listening to Broadcasts from BLE Beacon Receiver
		2. A configurable map of BLE receiver unique Id and their location co-ordinates
		
		3. Upon receiving UDP data, BLE receiver identifier,the beacon identifier , RSSI value and timestamp are extracted
