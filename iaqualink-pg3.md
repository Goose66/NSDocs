# iAquaLink
A NodeServer for Polyglot v3 (PG3) that interfaces with the iAquaLink™ cloud service to allow the ISY to control Jandy® AquaLink® pool controllers and accessories. See https://www.iaqualink.com for more information on iAquaLink™.

### Instructions for PG3 installation:
From the Polyglot Dashboard:
1. Install the iAquaLink node server from the Polyglot Node Server Store.
2. Add/modify the following Configuration Parameters under Configuration (note that the keys should automatically be added for required parameters):
    - key: username, value: username (email address) for iAquaLink account (required)
    - key: password, value: password for iAquaLink account (required)
    - key: sessionTTL, value: number of seconds that the session ID is refreshed in order to avoid timeout (optional - defaults to 43200 (12 hours))
    
    Once the required configuration parameters have been saved, the node server will attempt to connect to the iAquaLink Service on the next longpoll. This may take a couple of minutes, so be patient.  Check the Polyglot Dashboard for notices regarding bad configuration parameters and connection failure conditions.
3. Once the iAquaLink Service node appears in the ISY Adminstative Console and the iAquaLink Service shows as connected, click "Discover Devices" in the iAquaLink Service node to load nodes for the systems (pool controllers), devices, and OneTouch macros configured in your account.

### Notes for latest version (v3.1.10)
1. After adding all the nodes from "Discover Devices," the node states in the ISY Admin Console will all display with default or "N/A" values. The intial values should be retrieved at the next polling of the iAqualink service. However, depending on timing, the initial state value messages for the new nodes may arrive before the Admin Console has added the nodes, in which case the values will be lost and subsequent polls will not update the values. In that case, to get the initial values for the node states, use the "Force Update" for each Aqualink Controller node to retrieve the latest state values for that controller.
2. The nodeserver does not attempt to connect and login to the iAquaLink service until the first longpoll - approximately 60 seconds after the nodeserver starts. This is done to allow network components to reestablish connections when recovering from a power failure. The nodeserver will continue to attempt to connect and login every longpoll (e.g., every 60 seconds) until a connection is established, so watch your Polyglot Dashboard messages for connection errors or bad credentials when starting/restarting to avoid locking out your account.
3.Please don't set the shortPoll value to an extremely low value, e.g. < 10 seconds. Repeated, frequent pollying of the iAquaLink service may result in Zodiac making moves to block access by the node server.
4. The nodeserver relies on polling of the iAquaLink service and toggling of device states. Because of this, the nodeserver must first update the state before peforming any On, Off, etc. commands. Please be patient and provide time (up to shortPoll seconds) for the state change to be reflected before retrying your command. Because of the longer than normal polling period (15 seconds), you may want to structure your programs in the ISY to perform a Force Update on the MyQ Service Node before other programs that depend on the device status are executed.
5. If you change the setup on your AquaLink (temperature unit, type of lights or devices assigned to the AUX relays, etc.), you must delete all the nodes EXCEPT the iAquaLink Service node from the Polyglot Dashboard (not the ISY), restart the node server, and perform the "Discover Devices" procedure again. If you add new or modify the labels of existing OneTouch macros (scenes), a "Discover Devices" will pick them up the changes. But if you reset (delete) a OneTouch macro, you must delete the corresponding node from the Polyglot Dashboard as well.
6. To delete a device node or a OneTouch macro node, you must use the PG3 Dashboard to ensure the node does not reappear in the ISY Admin Console the next time that Polyglot and/or the iAquaLink nodeserver are restarted.
7. The iAquaLink Service node sends an AWAKE command (heartbeat) every long poll interval for node server health tracking. If the node server is stopped or crashes, no further AWAKE commands will be set and the "iAquaLink Service Connected" state should go to False. All other state values for all other nodes will remain unchanged, including the "Online" state of any AquaLink Controller nodes.

For discussion and feedback, please use the ISY Forums here: https://forum.universal-devices.com/forum/394-iaqualink/

### Version History
Current release:
3.1.10 - New feature (2023-04-19)
- fixed bug when no devices returned
- fixed bug falsely claiming no serial number present for System
- added support for OneTouch macros
- refactored code to asyncio-friendly structure

Previous releases:
3.0.9 - Testing fixes (2022-10-23)
- fixed up service node connection state (ST) reporting
- added AWAKE command (heartbeat) processing
- fixed pool and spa setpoint 

3.0.8 - Initial Release (2022-10-21)
- Converted iaqualink-polyglotv2 to PG3 API

