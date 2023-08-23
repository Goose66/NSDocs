# Autelis
A Node Server for Polyglot v3 (PG3) that interfaces with an Autelis Pool Control device to allow IoX to access the connected pool controller. The Autelis Pool Control devices are no longer manufactured and the website has been taken down. Note that currently only Jandy/Zodiac Aqualink controllers are supported. Pentair support may be added if a benefactor wants to pay for the migration.

### Instructions for PG3 installation:

From the Polyglot Dashboard:
1. Install the Autelis node server from the Polyglot Node Server Store.
2. Add/modify the following Custom Configuration Parameter under Configuration (note that the key should automatically be added for required parameters):
       
    - key: hostname, value: hostname or IP address of the Autelis device (required)
    - key: username, value: username for logging into the Autelis device (required)
    - key: password, value: password for logging into the Autelis device (required)
    - key: disableListener, value: 1 to disable the TCP listener for realtime state updates (optional - defaults to 0)
    - key: ignoreSolar, value: 1 to ignore Solar Heat settings (optional - defaults to 0)

    Once the required configuration parameters have been saved, the node server will attempt setup a connection to your Autelis Pool Control device. Check the Polyglot Dashboard for notices regarding bad configuration parameters (i.e., bad login credentials) and any connection failure conditions.

3.  Once a connection is estalished, the node server will automatically load nodes for the Pool Controller and attached devices.

### Notes for latest version (v3.1.2)

1. If you are upgrading from version 3.0.1, you must stop the node server, delete all of the nodes for the node server (using the PG3 dashboard) and then restart the node server in order to have the color lights nodes get the correct node type.
2. The node server only adds nodes that are returning values, so it should only add nodes for those equipment and temp_controls specific to your installation, except for solar heat which it seems to add regardless. You can add a flag to the custom parameters to ignore solar heat (see above).
3. The nodes are initally added with default names based on their common usage. You may change the names (especially for the AUX relays) to the name of the pool device controlled by the node.
4. The equipment (pump, aux, and macro (OneTouch)) nodes only take DON and DOF commands, so if you put the nodes in a Managed Scene and do a Fast On or Fast Off, the node will not respond.
5. If you turn spa and spaht or pool and poolht on right after one another (such as putting both in a scene), the second one does not take. There has to be a 2 or 3 second delay between spa and spaht or pool and poolht to make sure both are processed, so it will require a program.
6. Temperatures and setpoint values are reported to IoX in the temperature unit ("F" or "C") as configured in your pool controller. If this is changed while the node server is running, then the temperature and setpoint values will start being reported in the newly selected units. However, the selection lists for changing the setpoint values will still consist of values for the temperature unit set when the node(s) were originally created. To fix this, you must delete the nodes (using the PG3 Dashboard) and restart the node server to rediscover the nodes with the correct temperature unit.
7. If you do not wish to use the TCP listener for realtime status updates, e.g., because the PolISY/eISY aren't on the same LAN and/or are connected through a firewall or bridge that doesn't support TCP sockets, then set the disableListener configuration parameter described above to "1" or "Y". The node server will not attempt a direct conenction to the Autelis Device using the TCP sockets and will simply use the REST calls to the Autelis Device website to poll for state. In this case, you may want to reduce the shortPoll interval from the default of 60 seconds to, say, 10 seconds.
8. The connection to the Autelis device is checked and/or "fixed up" in the longPoll calls (currently set to 5 minutes). If an initial connection could not be made, e.g., due to timing issues with everything restarting after house-wide power failure, then the node server will try again in the next long poll call.
9. A "Node Server Status" state (driver) was added to the Pool Controller node. If the node server is stopped or fails, the "Node Server Status" will change from "Connected" to "Disconnected" or "Failed," respectively.

### Version History
3.1.2 - Added features (2023-08-26)
- added support for color lights
- added correct reporting of ON state for dimming AUX

3.0.1 - Initial Release (2023-08-16)

For discussion and feedback, please use the UD Forums <a href="https://forum.universal-devices.com/forum/431-autelis/" target="_blank">here</a>.
