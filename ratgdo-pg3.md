# ratgdo
A plugin for PG3/PG3x that allows IoX to access and control the functions of Liftmaster and Chamberlain garage door openers (GDOs) as well as basic support for other brands which use dry contacts to control the door. See https://paulwieland.github.io/ratgdo/ for general information on the ratgdo device, ordering, flashing, installation, and configuration instructions. For support and feedback on the plugin itself, please use the UD Forums [here](https://forum.universal-devices.com/forum/436-ratgdo/).

### Instructions for use:

1. Once your ratgdo device(s) are installed and connected to your LAN (Wifi), follow the [Device configuration](#config) instructions below to configure the device(s) to use MQTT to connect to the built-in Polisy/eISY MQTT broker (or select an alternate MQTT broker).
2. From the Polyglot V3 Dashboard, install the ratgdo plugin from the Plugins Store.
3. Once the plugin is running, check the notifications in the Polyglot V3 Dashboard to see if there are any problems connecting with the local MQTT broker.
4. If there were no notifications from the plugin, click the "Discover" button in the Polyglot V3 Dashboard for the plugin to discover and load nodes for the garage door openers with connected ratgdo devices.

### <a name="config">Device configuration:</a>
NOTE: YOUR ratgdo DEVICE(S) MUST BE FLASHED WITH THE APPROPRIATE MQTT FIRMWARE TO BE USED WITH THIS PLUGIN. See the "MQTT" section of the ratgdo Firmware page at https://paulwieland.github.io/ratgdo/flash.html to flash the devices. After flashing your device with the MQTT firmware and connecting the device to your Wi-fi, you must make the following configuration changes to each ratgdo device for it to work properly with the plugin. If you are unable to access the configuration web page on your device from the "View Device" link at the end of the firmware flashing process, you can access the configuration web page in a new browser window at http://\<ratgdo IP address\>. Note that the ratgdo configuration page will require you to set an "OTA & Web Config Password" upon initial configuration. Make sure  you don't forget this password! In order to gain subsequent access to the configuration page, you must authenticate with a username of the ratgdo "Device Name" and the "OTA & Web Config Password" that you set. If you forget the password, you will have to reflash the device and start again.
Configuration settings that must be changed/verified comprise:
   * Make sure the "Enable MQTT" checkbox is checked.
   * Set the "MQTT server IP" to the IP address of your Polisy or eISY (unless using an alternate MQTT broker). NOTE FQDN (hostname) is not supported.
   * Set the "MQTT server port" to 1884 (unless using an alternate MQTT broker).
   * MQTT server username and password need not be changed if using the default Polisy or eISY broker.
   * Leave the "Home Assistant Discovery Prefix" set to "homeassistant" unless you changed it for your configuration - see discussion of the "discoverytopicprefix" Custom Configuration Parameter in notes below.

### Notes for latest version (v3.2.5):
1. This plugin requires at least version 3.1.20 of PG3/PG3x and version 5.6.2 of IoX (in order to ensure the local MQTT broker is installed).
2. The GDO nodes generate "Open" (DON) commands when the door status changes from "Closed" to "Open" or "Opening", and "Close" (DOF) commands when the status changes from anything to "Closed." The Light nodes similarly generate "On" (DON) and "Off" (DOF) commands when the light status switches to "On" or "Off," respectively. These commands are only generated if the status change results from an external control signal (i.e., not a command from IoX through the plugin).
3. The GDO nodes generate a "Motion" (DON3) command when motion is detected. The "Motion" state is also changed to "Detected". The plugin will change the "Motion" state back to "Clear" 60 seconds after the last motion detection event.
4. The Discovery process waits 5 seconds for all HA discovery messages to be processed from the MQTT broker and it may take another several seconds to process the retained states while the new nodes are added. Accordingly, after you click the "Discover" button, please wait 15+ seconds for the nodes to be created in IoX and for the initial state values to be processed before trying again or logging an error.
5. The  plugin will discover your devices based on the Home Assistant (HA) discovery process, including the base MQTT topic ("topic prefix") for status and command messages. If you want to set a topic prefix for your ratgdo devices to support other environments, do so before performing the Discovery process, or you will have to delete your nodes and rediscover them after making any such changes.
6. Version of the ratgdo firmware earlier than 2.5.8 provide the same configuration information in discovery (cover, light, and binary_sensor (obstruction) components) whether the ratgdo can sense or control all these aspects of your GDO or not. Therefore, the plugin may add a garage door opener node AND a light node for each discovered ratgdo device. The light node may simply display "Unknown" for light status and the light commands may not be functional. Similarly, the obstruction, lock, and motion states of the garage door opener node may read "Unknown" and the corresponding commands may not work. A request has been made to the developer to update the firmware to return the "Garage Door Control Protocol" configuration setting, which will allow the plugin to more appropriately add or remove these non-working states. In the meantime, you can simply delete the light node (in the PG3 Dashboard) after discovery if it doesn't function and you don't want it.
7. Door state, Light state, Lock state, and Obstruction state should all work for Security + 2.0 doors. Motion state should work if you have a compatible wall control pad with a motion detector. Door state, Light State, and Lock state should work for Security + 1.0 doors. Only Door state currently works for "Dry Contact" mode doors.
8. The plugin supports use of an alternate MQTT broker other than the local (Polisy/eISY) MQTT broker. To use an alternate MQTT broker, add or modify the following optional Custom Configuration Parameters under Configuration: 
- key: mqtthost, value: hostname for alternate MQTT broker (optional - defaults to "localhost")
- key: mqttport, value: port number for alternate MQTT broker (optional - defaults to 1884)
- key: mqttclientid, value: client ID for connecting to alternate MQTT broker (optional - defaults to "RATGDONS")
- key: username, value: username for authenticating with alternate MQTT broker (optional)
- key: password, value: password for authenticating with alternate MQTT broker (optional)
9. If you have changed the HA dicovery topic prefix in your HA  configuration and want to use this plugin for IoX, then you can set the discovery topic prefix for HA discovery using the following optional Custom Configuration Parameter: 
- key: discoverytopicprefix, value: string topic prefix for HA Discovery Prefix (optional - defaults to "homeassistant")
10. If upgrading your plugin from a version earlier than v3.1.4, you will need to delete your existing GDO and Light nodes (in the PG3 Dashboard) and then perform the Discovery process again. However, the node addresses of the nodes will remain the same, so any programs you have should continue to work without modification.
   
### Version history:
v3.2.5 - New Features (2024-01-31)
- GDO nodes have motion state 
- GDO nodes send DON3 ("Motion") command on motion "events"
- GDO motion state set to clear 60 seconds after last motion event
- Names added to driver list (in PG3 dashboard)
- Light nodes now child nodes of GDO nodes

v3.1.4 - Upgrades (2024-01-05)
- discovered devices subscribe to own topics eliminating some configuration
- GDO and Light nodes send DON and DOF commands in response to status change
- completed implementation of support for alternative MQTT brokers
  
v3.0.2 - Bug Fixes (2023-12-20)
- bug fixes from initial field testing
- changed door state driver to index w/ text labels

v3.0.1 - Pre-release (2023-12-19)
