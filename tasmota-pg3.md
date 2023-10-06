# Tasmota
A Node Server for Polyglot V3 that allows IoX to access and control Martin Jerry and other Tasmota switches and modules through MQTT.

### Instructions for use:

1. Make sure all of your Tasmota devices connected to the Wifi and configured to use MQTT on the Polisy/eISY MQTT broker (or an alternate MQTT broker). See below for more information on configuring individual devices.
2. From the Polyglot V3 Dashboard, install the Tasmota node server from the Node Server Store.
3. Add/modify the following optional Custom Configuration Parameter under Configuration: 
  - key: teleperiod, value: number of seconds for devices to report status (optional - defaults to 60)
  - key: autoconfigure, value: flag indicating whether node server should attempt to configure discovered devices (optional - defaults to 1 (enabled))
  - key: mqtthost, value: hostname for alternate MQTT broker (optional - defaults to "localhost")
  - key: mqttport, value: port number for alternate MQTT broker (optional - defaults to 1884)
  - key: mqttclientid, value: client ID for connecting to MQTT broker (optional - defaults to "TASMOTA_NS")
  - key: username, value: username for authenticating with alternate MQTT broker (optional)
  - key: password, value: password for authenticating with alternate MQTT broker (optional)
4. After a connection to the MQTT brokerr is estalished (about 15 seconds), if no errors have occurred, click the "Discover" button in the Polyglot V3 Dashboard for the node server to discover and load nodes for the Tasmota devices connected to the MQTT broker.

### Notes for latest version (v3.0.1):
1. Autoconfiguration of devices is not implemented (except for telemetry period).

### Device configuration:
* For all devices:
    - MQTT Configuration (unless you have an alternate MQTT broker):
        * Host = <ip address or hostname of Polisy/Eisy>
        * Port = 1884
        * Topic = <anything you want but the first 12 characters of the topic of each must be unique>
        * Full Topic = tasmota/%prefix%/%topic%/
    - Other Configuration:
        * Friendly Name 1 = <Name for device node in IoX>

* Martin Jerry US-SD-TC01 Dimmer:
    - Template: "NAME":"US-SD-TC01","GPIO":[1,1,1,1,1,1,0,0,1,1,1,1,1,0],"FLAG":0,"BASE":54}
    - Commands:
        * SetOption20 0
        * SetOption54 0

* Martin Jerry US-FC-01 Fan Controller:
    - Template={"NAME":"US-FC-01","GPIO":[1,1,1,1,1,1,0,0,1,1,1,1,1,0],"FLAG":0,"BASE":54}
    - Commands:
        * Rule1
        ON TuyaReceived#Data=55AA03070005030400010016 DO publish2 tasmota/stat/%topic%/STATE {"Speed": 0} ENDON
        ON TuyaReceived#Data=55AA03070005030400010117 DO publish2 tasmota/stat/%topic%/STATE {"Speed": 1} ENDON
        ON TuyaReceived#Data=55AA03070005030400010218 DO publish2 tasmota/stat/%topic%/STATE {"Speed": 2} ENDON
        ON TuyaReceived#Data=55AA03070005030400010319 DO publish2 tasmota/stat/%topic%/STATE {"Speed": 3} ENDON
        * Rule1 1

HumiditySwitch:
SetOption8 = <0 = C (default), 1 = F>

### Version history:
3.0.1 - Initial Release (2023-10-10)
- Supports:
    * Martin Jerry (MJ) MJ-S01 Switch
    * MJ Plug V by MJ (Plug-in  Module)
    * MJ US-SD-TC01 (Dimmer)
    * MJ US-FC-01 (Fan Controller)
    * MJ US-SS01 Switch
    * MJ US-SS02 Swtich (Humidity Switch)
