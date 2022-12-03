# VenstarCT
A NodeServer for Polyglot v3 that interfaces to Venstar Wi-Fi thermostats, including the ColorTouch and Explorer series, through a local API. See https://venstar.com/thermostats/ for more information on compatible thermostats.

### Instructions for installation of node server:
From the Polyglot Dashboard:
1. Install the VenstarCT node server from the Polyglot Node Server Store.
2. Give the node server a minute to start.
3. Make sure your Venstar ColorTouch thermostat(s) are powered on, unlocked, and on the same local network subnet as your Polisy. 
4. From the Polyglot Dashboard, select "Details" and then click the "DISCOVER" button to load nodes for each thermostat found on the network. THIS PROCESS WILL TAKE AT LEAST 10 SECONDS, depending on the number of thermostats you have, so please be patient before retrying. Also, please check the Polyglot Dashboard for messages regarding Discover failure conditions. Alternatively, for any thermostats not found (e.g., not on the same LAN subnet but reachable from your Polisy), you can add a locally accessible hostname or IP address of the thermostat to the "hostname" Custom Configuration Parameters under "Configuration," and then rerun the Discover process. Multiple hostnames can be entered, separated by semicolons (;). Note that any existing, working thermostats in the nodeserver need not be specified again.

### Notes for latest version (v3.0.11)
1. Currently only residential ColorTouch (T5800, T7800, T7850, and T7900) and Explorer (T2000, T2100, T3700, T3800, T3890, T3950) thermostats are supported.
2. Be sure to check if your thermostat requires the "Local API" setting to be enabled before executing discovery.
3. When Thermostat nodes are initially added in discovery, it may take several minutes (at least one longpoll interval) before all state values are updated.
4. The nodeserver relies on polling of the Venstar thermostats, so there may be some latency (up to shortPoll seconds) for reflections of changes in state.
5. All thermostats will get at least one child node for a sensor labeled "Thermostat." If you don't have any other remote sensors, this is redundant, and it will be removed in a future version.
6. If a thermostat control rule is violated, like setting the heat and cool setpoint too close to each other in Auto mode or trying to change fan mode or setpoints when in Away mode, a warning is logged in the log and the command is ignored. There is currently no way to send such warning or error messages back to the ISY.
7. The reported rutimes rollover a configured time (currently 9 minutes) after midnight. This is to allow you to snapshot the runtimes at midnight (or even a couple minutes after to account for longpoll interval) for the present day (e.g., if you are aggregating over some longer period) even if the times on the Polisy and on the thermostat are not exactly synchornized. 

For discussion and feedback, please use the ISY Forums here: https://forum.universal-devices.com/forum/393-venstarct/.

### Version History
Current release:
3.0.11 - More bug fixes (2022-12-03)
- fixup UOM for heat/cool state in node creation to account for PG3 quirk

Previous releases:
3.0.10 - Bug fixes (2022-11-28)
- changed UOM for heat/cool state to ISY Index (25)

3.0.9 - Bug fixes (2022-10-16)
- fixed runtimes and added rollover delay
- fixed profile editors to show values in condition builder

3.0.8 - Bug fixes and new features (2022-10-15)
- Fixed heat/cool state
- changed mode change processing to account for away mode changes
- added humidifier set point and active state
- added runtimes 

3.0.7 - bug fix (2022-10-07)
- Fixed load of existing sensor nodes

3.0.6 - Testing release (2022-10-06)
- Fixed dependencies in requirements.txt
- Added dump of runtimes to log in longpoll

3.0.5 - Initial release (2022-10-04)
- Converted VenstarCT Polyglot v2 node server to PG3 API
- Fixed up reporting of schedule modes (parts)
