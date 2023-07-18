# iopool
A Node Server for Polyglot v3 (PG3) that interfaces with iopool public API to allow IoX to access iopool EcO pool monitors. See <a href="https://iopool.com/pages/pool-monitor" target="_blank">iopool.com</a> for more information on compatible devices.

### Instructions for PG3 installation:

From the Polyglot Dashboard:
1. Install the iopool node server from the Polyglot Node Server Store.
2. Add/modify the following Custom Configuration Parameter under Configuration (note that the key should automatically be added for required parameters):
    
    - key: apiKey, value: API Key from iopool mobile app (go to More->Settings to see your API Key)(required)
    
    Once the required configuration parameter has been saved, the node server will attempt setup a connection to the iopool API. Check the Polyglot Dashboard for notices regarding bad configuration parameters (i.e., bad API Key value) and any connection failure conditions.
3. After a connection is estalished (a couple of seconds), if no errors have occurred, click the "Discover" button in the Polyglot Dashboard for the node server to discover and load Pool nodes for the pools (EcO monitors) configured in your mobile app account.

### Notes for latest version (v3.0.1)

1. While the folks at iopool have stated their polling limit is once a second, the EcO monitor only appears to take readings every 15 minutes, so a low short polling value seems unnecessary. 
2. To delete a device, you must use the PG3 Dashboard to ensure the device does not reappear in the IoX Admin Console the next time that Polyglot and/or the IOPOOL node server are restarted.
3. The nodeserver will filter non-ASCII characters from the pool title (name), like accent or Kanji characters, before adding the node to IoX. You can rename the nodes in IoX as you like.

### Version History
v3.0.1 - Initial Release (2023-07-18)

For discussion and feedback, please use the UD Forums <a href="https://forum.universal-devices.com/forum/430-iopool/" target="_blank">here</a>.
