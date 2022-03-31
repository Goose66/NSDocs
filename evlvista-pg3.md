# evlvista-pg3
A node server for Polyglot v3 (PG3) that interfaces with a Honeywell Vista series alarm panel through EnvisaLink™ EVL-3/4 and DUO™ adapaters from EyezOn. See http://www.eyezon.com/ for more information.

### Instructions for PG3 installation:
From the Polyglot Dashboard:
1. Install the EnvisaLink-Vista node server from the Polyglot Node Server Store.
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
    
Once the required configuration parameters have been saved, the node server will attempt to connect to the EnvisaLink device and then update the panel, partition, and zone states. This will take a couple of minutes depending on the shortpoll interval, so please be patient. Check the Polyglot Dashboard for notices regarding bad configuration parameters and connection failure conditions.

### Using the node server events (commands)
The nodes of the EnvisaLink-Vista Node Server generate the following incoming commands to the ISY, allowing the nodes to be added as controllers to scenes and the commands to be used as triggers in programs:

ZONE
- Sends a *DON* ("Opened") command when the zone is opened
- Sends a *DOF* ("Closed") command when the zone is closed

PARTITION
- Sends a *DON* ("Alarm Triggered") command when the partition is alarming
- Sends a *DOF* ("Alarm Restored") command when the partition is disarmed

PANEL
- Sends an *AWAKE* ("Heartbeat") command periodically for heartbeat monitoring

### Notes for latest version (v3.0.3)

1. Initially, there are several state values that are unknown when the node server starts and will default to 0 (or last known value if restarted). This includes trouble states, door chime, and the like. These state values may not be correct until the status is changed while the node server is running.
2. The initial connection to the EnvisaLink and alarm panel may not be made until a couple of short polls have occurred (e.g., 30 or 60 seconds after start). The various state values (zone states, zone bypass, zone timers, etc.) are updated over subsequent short polls. Therefore, depending on the "shortPoll" configuration setting and the number of partitions, it may take a few minutes after starting/restarting the node server for all the states to be updated.
3. If the connection to the EnvisaLink is lost, or if the node server doesn't hear from the EnvisaLink for 10 minutes (including the expected four-minute keepalive), then the connection is reset and the node server will attempt reconnect on the next short poll and every subsequent short poll until connection is reestablished or the node server is shutdown.
4. Any change to "disablewatchdog" or "zonetimerdumpflag" parameters while the node server is running should take effect immediately (i.e. at next time the functions are set to run). Any change to the "hostname," "password," or "usercode" parameters while the node server is running will be used on the next conenction attempt to the EnvisaLink, whether the initial connection or a reconnect. If you change the "numpartitions" or "numzones" parameters after the AlarmPanel node has been created, you must restart the node server for these new parameter values to take effect.
7. If your EnvisaLink is firewalled and can not connect to the EyezOn web service, then the EnvisaLink will reboot every 20 minutes ("Watchdog Timer") in order to try and reestablish the connection to the web service. This will kill the connection to the node server as well and it will (attempt to) reconnect on the next short poll. If you set the "diablewatchdog" configuration setting to 1, the node server will send a periodic poll to the EnvisaLink to reset the Watchdog Timer so that the EnvisaLink won't reboot. The poll is sent every long poll if the "diablewatchdog" configuration parameter is set, so the "longpoll" configuration setting needs to be less than 1200 seconds (20 minutes).
8. During the intial connecting and status reporting on startup, the node server sends keystrokes to the keypad to dump bypass zones to set the initial Zone Bypassed states. This may cause the status LEDs on the keypad(s) to blink briefly and a Security Event alert (text and/or email) to be generated by EyezON.
9. The zone timers ("Time Closed") represent the time since the last closing of the zone, in seconds, and are calculated in 5 second intervals. The timers have a maximum value of 327675 seconds (91 hours) and won't count up beyond that. The timing of the zone timer updates is based on the configured zonetimerdumpflag parameter (defaults to every short poll).  
11. Due to the way zone bypass flags are updated by the DSC system, zones may require up to two shortpoll intervals (e.g., 60 seconds) for their Zone Bypassed states to reset to False after a partition is disarmed and returns to the ready state.
2. With SmartZoneTracking enabled, the node server keeps up with the state of zones (open, closed) instead of relying on the EnvisaLink's zone state tracking. States for bypassed zones show as "Unknown." If the feature is disabled, then the node server relies on the zone state tracking in the EnvisaLink. Either way, zone states are squirrely.
3. Bypass and SmartZoneTracking (probably) only work for zones in partition 1. The behavior for zones in other partitions is undefined.5. The node server sends an AWAKE command (heartbeat) to the Alarm Panel node every four minutes (when the keepalive is received from the alarm panel). You can check for this in a program on the ISY to monitor the connection. There is also an "Alarm Panel Connected" driver value that reflects whether the connection to the EnvisaLink/alarm panel is active, but this may not get updated if the node server fails.

For discussion and feedback, please use the ISY Forums here: https://forum.universal-devices.com/forum/340-envisalink-vista/.

### Version History
v3.0.8 - Initial Release (2022-03-15)
- Converted to PG3 API
- Refactored code to be more readable and better structured
- Added Toggle Bypass command to zone nodes with improved bypass update functionality
- Added better error handling in API module
- Fixed state updates after Key Alarm (Fire/Auxilary/Police) restoral
