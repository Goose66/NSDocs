# MyQ
A node server for PG3/PG3x that interfaces with LiftMaster/Chamberlain MyQ Web service to allow the IoX to control LiftMaster/Chamberlain MyQ compatible garage door openers, light modules, locks, and more. See https://www.liftmaster.com/myqcompatibility for compatible devices.

### Instructions for PG3/PG3x installation:

From the Polyglot Dashboard:
1. Install the MyQ nodeserver from the Polyglot NodeServer Store.
2. Add/modify the following Custom Configuration Parameters under Configuration (note that the keys should automatically be added for required parameters):
    
    - key: username, value: username (email address) for MyQ online account (required)
    - key: password, value: password for MyQ online account (required)
    - key: homename, value: home name from which to load devices (if your account has access to multiple homes) (optional)
    
    Once the required configuration parameters have been saved, the nodeserver will attempt to connect to the MyQ Service. This may take a couple of minutes, so be patient.  Check the Polyglot Dashboard for notices regarding bad configuration parameters and connection failure conditions.
3. Once the MyQ Service node appears in the ISY Adminstative Console and the MyQ Service shows as connected, click "Discover Devices" in the MyQ Service node to load nodes for the gateways, garage door openers, light modules, locks, etc. configured in your account.

### Notes for latest version (v3.2.22)

1. Please don't set the shortPoll value to an extremely low value, e.g. < 10 seconds. Repeated, frequent polling of the MyQ service may result in Chamberlain making moves to block access by the node server.
2. On the first start of the node server, the node server does not attempt to connect and login to the MyQ service until the first longpoll after the configuration parameters are entered and save (log message: "Re-establishing MyQ connection in longPoll()"). That means it may take up to 60 seconds before a connection is attempted after entering your credentials. In addition, the nodeserver will continue to attempt to connect and login every longpoll (e.g., every 60 seconds) until a connection is established, so watch your Polyglot Dashboard messages for connection errors or bad credentials to avoid locking out your account.
3. When the node server is restarted (e.g., manually in Dashboard or after failure/reboot), the node server will immediately attempt a connection to the MyQ service and then retry the connection attemp in every subsequent longpoll (e.g., every 60 seconds) until a connection is established. This is done to allow network components to reestablish connections when recovering from a power failure. 
4. If you have multiple accounts (referred to in the MyQ app as "Homes") authorized to your MyQ account, the nodeserver loads the first one in the list by default. To change this, add the "homename" configuration parameter to the Custom Configuration Parameters with the name of the "home" you want to use. You can find the "Home Name" at the top of the device list in the MyQ mobile app. If you add/change the homename parameter after the initial connection to the MyQ Service has been made, then the node server will have to be restarted for the change to take effect.
5. Upon selecting "Discover Devices," device (garage door opener, light module, etc.) nodes are grouped under the gateway through which they are accessed. You can "Ungroup" the device nodes from under the gateway nodes through the ISY Admin Console user interface.
6. When you close a garage door using the Close command of the associated node, there is a ~10 second alarming period. During this period, the status may change from "Closing" to "Open" before finally changing to "Closed," depending on the timing of the status polling.
7. To delete a device, you must use the PG3 Dashboard to ensure the device does not reappear in the ISY Admin Console the next time that Polyglot and/or the MyQ node server are restarted.
8. The nodeserver will filter non-ASCII characters from the device description, like accent or Kanji characters, before adding the node to the ISY. You can rename the nodes in the ISY as you like.
9. Because of the longer than normal polling period (20 seconds), you may want to structure your programs in the ISY to perform a Force Update on the MyQ Service Node before other programs that depend on the door status are executed.
10. This version includes locks but lock nodes show the status only. In order to add control for locks, a donation of hardware is needed to develop and test.

### Version History
3.2.22 - Bug Fixes (2023-09-14)
- added user-configurable User-Agent in oAuth and REST calls
- added PG3 managed Node Server Status
- removed version.txt file

3.1.21 - Bug Fix (2023-01-01)
- added removal of obsolete files in install.sh

3.1.20 - New Feature (2022-12-26)
- added "Low Battery" and "No Comms" states to garage door openers (for door position sensor)
- Refactored code to asyncio-friendly structure
- removed server.json and profile.zip
- reformatted device properties returned from API to make additions easier

3.0.19 - bug fixes/PG3 changes (2022-04-22)
- fixes for install.sh
- fixes for cameras
- removed unused server.json entries

v3.0.18 - Initial Release (2022-02-07)
- Converted myq-polyglotv2 to PG3 API
- Refactored code to fit PG3 design model (nslib)

For discussion and feedback, please use the ISY Forums here: https://forum.universal-devices.com/forum/328-myq/.
