# Schlage
A Node Server for Polyglot v3 (PG3) that interfaces with Schlage cloud service to allow the IoX to control Schlage Encode WiFi locks and Schlage Sense locks with Sense WiFi Adapter. See <a href="https://www.schlage.com/en/home/products/products-smart-locks/schlage-encode/wifi-smart-lock-listing.html" target="_blank">WiFi Smart Locks | Schlage Encode</a> for compatible devices.

### Instructions for PG3 installation:

From the Polyglot Dashboard:
1. Install the Schlage node server from the Polyglot Node Server Store.
2. Add/modify the following Custom Configuration Parameters under Configuration (note that the keys should automatically be added for required parameters):
    
    - key: username, value: username (email address) for Schlage cloud account (required)
    - key: password, value: password for Schlage cloud account (required)
    
    Once the required configuration parameters have been saved, the node server will attempt to connect to the Schlage service. This may take a couple of minutes, so be patient.  Check the Polyglot Dashboard for notices regarding bad configuration parameters and connection failure conditions.
3. After a couple of minutes, if no errors have occurred, the node server should be connected to the Schlage cloud account. Click the "Discover" button in the Polyglot Dashboard for the node server to discover and load Lock nodes for the locks configured in your account.

### Notes for latest version (v3.0.1)

1. Please don't set the shortPoll value to an extremely low value, e.g. < 10 seconds. Repeated, frequent polling of the Schlage cloud service may result in Schlage making moves to block access by the node server.
2. On the first start of the node server, the node server does not attempt to connect and login to the Schlage cloud service until the first longpoll after the configuration parameters are entered and saved (log message: "(Re)establishing Schlage connection in longPoll()"). That means it may take up to 60 seconds before a connection is attempted after entering your credentials. In addition, the node server will continue to attempt to connect and login every longpoll (e.g., every 60 seconds) until a connection is established, so watch your Polyglot Dashboard messages for connection errors or bad credentials to avoid locking out your account.
3. When the node server is restarted (e.g., manually in Dashboard or after failure/reboot), the node server will immediately attempt a connection to the Schlage cloud service and then retry the connection attemp in every subsequent longpoll (e.g., every 60 seconds) until a connection is established. This is done to allow network components to reestablish connections when recovering from a power failure. 
7. To delete a device, you must use the PG3 Dashboard to ensure the device does not reappear in the IoX Admin Console the next time that Polyglot and/or the Schlage node server are restarted.
8. The nodeserver will filter non-ASCII characters from the device names, like accent or Kanji characters, before adding the node to IoX. You can rename the nodes in IoX as you like.
9. Because of the longer than normal polling period (20 seconds), you may want to structure your programs in IoX to perform a Force Update on a lock node before other programs that depend on the lock status are executed.

### Version History
v3.0.1 - Initial Release (2023-07-13)

For discussion and feedback, please use the UD Forums <a href="https://forum.universal-devices.com/forum/429-schlage/" target="_blank">here</a>.
