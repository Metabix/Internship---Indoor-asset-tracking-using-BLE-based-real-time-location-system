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
		                        BLE-RECIVER-IDENTIFIER
								                      --->
										                   BCN-IDENTIFIER
														                  -->[ RECEIVED-TIMESTAMP -->RSSI VALUE] # FIFO queue of last 20 readings																		                       -->RSSI Value
																		  -->Running Average RSSI Value
																		  -->Last Received Timestamp
		   and stored in an object outlined above. The Timestamp and RSSI values of each beacon received from each receiver is put in a FIFO queue of 20
		   The running average RSSI value is calculated for 20 readings and stored under beacon identifier along with last beacon timestamp
		4. Every second the Location engine scans as follows:
		                   4.a For each beacon identify the top three BLE-RECEIVER-IDENTIFIERs which have the highest value of Running Average RSSI 
						        Value for that BCN-IDENTIFIER
						   4.b Convert RSSI Value to distance
						   4.b Pass the BLE-RECEIVER position,computed distance values of top three to trilateration algorithim to compute the actual postion in x,y,z
						       coordinates and save them in a position object
							             POSITION 
										         --->
												      BCN-IDENTIFIER
													                  --> [x,y,z]
																	  --> Last Received Timestamp 
																	  
	    5. A gevent Flask based HTTP API to provide list of beacons found and their computed coordinates by sending the position objects to UI Client when requested
		6. A gevent Flast based UI server to serve the UI client on browser
		7. Store the first beacon name, position location of the beacon in SQLite3 database with date and time stamp. If position location changes by 1.5 meters from previous reading 
		   then insert a new record in database with new timestamp and new position
		
		
		References:
		    a. Trilateration algorithm in python 
			                         https://github.com/akshayb6/trilateration-in-3d
									 https://gist.github.com/marmakoide/6bf9254c4d95d2dec70a869706376de9
									 https://github.com/noomrevlis/trilateration
			b. RSSI(RF Received Signal Strength) to distance calculation:
			                         https://gist.github.com/eklimcz/446b56c0cb9cfe61d575
			c. Gevent Flask Server
			                         https://gist.github.com/viksit/b6733fe1afdf5bb84a40
									 
4. UI Client:
        REquirements:
		    1. Show a static indoor map of a location
			2. Make one second interval AJAX calls to the server to fetch beacon Positions
			3. Translate the XY coordinates in to pixels coordinates based on mapping of the location dimensions to pixels
			4. Draw the map pin object in dark red. 
			5. Mouse hovering over the Pin should provide a call out listing the Tag Id, X,Y,Z position in meters and tag last received timestamp
			6. As the tag starts moving mark the trail path in light brown color
			7. PRovide a button at top to reset all trails
			
		REferences:
		     https://github.com/wrld3d/wrld-indoor-maps-api/blob/master/TUTORIAL-TOOL.md
