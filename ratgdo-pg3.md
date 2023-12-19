# ratgdo
A plugin for PG3/PG3x that allows IoX to access and control the functions of Liftmaster and Chamberlain garage door openers as well as basic support for other brands which use dry contacts to control the door. See https://paulwieland.github.io/ratgdo/ for general information on the ratgdo device, ordering, flashin, installation, and configuration instructions. For support and feedback on the plugin itself, please use the UD Forums here.

### Instructions for use:

1. Once your ratgdo device(s) are installed and connected to your LAN (Wifi), follow the [Device configuration](#config) instructions below to configure the device(s) to use MQTT to connect to the Polisy/eISY MQTT broker (or an alternate MQTT broker).
2. From the Polyglot V3 Dashboard, install the ratgdo plugin from the Plugins Store.
3. Once the plugin is running, check the notifications in the Polyglot V3 Dashboard to see if there are any problems connecting with the local MQTT broker.
4. If there were no notifications from the plugin, click the "Discover" button in the Polyglot V3 Dashboard for the plugin to discover and load nodes for the garage door openers with connected ratgdo devices.

### Notes for latest version (v3.0.1):
1. ***NOTE*** This version is PRERELEASE and has only been tested with a dry contact (Genie Pro Screw Drive) garage door opener with minimal changes to the default ratgdo configuration.
2. This plugin requires at least version 3.1.20 of PG3/PG3x and version 5.6.2 of IoX (in order to ensure the local MQTT broker is installed).
3. Currently, the MQTT Topic Prefix must be set to "ratgdons/" for the plugin to work correctly. The Home Assistant (HA) discovery process should still allow these devices to be loaded into HA with this topic prefix set. I am considering removing this requirement in future versions to add additional flexibility.
4. Currently it appears that ratgdo firmware provides the same configuration information in discovery (door, light, and obstruction nodes) whether the ratgdo can sense or control all these aspects of your GDO or not. Therefore, the plugin adds a garage door opener node and a light node for each discovered ratgdo device. The light node may simply display "Unknown" for light status and the light commands may not be functional. Similarly, the obstruction and lock states of the garage door opener node may read "Unknown" and the corresponding commands may not work. Hopefully, the ratgdo firmware can be updated to reflect actual sense/control capabilities based on the "Garage Door Control Protocol" configuration setting in the future. In the meantime, you can simply delete the light node after discovery if doesn't function and you don't want it.
5. The plugin supports use of an alternate MQTT broker other than the local (Polisy/eISY) MQTT broker, but this capability has not been tested. To use an alternate MQTT broker, add or modify the following optional Custom Configuration Parameters under Configuration: 
- key: mqtthost, value: hostname for alternate MQTT broker (optional - defaults to "localhost")
- key: mqttport, value: port number for alternate MQTT broker (optional - defaults to 1884)
- key: mqttclientid, value: client ID for connecting to alternate MQTT broker (optional - defaults to "RATGDONS")
- key: username, value: username for authenticating with alternate MQTT broker (optional)
- key: password, value: password for authenticating with alternate MQTT broker (optional)
6. If you have changed the HA dicovery topic prefix in your HA  configuration and want to use this plugin for IoX, then you can set the discovery topic prefix for HA discovery using the following optional Custom Configuration Parameter: 
- key: discoverytopicprefix, value: string topic prefix for HA Discovery Prefix (optional - defaults to "homeassistant")

### <a name="config">Device configuration:</a>
The following configuration changes must be made to each ratgdo device on your LAN for it to work properly with the plugin. These configuration changes can be made through the local configuration web page on the ratgdo device (i.e., http://<ratgdo IP address>). Note that the ratgdo configuration page will require you to set an "OTA & Web Config Password" upon initial configuration. Make sure  you don't forget this password! In order to gain subsequent access to the configuration page, you must authenticate with a username of the ratgdo "Device Name" and the "OTA & Web Config Password" that you set. If you forget the password, you will have to reflash the device and start again.
Configuration settings that must be changed/verified comprise:
   * Make sure the "Enable MQTT" checkbox is checked.
   * Set the "MQTT server IP" to the IP address of your Polisy or eISY (unless using an alternate MQTT broker).
   * Set the "MQTT server port" to 1884 (unless using an alternate MQTT broker).
   * MQTT server username and password need not be changed if using the default Polisy or eISY broker.
   * Set the "MQTT Topic Prefix" to "ratgdons/" (including the trailing forward slash).
   * Leave the "Home Assistant Discovery Prefix" set to "homeassistant" unless you changed it for your configuration - see discussion of the "discoverytopicprefix" Custom Configuration Parameter above.

### Version history:
3.0.1 - Pre-release (2023-12-20)
