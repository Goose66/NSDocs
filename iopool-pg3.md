# iopool
A Node Server for Polyglot v3 (PG3) that interfaces with iopool public API to allow IoX to access iopool EcO pool monitors. See <a href="https://iopool.com/pages/pool-monitor" target="_blank">iopool.com</a> for more information on compatible devices.

### Instructions for PG3 installation:

From the Polyglot Dashboard:
1. Install the iopool node server from the Polyglot Node Server Store.
2. Add/modify the following Custom Configuration Parameter under Configuration (note that the key should automatically be added for required parameters):
    
    - key: apiKey, value: API Key from iopool mobile app (go to More->Settings in the mobile app to see your API Key)(required)
    - key: tempUnit, value: "F" for Farenheit or "C" for Celcius temperature reporting (optional - defaults to "C") 
    
    Once the required configuration parameter has been saved, the node server will attempt setup a connection to the iopool API. Check the Polyglot Dashboard for notices regarding bad configuration parameters (i.e., bad API Key value) and any connection failure conditions.
3. After a connection is estalished (about 15 seconds), if no errors have occurred, click the "Discover" button in the Polyglot Dashboard for the node server to discover and load Pool nodes for the pools (EcO monitors) configured in your mobile app account.

### Notes for latest version (v3.1.2)

1. The "tempUnit" Custom Configuration Parameter allows the temperature reported to IoX to be in Farenheit or Celcius. The EcO monitors always report in Celcius, regardless of how you set the temp unit in the mobile app.
2. A "Node Server Status" state (driver) value was added to the Pool nodes. However, to get this to work in existing implementation, you need to delete your current Pool nodes (using the PG3 Dasboard - see # 4 below), restart the node server, then rediscover your devices using the "Discover" button. Note that this only works for the first Pool configured in your iopools account. All others will report "Not Tracked" for "Node Server Status."
3. While the folks at iopool have stated their polling limit is once a second, the EcO monitor only appears to take readings every 15 minutes, so a low short polling value seems unnecessary. 
4. To delete a device, you must use the PG3 Dashboard to ensure the device does not reappear in the IoX Admin Console the next time that Polyglot and/or the IOPOOL node server are restarted.
5. The nodeserver will filter non-ASCII characters from the pool title (name), like accent or Kanji characters, before adding the node to IoX. You can rename the nodes in IoX as you like.
6. If the node server is stopped or fails, the "Node Server Status" (for the first Pool node) will changed to "Disconnected" or "Failed," respectively. If the connection to the iopool service fails (or your PolISY/eISY loses it's Internet Connection), the "Node Server Status" (again for the first Pool node) will state "Connected" but the "Operating Mode" state will change to "Unknown" and teh "Time Since Last Valid Measurement" will count up past 15 minutes. You can use these facts for adding programs to monitor the status of the node server.

### Version History
3.1.2 - Added Features
- added temperature unit parameter
- added Node Server Status to pool node

3.0.1 - Initial Release (2023-07-22)

For discussion and feedback, please use the UD Forums <a href="https://forum.universal-devices.com/forum/430-iopool/" target="_blank">here</a>.
