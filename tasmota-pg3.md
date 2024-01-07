# Tasmota
A Node Server for Polyglot V3 that allows IoX to access and control Martin Jerry and other Tasmota switches and modules through MQTT (see below for supported devices). For discussion of individual device configuration, new device support requests, and other feedback, please use the UD Forums here.

### Instructions for use:

1. Make sure all of your Tasmota devices are connected to your LAN and configured to use MQTT to connect to the Polisy/eISY MQTT broker (or an alternate MQTT broker). See below for more information on configuring individual devices.
2. From the Polyglot V3 Dashboard, install the Tasmota node server from the Node Server Store.
3. Once the node server is running, check the notifications in the Polyglot V3 Dashboard to see if there are any problems connecting with the local MQTT broker.
4. If there were no errors connecting to the MQTT broker, click the "Discover" button in the Polyglot V3 Dashboard for the node server to discover and load nodes for the Tasmota devices connected to the MQTT broker.

### Notes for latest version (v3.1.6):
1. The following devices are currently supported:
    * Martin Jerry (MJ) MJ-S01 Switch (Switch)
    * MJ Plug V by MJ (Plug-in  Module)
    * MJ US-SD-TC01 (Dimmer)
    * MJ US-FC-01 (Fan Controller)
    * MJ US-SS01 Switch (Switch)
    * MJ US-SS02 Switch (Humidity Switch)
    * Shelly 1 (Switch)
    * Sonoff Mini R2 (Switch)
    * Sonoff Mini R4 (Switch)
2. This node server requires at least version 3.1.23 of PG3 or PG3x (in order to ensure the local MQTT broker is installed).
3. Autoconfiguration of the devices (configuration beyond MQTT broker) may be turned on or off using the following optional Custom Configuration Parameter:
    * key: autoconfigure, value: flag indicating whether node server should attempt to configure discovered devices (optional - defaults to 1 (enabled))
NOTE: Autoconfiguration of devices is not yet implemented (except for telemetry period). Be sure to follow instructions below to configure your devices to operate properly with the node server. 
4. The telemetry period indicates the frequency that the Tasmota devices will report their state to the node server independent of state changes. If autoconfiguration is enabled, the telemetry period of each discovered device is set to 60 seconds. To adjust the telemetry period configured for discovered devices (e.g., to reduce network load), use the following optional Custom Configuration Parameter:
    * key: teleperiod, value: number of seconds for devices to report status (optional - defaults to 60)
5. The node server supports use of an alternate MQTT broker other than the local (Polisy/eISY) MQTT broker, but this capability has not been tested. To use an alternate MQTT broker, add or modify the following optional Custom Configuration Parameters under Configuration: 
    * key: mqtthost, value: hostname for alternate MQTT broker (optional - defaults to "localhost")
    * key: mqttport, value: port number for alternate MQTT broker (optional - defaults to 1884)
    * key: mqttclientid, value: client ID for connecting to MQTT broker (optional - defaults to "TASMOTA_NS")
    * key: username, value: username for authenticating with alternate MQTT broker (optional)
    * key: password, value: password for authenticating with alternate MQTT broker (optional)
6. If the MQTT configuration of your devices is already setup to support another home automation controller (e.g., Home Assistant) and you cannot use the default "tasmota/" topic prefix for your devices (see "Full Topic" configuration below), you can change the topic prefix that the plugin will look for with the following optional Custom Configuration Parameter:
    * key: mqtttopicprefix, value: topic prefix for topic subscriptions (optional)
The specified topic prefix value must match the prefix value in the full topic configured for the Tasmota devices. If no mqtttopicprefix parameter is specified, then the topic prefix defaults to "tasmota/". An empty topic prefix value is valid. If a non-empty topic prefix parameter is specified, it must include the trailing "/".

### Device configuration:
The following configuration changes must be made to each Tasmota device on your network for it to work properly with the node server. These configuration changes can be made through the "Configuration" section of the web user interface for each Tasmota device.
- For all devices:
   * Under "Configure MQTT" (unless you have an alternate MQTT broker):
      - Host = <ip address or hostname of Polisy/eISY>
      - Port = 1884
      - Topic = <can leave as-is but the first 12 characters of the topic of each device must be unique>
      - Full Topic = tasmota/%prefix%/%topic%/
   * Under "Configure Other":
      - Friendly Name 1 = <name for device in IoX>

- Martin Jerry US-SD-TC01 Dimmer:
   * Under "Configure Other":
     - Template: {"NAME":"US-SD-TC01","GPIO":[1,1,1,1,1,1,0,0,1,1,1,1,1,0],"FLAG":0,"BASE":54}
        (paste into "Template" field and select "Activate" checkbox)
   * Using the "Console", enter the following commands:
      - SetOption20 0 (when setting Dimmer state, also turn power on)
      - SetOption54 0 (prevent Tuya MCU from sending power state)

- Martin Jerry US-FC-01 Fan Controller:
   * Under "Configure Other":
      - Template: {"NAME":"US-FC-01","GPIO":[1,1,1,1,1,1,0,0,1,1,1,1,1,0],"FLAG":0,"BASE":54}
        (paste into "Template" field and select "Activate" checkbox)
   * Using the "Console", enter the following commands:
      - Rule1 ON TuyaReceived#Data=55AA03070005030400010016 DO publish2 tasmota/stat/%topic%/STATE {"Speed": 0} ENDON
            ON TuyaReceived#Data=55AA03070005030400010117 DO publish2 tasmota/stat/%topic%/STATE {"Speed": 1} ENDON
            ON TuyaReceived#Data=55AA03070005030400010218 DO publish2 tasmota/stat/%topic%/STATE {"Speed": 2} ENDON
            ON TuyaReceived#Data=55AA03070005030400010319 DO publish2 tasmota/stat/%topic%/STATE {"Speed": 3} ENDON
      - Rule1 1

- Martin Jerry US-SS02 Humidity Switch:
   * Using the "Console", enter the following commands:
      - SetOption8 = <0 = C (default), 1 = F> (sets your desired temp unit reporting)

- Shelly 1
   * Under "Configure Other":
      - Template: {"NAME":"Shelly 1","GPIO":[1,1,0,1,224,192,0,0,0,0,0,0,0,0],"FLAG":0,"BASE":46} 
        (paste into "Template" field and select "Activate" checkbox)

- Sonoff Mini R2
   * Under "Configure Other":
      - Template: {"NAME":"Sonoff MINIR2","GPIO":[17,0,0,0,9,0,0,0,21,157,0,0,0],"FLAG":0,"BASE":1}
        (paste into "Template" field and select "Activate" checkbox)

- Sonoff Mini Extreme R4
   * Under "Configure Other":
      - Template: {"NAME":"Sonoff MINIR4","GPIO":[32,0,0,0,0,0,0,0,0,0,0,0,0,0,0,576,0,0,0,0,0,0,224,160,0,0,0,0,0,0,0,0,0,0,0,0],"FLAG":0,"BASE":1}
        (paste into "Template" field and select "Activate" checkbox)

### Version history:
3.1.6 - New Devices and Reliability Enhancements (2024-01-07)
- Added support for:
    * Sonoff Mini R2 (Switch)
    * Sonoff Mini R4 (Switch)
- Reworked override of MQTT topic prefix
- Added code for beta testing support of seeedstudio HP sensor (HP Sensor)
- Added check for correct full topic configuration in discovery
- Added check for connection and reconnection attempt in shortpoll

3.0.4 - Initial Release (2023-10-10)
- Added support for:
    * Martin Jerry (MJ) MJ-S01 Switch
    * MJ Plug V by MJ (Plug-in  Module)
    * MJ US-SD-TC01 (Dimmer)
    * MJ US-FC-01 (Fan Controller)
    * MJ US-SS01 Switch
    * MJ US-SS02 Switch (Humidity Switch)
    * Shelly 1 (Switch Module)
