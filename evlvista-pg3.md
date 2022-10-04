# EnvisaLink-HW
A node server for Polyglot v3 (PG3) that interfaces with a Honeywell Vista series alarm panel through EnvisaLink™ EVL-3/4 and DUO™ adapaters from EyezOn. See http://www.eyezon.com/ for more information.

### Prerequisites
Before installing the node server, make sure your EnvisaLink device is connected to your compatible Honeywell/Ademco Vista alarm panel and your home network and is accessible via a web browser at http://envisalink.local or http://<device IP>. You will need to know the user ID ("user") and password (defaults to "user") for the EnvisaLink device. In addition, make sure that the additional programming steps for your alarm panel to ensure full functionality with the EnvisaLink device have been performed, as outlined in the installation instructions. The installation instructions may be found here (last verified 2022-09-28):

https://www.eyez-on.com/EZMAIN/EyezonEnvisalinkHoneywellInstallationGuide2018.pdf

### Instructions for installation of node server:
From the Polyglot Dashboard:
1. Install the EnvisaLink-HW node server from the Polyglot Node Server Store.
2. Add/modify the following required Configuration Parameters under Configuration (note that the keys should automatically be added for required parameters):
    - key: hostname, value: locally accessible hostname or IP address of EnvisaLink or DUO device (e.g., "envisalink.local" or "192.168.1.145")
    - key: password, value: password for EnvisaLink device
    - key: usercode, value: user code for disarming alarm panel (e.g., "5555")
3. You can also add the following optional Configuration Parameters under Configuration:
    - key: numpartitions, value: number of partition nodes to generate (defaults to 1)
    - key: numzones, value: number of zone nodes to generate (defaults to 8)
    - key: disablewatchdog, value: 0 or 1 for whether EyezOn cloud service watchdog timer should be disabled (defaults to 0 - not disabled)
    - key: zonetimerdumpflag, value: numeric flag indicating whether dumping of the zone timers should be done on shortpoll (1), longpoll (2), or disabled altogether (0) (defaults to 1 - shortpoll)
    - key: smartzonetracking, value: 0 or 1 for whether node server should use a heuristic to track zone status (open, closed) or just rely on EnvisaLink (defaults to 0 - do not track)

**PLEASE NOTE**: Once the required configuration parameters have been saved, the node server will create the panel, partition, and zone nodes in the next shortpoll event, and then attempt to connect to the EnvisaLink device and update all of the panel, partition, and zone states over multiple, subsequent shortpoll events. It may take two or three minutes depending on the shortpoll interval before all nodes and states are reflected properly in the ISY, so please be patient. Check the Polyglot Dashboard for notices regarding bad configuration parameters and connection failure conditions.

### Using the node server events (commands)
The nodes of the EnvisaLink-Vista Node Server generate the following incoming commands to the ISY, allowing the nodes to be added as controllers to scenes and the commands to be used as triggers in programs:

PARTITION
- Sends a *DON* ("Alarm Triggered") command when the partition is alarming
- Sends a *DOF* ("Alarm Restored") command when the partition is disarmed

PANEL
- Sends an *AWAKE* ("Heartbeat") command periodically for heartbeat monitoring (not currently implemented)

### Notes for latest version (v3.0.3)

1. Known issues:
 - the Alarm Panel node has an AWAKE command (heartbeat) defined in the profile but this functionality has not been implemented.
 - On Entry Delay (but not Exit Delay?) any Battery Trouble status will vacillate between True and False every couple of seconds due to the seemingly random nature of the keypad update messages that the node server relies on for much of its state tracking.
2. If the connection to the EnvisaLink is lost, or if the node server doesn't hear from the EnvisaLink for 20 seconds, then the connection is reset and the node server will attempt reconnect on the next short poll and every subsequent short poll until connection is reestablished or the node server is shutdown.
3. Any change to "usercode", "disablewatchdog", "zonetimerdumpflag", or "smartzonetracking" parameters while the node server is running should take effect immediately (i.e. at next time the related functions run). Any change to the "hostname" or "password" parameters while the node server is running will be used on the next conection attempt to the EnvisaLink, whether the initial connection or a reconnect. If you change the "numpartitions" or "numzones" parameters after the AlarmPanel node has been created, you must restart the node server for these new parameter values to take effect.
4. If your EnvisaLink is firewalled and can not connect to the EyezOn web service, then the EnvisaLink will reboot every 20 minutes ("Watchdog Timer") in order to try and reestablish the connection to the web service. This will kill the connection to the node server as well and it will (attempt to) reconnect on the next short poll. If you set the "diablewatchdog" configuration setting to 1, the node server will send a periodic poll to the EnvisaLink to reset the Watchdog Timer so that the EnvisaLink won't reboot. The poll is sent every long poll if the "diablewatchdog" configuration parameter is set, so the "longpoll" configuration setting needs to be less than 1200 seconds (20 minutes).
5. The zone timers ("Time Closed") represent the time since the last closing of the zone, in seconds, and are calculated in 5 second intervals. The timers have a maximum value of 327675 seconds (91 hours) and won't count up beyond that. The timing of the zone timer updates is based on the configured zonetimerdumpflag parameter (defaults to every short poll).  
6. With SmartZoneTracking enabled, the node server keeps up with the state of zones (open, closed) instead of relying on the EnvisaLink's zone state tracking. States for bypassed zones show as "Unknown." If the feature is disabled, then the node server relies on the zone state tracking in the EnvisaLink. Either way, zone states are squirrely.
7. Bypass and SmartZoneTracking (probably) only work for zones in partition 1. The behavior for zones in other partitions is undefined.

For discussion and feedback, please use the ISY Forums here: https://forum.universal-devices.com/forum/390-envisalink-hw/.

### Version History
3.0.3 - Initial Release (2022-09-28)
- Converted envisalink-polyglotv2 to PG3 API
- Refactored nodeserver startup and node creation to account for misordering of node creation
- removed "Alarming" zone state and added "Last Alarm Zone" to Partition node instead
- changed partition states and arming commands to be Vista specific
- fixed alternating between NOT-READY and ALARM-IN-MEMORY states over consecutive keypad updates
- added version number passed in poly.start() and removed server.json
- fixed processing of stop() events to properly shutdown nodeserver
