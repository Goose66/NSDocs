# BondBridge
A Node Server for Polyglot v3 that interfaces with Olibra Bond Bridges and Smart by Bond (SBB) devices locally to allow the ISY to control ceiling fans, lights, blinds, fireplaces, and more. See https://bondhome.io/ for more information on Olibra Bond Bridge.

***NOTICE***: A recent change to the eISY/Polisy firmware has broken the Bond Bridge Discovery process. We are looking for a workaround, but in the meantime please retrieve the IP address and token from the Bond Home mobile app for each Bond Bridge or SBB device on your LAN and add them in a semicolon separated-list to the "hostname" and "token" custom configuration parameters, respectively, before pressing the "Discover" button on the Dashboard. This will allow the node server to discover your devices setup on all of your Bond Bridges without relying on the Discovery process to find the bridges on the LAN (see release note # 1 below).

### Instructions for PG3 installation:

From the Polyglot Dashboard:
1. Install the BondBridge node server from the Polyglot Node Server Store.
2. Once the BondBridge node server is running, unlock the Bond devices on your network (e.g., power cycle your Bond bridge(s)) and then click "Discover" in the Dashboard to load nodes for each device discovered. Once a bridge is unlocked by power cycling, you have 10 minutes to intiate the Discover command. THIS PROCESS MAY TAKE SEVERAL SECONDS depending on the number of Bond Bridges and devices there are, so please be patient and wait a minute or two before retrying. Also, please check the Polyglot Dashboard for messages regarding Discover failure conditions.

### Release Notes (v3.1.13)
   
1. Your Bond bridge may not be discoverable by the automatic Discover routine depending on the configuration of your network and Wi-Fi. If your Bond Bridge or SBB device is not discoverable, you may connect to it manually by adding the following Custom Configuration Parameters under Configuration in the Polyglot Dashboard and then re-running the Discover command:
    
    ##### Custom Configuration Parameters:
    - key: hostname, value: locally accessible hostname or IP address for Bond Bridge (e.g., "192.168.1.145" or "ZZBL45678.local").
    - key: token, value: local access token for Bond Bridge. Available in the "Settings" for the bridge in the Bond Home mobile app.
    
   You can also specify multiple Bond Bridges and/or SBB devices for Discovery in Custom Configuration Parmaeters by specifying a hostname and token for each, separated by semicolons (;) in the "hostname" and "token" paramaters. Make sure that the corresponding values are specified in the same order.
2. You can optionally modify the polling interval by changing the "shortPoll" configuration parameter under Configuration - Advanced Configuration. Note that the default is 60 seconds, and because the Bond Bridge provides instantaneous state updates over UDP (BPUP), frequent polling for state is not required. The "longPoll" value is not used.
3. Only very basic functionality for shades, fireplaces, and generic devices (just Open/Close or On/Off functionality). Additional functionality will be added when users with these devices are available to test new code.
4. The ST driver of Ceiling Fan nodes reflects the current speed of the fan as a percentage of the maximum speed, with 0 being 0% (Off) and the maximum speed being 100%. In order to set the fan to a specific, known speed, use the Set Speed command. The Set Speed command lets you set the speed to up to 10 speed numbers. Speed numbers over the maximum speed set the fan to the maximum speed.
5. If your fan has an uplight and downlight, the nodserver will create two light nodes that you can turn on and off seperately. The result of setting the brightness level of either (if available) is unknown since I did not have such a fan to test with.
6. Increase Speed (%) and Decrease Speed (%) increase and decrese the fan speed percentage by approx. 3%. This is to mimic BRT and DIM commands in Insteon which brighten and dim lights/devices over 32 steps. The hope is that you can drop a SwitchLinc dimmer in a scene with the fan and the bright/dim functionality will work intuitively over the LEDs displayed to control the fan speed. Increase Speed (#) and Decrease Speed (#) increase and decrease the fan speed by one speed number.
7. This node server contains Alpha support for Sidekick Scene Keypads on Bond Bridge Pro. Functionality may not behave as expected.

### Version History
3.1.13 - Bug Fixes (2023-02-01)
- Changed several DEBUG log messages to INFO level
- Fixed bug in hooking BPUP_EventListener callback for keypad events 
- Added subset to all editors in nodedefs
- Append key number to name on node creation
- Fix error when closing BPUP socket
- Prevent polling from trying to update state for keypads

3.1.12 - Patch (2023-01-27)
- Quick fix for bad removal of files

3.1.11 - New features and fixes (2023-01-27)
- Added support for Sidekick Scene Keypads on Bond Bridge Pro
- Change way brt and dim commands are handled for fan speed
- Change way brt and dim commands are handled for light
- Update bridge hostname and token if already exists
- Ignore devices with no actions (log warning)
- Reorgnized file structure to be more asyncio friendly

3.0.10 - Bug fixes (2022-09-18)
- Fixed ignoring new "__" hash value in device list from API
- Fixed missing NLS entry for "Force Update" button label on Bridge nodes
- Increased wait time for newly discovered Bridge nodes to initialize to up to 1100ms before device discovery
- Refactored BPUP message parsing to allow for "action" messages

v3.0.9 - Bug fix (2022-02-27) 
- Fixed editors in profile

v3.0.8 - Initial Release (2022-02-26)
- Converted Bond-polyglotv2 to PG3 API
- Refactored code to fit PG3 design model (nslib)

For discussion and feedback, please use the ISY Forums here: https://forum.universal-devices.com/forum/329-bond/.
