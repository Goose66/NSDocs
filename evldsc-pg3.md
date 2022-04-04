# EnvisaLink-DSC
A Nodeserver for Polyglot v3 (PG3) that interfaces with a DSC PowerSeries™ alarm panel through EnvisaLink™ EVL-3/4 and DUO™ adapaters from EyezOn. See http://www.eyezon.com/ for more information.

### Instructions for PG3 installation:
From the Polyglot Dashboard:
1. Install the EnvisaLink-DSC nodeserver from the Polyglot NodeServer Store.
2. Add/modify the following required Configuration Parameters under Configuration (note that the keys should automatically be added for required parameters):
    - key: hostname, value: locally accessible hostname or IP address of EnvisaLink or DUO device (e.g., "envisalink.local" or "192.168.1.145")
    - key: password, value: password for EnvisaLink device
    - key: usercode, value: user code for disarming alarm panel (e.g., "5555")
3. You can also add the following optional Configuration Parameters under Configuration:
    - key: numpartitions, value: number of partition nodes to generate (defaults to 1)
    - key: numzones, value: number of zone nodes to generate (defaults to 8)
    - key: numcmdouts, value: number of command output nodes to generate (defaults to 2)
    - key: disablewatchdog, value: 0 or 1 for whether EyezOn cloud service watchdog timer should be disabled (defaults to 0 - not disabled)
    - key: zonetimerdumpflag, value: numeric flag indicating whether dumping of the zone timers should be done on shortpoll (1), longpoll (2), or disabled altogether (0) (defaults to 1 - shortpoll)

Once the required configuration parameters have been saved, the nodeserver will attempt to connect to the EnvisaLink device and then update the panel, partition, and zone states. This will take a couple of minutes depending on the shortpoll interval, so please be patient. Check the Polyglot Dashboard for notices regarding bad configuration parameters and connection failure conditions.

### Using the nodeserver events (commands)
The nodes of the EnvisaLink-DSC Nodeserver generate the following incoming commands to the ISY, allowing the nodes to be added as controllers to scenes and the commands to be used as triggers in programs:

ZONE
- Sends a *DON* ("Opened") command when the zone is opened
- Sends a *DOF* ("Closed") command when the zone is closed

PARTITION
- Sends a *DON* ("Alarm Triggered") command when the partition is alarming
- Sends a *DOF* ("Alarm Restored") command when the partition is disarmed

COMMAND_OUTPUT
- Sends a *DON* ("Activate Output") command when the command output is activated

PANEL
- Sends a *DON* ("Alarm Triggered") command when a smoke/panic alarm is activated
- Sends a *DOF* ("Alarm Restored") command when an active smoke/panic alarm is cleared
- Sends an *AWAKE* ("Heartbeat") command periodically for heartbeat monitoring

### Notes for latest version (v3.0.8)

1. Initially, there are several state values that are unknown when the nodeserver starts and will default to 0 (or last known value if restarted). This includes trouble states, door chime, and the like. These state values may not be correct until the status is changed while the nodeserver is running.
2. The initial connection to the EnvisaLink and alarm panel may not be made until a couple of short polls have occurred (e.g., 30 or 60 seconds after start). The various state values (zone states, zone bypass, zone timers, etc.) are updated over subsequent short polls. Therefore, depending on the "shortPoll" configuration setting and the number of partitions, it may take a few minutes after starting/restarting the nodeserver for all the states to be updated.
3. If the connection to the EnvisaLink is lost, or if the nodeserver doesn't hear from the EnvisaLink for 10 minutes (including the expected four-minute keepalive), then the connection is reset and the nodeserver will attempt reconnect on the next short poll and every subsequent short poll until connection is reestablished or the nodeserver is shutdown.
4. Any change to "disablewatchdog" or "zonetimerdumpflag" parameters while the nodeserver is running should take effect immediately (i.e. at next time the functions are set to run). Any change to the "hostname," "password," or "usercode" parameters while the nodeserver is running will be used on the next conenction attempt to the EnvisaLink, whether the initial connection or a reconnect. If you change the "numpartitions," "numzones," or "numcmdouts" parameters after the AlarmPanel node has been created, you must restart the nodeserver for these new parameter values to take effect.
5. The nodeserver sends an AWAKE command (heartbeat) to the Alarm Panel node every four minutes (when the keepalive is received from the alarm panel). You can check for this in a program on the ISY to monitor the connection. There is also an "Alarm Panel Connected" driver value that reflects whether the connection to the EnvisaLink/alarm panel is active, but this may not get updated if the nodeserver fails.
6. The command output nodes are limited to partition 1 only. These Command Output nodes send *DON* commands, but not *DOF* commands.
7. If your EnvisaLink is firewalled and can not connect to the EyezOn web service, then the EnvisaLink will reboot every 20 minutes ("Watchdog Timer") in order to try and reestablish the connection to the web service. This will kill the connection to the nodeserver as well and it will (attempt to) reconnect on the next short poll. If you set the "diablewatchdog" configuration setting to 1, the nodeserver will send a periodic poll to the EnvisaLink to reset the Watchdog Timer so that the EnvisaLink won't reboot. The poll is sent every long poll if the "diablewatchdog" configuration parameter is set, so the "longpoll" configuration setting needs to be less than 1200 seconds (20 minutes).
8. During the intial connecting and status reporting on startup, the nodeserver sends keystrokes to the keypad to dump bypass zones to set the initial Zone Bypassed states. This may cause the status LEDs on the keypad(s) to blink briefly and a Security Event alert (text and/or email) to be generated by EyezON.
9. The zone timers ("Time Closed") represent the time since the last closing of the zone, in seconds, and are calculated in 5 second intervals. The timers have a maximum value of 327675 seconds (91 hours) and won't count up beyond that. The timing of the zone timer updates is based on the configured zonetimerdumpflag parameter (defaults to every short poll).  
10. The reporting of trouble states through EnvsiaLink's TPI doesn't seem to align exactly with the description of the various trouble states in the documentation for the DSC panels. In addition, depending on how your panel is programmed, the panel may not send trouble reporting commands for certain conditions (e.g., AC power out). The nodeserver updates the trouble driver values for the Alarm Panel node from both specific trouble reporting commands from the EnvsiaLink and the state of keypad LEDs for partition 1.
11. Due to the way zone bypass flags are updated by the DSC system, zones may require up to two shortpoll intervals (e.g., 60 seconds) for their Zone Bypassed states to reset to False after a partition is disarmed and returns to the ready state.

For discussion and feedback, please use the ISY Forums here: https://forum.universal-devices.com/forum/340-envisalink-dsc/.

### Version History
v3.0.8 - Initial Release (2022-03-15)
- Converted to PG3 API
- Refactored code to be more readable and better structured
- Added Toggle Bypass command to zone nodes with improved bypass update functionality
- Added better error handling in API module
- Fixed state updates after Key Alarm (Fire/Auxilary/Police) restoral
