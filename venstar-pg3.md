# VenstarCT
A NodeServer for Polyglot v3 that interfaces to the Venstar ColorTouch series of thremostats through a local API. See https://venstar.com/thermostats/colortouch/ for more information on thermostats.

### Instructions for installation of node server:
From the Polyglot Dashboard:
1. Install the VenstarCT node server from the Polyglot Node Server Store.
2. Give the node server a minute to start.
3. Make sure your Venstar ColorTouch thermostat(s) are powered on, unlocked, and on the same local network subnet as your Polisy. 
4. From the Polyglot Dashboard, select "Details" and then click the "DISCOVER" button to load nodes for each thermostat found on the network. THIS PROCESS WILL TAKE AT LEAST 10 SECONDS, depending on the number of thermostats you have, so please be patient before retrying. Also, please check the Polyglot Dashboard for messages regarding Discover failure conditions. Alternatively, for any thermostats not found (e.g., not on the same LAN subnet but reachable from your Polisy), you can add a locally accessible hostname or IP address of the thermostat to the "hostname" Custom Configuration Parameters under "Configuration," and then rerun the Discover process. Multiple hostnames can be entered, separated by semicolons (;). Note that any existing, working thermostats in the nodeserver need not be specified again.

### Notes for latest version (v3.0.5)
1. Currently only residential ColorTouch thermostats (T7800, T7850, and T7900) are supported.
2. The nodeserver relies on polling of the Venstar ColorTouch thermostats, so there may be some latency (up to shortPoll seconds) for reflections of changes in state.
3. All thermostats will get at least one child node for a sensor labeled "Thermostat." If you don't have any other remote sensors, this is redundant, and it will be removed in a future version.
4. If a thermostat control rule is violated, like setting the heat and cool setpoint too close to each other in Auto mode or trying to change fan mode or setpoints when in Away mode, a warning is logged in the log and the command is ignored. There is currently no way to send such warning or error messages back to the ISY.
5. Once added takes long time

For discussion and feedback, please use the ISY Forums here: https://forum.universal-devices.com/forum/340-venstarct/.

### Version History
Current release:
3.0.5 - Initial Release (2022-09-28)
- Converted VenstarCT Polyglot v2 node server to PG3 API
- Fixed up reporting of schedule modes (parts)
