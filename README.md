# Node-red-contrib-Verisure

This module provides two nodes, one config and one function node, to fetch the status of a Verisure site.
The module and it's dependencies is strictly unofficial, not supported in any way by Verisure AS (and use is probably not encouraged by them)

# Unreleased branch for adding device data support

## Install
NB! Breaking changes in version 0.4, separation of alarm node and newly added sensor node.

To install 
To install the stable version use the `Menu - Manage palette` option and search for `node-red-contrib-verisure`, or run the following command in your Node-RED user directory (typically `~/.node-red`):

	$ npm i node-red-contrib-verisure
	
Or, to install, download the files to a local folder, same structure. Switch to your .node-red directory and use npm link or npm install

## Dependencies

Depends on verisure package

	$ npm install verisure --save

## Nodes

### Verisure config

This is just a credential node, to separate out storage of your credentials and simplify usage of multiple Verisure nodes/actions in the future

### Verisure Alarm node

This node connects to the first verisure site returned with your username and password. Then fetch the armed or unarmed status. The fetch happens whenever input is recieved. The node changes the payload, but nothing else in the message object. Payload returned is a simple json structure:
	
	{ 'current_status': "ARMED_AWAY", 'changed': false, 'date': "2016-01-01T00:00:00.000Z", 'name': "John D. Oe" }
	{ 'current_status': "ARMED_HOME", 'changed': false, 'date': "2016-01-01T00:00:00.000Z", 'name': "John D. Oe"  }
	{ 'current_status': "DISARMED", 'changed': false, 'date': "2016-01-01T00:00:00.000Z", 'name': "John D. Oe"  }

Verisure node is currently expecting alarm to be in DISARMED state when starting. If not, the first Changed status will be wrong.

### Verisure Sensor Node

This node connects to the first verisure site returned with your username and password. It can then be used to fetch values from named sensor in the site.
The node accepts input in json format.

	{'type': "climate", 'label': "2RTL M7"} // returns sensor data from this device
	{'type': "climate", 'index': 4} // returns sensor data from this device, number 4 in array list
	{'type': "climate", 'area': "Master Bedroom"} // returns sensor data from this device area
	{'type': "lock", 'label': "2YGL M8"} // returns lock data from this device
	{'type': "lock", 'index': 5} // returns lock data from this device
	{'type': "doorwindow", 'index': 5} // returns door or window state data from this device
	{'type': "site"} // returns full site overview as object. Hook up a debug to see all you can use

You will find all indexes and labels if you output a fill site object and look through it. Note that access by index will be faster. The node always reaches out to get new data from Verisure.

TO BE ADDED in this feature branch

#### Return objects
Climate: {"deviceLabel":"2ZEL TMP","deviceArea":"Gang","deviceType":"SMOKE2","temperature":21.9,"humidity":40,"time":"2018-10-19T17:51:54.000Z"}

Doorlock: {"deviceLabel":"2ZF7 SFG","area":"Hoveddør","userString":"Tor","method":"CODE","lockedState":"UNLOCKED","currentLockState":"UNLOCKED","pendingLockState":"NONE","eventTime":"2018-10-19T17:33:23.000Z","secureModeActive":false,"motorJam":false,"paired":true}

DoorWindow: {"deviceLabel":"2JRY 4WTH","area":"Inngang","state":"CLOSE","wired":false,"reportTime":"2018-10-19T17:34:00.000Z"}

Error: { 'Error': true, 'message': 'No such device' }

# Security
Verisure system is quite secure as such, but care should be taken on your side as well when integrating. Node-red should be set up with your custom encryption key, that will be used for securing credentials. This is done by setting the credentialSecret key in the node-red settings files. 
You should also consider setting up a specific user account on your verisure site, used only for integration, with as few rights as possible.

# Troubleshooting
Not many common issues known at this point. 
- If you pull to often from Verisure, they will throttle your requests.
- It seems like use of the same account for this node and the verisure App might log you out of the app.
- Breaking change in version 0.4, node-red will complain about missing VerisureNode. You need to replace this in your flows with the new VerisureAlarmnode
- And by the way, do not expect that your installation company managed to keep naming standards consistent. Area for your main door lock and your main door open/closed sensor might be different...
- When working with the site object, note that naming standards in the objects properties are inconsistent, from the Verisure package, probably inherited from the Verisure apis/datamodel. For instance is area and devicearea properties used alternating.

# Legal Disclaimer

This software is not affiliated with Verisure Holding AB and the developers take no legal responsibility for the functionality or security of your alarms and devices. Neither does any of the contributers to this package. Treat your Verisure credentials with the uthermost care.
