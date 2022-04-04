# MyQ
A NodeServer for Polyglot v3 (PG3) that interfaces with LiftMaster/Chamberlain MyQ Web Service to allow the ISY 994i to control LiftMaster/Chamberlain MyQ compatible garage door openers, light modules, locks, and more. See https://www.liftmaster.com/myqcompatibility for compatible devices.

### Instructions for PG3 installation:

From the Polyglot Dashboard:
1. Install the MyQ nodeserver from the Polyglot NodeServer Store.
2. Add/modify the following Configuration Parameters under Configuration (note that the keys should automatically be added for required parameters):
    
    #### Advanced Configuration:
    - key: shortPoll, value: polling interval for MyQ cloud service in "active" polling mode (defaults to 20 seconds).
    - key: longPoll, value: polling interval for MyQ cloud service when not in "active" polling mode (defaults to 60 seconds).
    
    #### Custom Configuration Parameters:
    - key: username, value: username (email address) for MyQ online account (required)
    - key: password, value: password for MyQ online account (required)
    - key: homename, value: home name from which to load devices (if your account has access to multiple homes) (optional)
    
    Once the required configuration parameters have been saved, the nodeserver will attempt to connect to the MyQ Service. This may take a couple of minutes, so be patient.  Check the Polyglot Dashboard for notices regarding bad configuration parameters and connection failure conditions.
3. Once the MyQ Service node appears in the ISY Adminstative Console and the MyQ Service shows as connected, click "Discover Devices" in the MyQ Service node to load nodes for the gateways, garage door openers, light modules, locks, etc. configured in your account.

Notes for latest version (v3.0.18)

1. Please don't set the shortPoll value to an extremely low value, e.g. < 10 seconds. Repeated, frequent pollying of the MyQ service will result in Chamberlain making moves to block access by the nodeserver.
2. This version adds locks. Locks show the status only. In order to add control for locks, a donation of hardware is needed to develop and test.
3. The nodeserver does not attempt to connect and login to the MyQ service until the first longpoll - approximately 60 seconds after the nodeserver starts. This is done to allow network components to reestablish connections when recovering from a power failure. The nodeserver will continue to attempt to connect and login every longpoll (e.g., every 60 seconds) until a connection is established, so watch your Polyglot Dashboard messages for connection errors or bad credentials when starting/restarting to avoid locking out your account.
4. If you have multiple accounts (referred to in the MyQ app as "Homes") authorized to your MyQ account, the nodeserver loads the first one in the list by default. To change this, add the "homename" configuration parameter to the Custom Configuration Parameters with the name of the "home" you want to use. You can find the "Home Name" at the top of the device list in the MyQ mobile app. If you add/change the homename parameter after the initial connection to the MyQ Service has been made, then the nodeserver will have to be restarted for the change to take effect.
5. Upon selecting "Discover Devices," device (garage door opener, light module, etc.) nodes are grouped under the gateway through which they are accessed. You can "Ungroup" the device nodes from under the gateway nodes through the ISY Admin Console user interface.
6. When you close a garage door using the Close command of the associated node, there is a ~10 second alarming period. During this period, the status may change from "Closing" to "Open" before finally changing to "Closed," depending on the timing of the status polling.
7. To delete a device, you must use the PG3 Dashboard to ensure the device does not reappear in the ISY Admin Console the next time that Polyglot and/or the MyQ nodeserver are restarted.
8. The nodeserver will filter non-ASCII characters from the device description, like accent or Kanji characters, before adding the node to the ISY. You can rename the nodes in the ISY as you like.
9. Because of the longer than normal polling period (20 seconds), you may want to structure your programs in the ISY to perform a Force Update on the MyQ Service Node before other programs that depend on the door status are executed.

### Version History
v3.0.18 - Initial Release (2022-02-07)
- Converted myq-polyglotv2 to PG3 API
- Refactored code to fit PG3 design model (nslib)

For discussion and feedback, please use the ISY Forums here: https://forum.universal-devices.com/forum/328-myq/.
