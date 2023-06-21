# kmlTakFlow
Node-Red Flow to forward Placemark locations of a KML Network Link as CoT to ATAK via multicast or TAKServer. (used in conjunction with Ampledatas node-red-contrib-tak).

![flow](/kmlTakFlow1.png?raw=true "Node Red Flow")


REQUIREMENTS:

-Node-Red

-node-red-contrib-tak https://github.com/snstac/node-red-contrib-tak

-TAKServer (or Multicast supported devices)

-WiGLE account to obtain an API Token https://www.wigle.net

-KML Network Link (tested working with Google Earth)

--------------------------------

SETUP NODE-RED:

-https://nodered.org/docs/getting-started/

-options include installing node-red on your local PC, a Raspberry Pi or similar single board computer or mini PC, deploying node-red on a cloud server or virtual private server (VPS), Docker Container, or local virtual environment. After installing node-red you should be able to go to the node-red dashboard at http://*nodeRedIPaddress:1880 you may have to open appropriate ports (1880) to allow devices to access the node-red dashboard.

-----------------------------

IMPORT .JSON FLOW TO NODE RED:

-in GitHub: click on "kmlTakFlow.json" > click on the download icon "Download raw file" > note where the "kmlTakFlow.json" file downloaded to, default is in your Downloads folder.

-in Node-Red: click on menu icon (3 horizontal lines top right) > click on "Import" > click on "select a file to import" > go to Downloads folder and click on "kmlTakFlow.json" > Upload > Import

ALTERNATIVELY..

-you can just copy the whole "kmlTakFlow.json" code from GitHub and paste it into the Node-Red Import Clipboard.

-------------------------------

CONFIGURE TAKSERVER:

-TAKServer can be hosted on various platforms: Raspberry Pi, Mini PCs, your local PC, Cloud Servers or Virtual Private Servers (VPS), Docker Containers, or Virtual Environments. Regardless, if you're looking for instructions on setting up your TAKServer I recommend ATAKHQs guides https://www.atakhq.com/en/tak-server.

-Configuring the TCP (TAKServer) node in Node-Red wigleTakFlow, Input your TAKServer IP and Port, and checkbox whether you're using SSL/TLS or not. If you are using SSL/TLS, ensure you upload you TAKServer certificates and key that are located in the directory of your TAKServer that stores all client certificates/keys (ie: ubuntu docker container is "/opt/tak/certs/files"). I Suggest creating a TAK client "node-red" to be used specifically for Node-Reds handling of forwarding data to TAK. In your certificates file you will want to copy and upload "node-red.pem" as the "Certificate", "node-red.key" as the "Private Key" and "ca.pem" as the "CA Certificate" in the TLS Configuration Properties in the TCP (TAKServer) node in Node-Red. You will also need to enter the Passphrase for your TAKServer truststore, this can be found in your TAKServers CoreConfig.xml file. Default is "atakatak".

----------------------------------

CONFIGURE PING NODE:

-in Node-Red: configure ping node "repeat" > "interval" and set the frequency of how often the kml feed gets requested and then forwarded to TAK.

-----------------------------------

CONFIGURE KML FEED NODE:

-in Node-Red: input your kml network link URL (ie: http://yournetworklink.kml).

-understand there has been limited testing with different kml network links, if your kml feed isn't working and this is something you're relying on using please comment on the issue you're having and I will adjust what I can to hopefully get it working.

-----------------------------------

USING THE KMLTAKFLOW:

-once your TCP TAKServer node is connected, your kml network link is configured in the kml feed node, and your ping node is configured with your preferred interval ping times, you're ready to start forwarding your kml data to TAK. Open ATAK/iTAK and test to make sure you're receiving CoT either from multicast or the TAKServer.


------------------------------------

HOW THE KMLTAKFLOW WORKS:

-the ping node initiates the http GET request.

-the kml feed node provides the network kml url for where to send the http GET request.

-the placemark data node filters and selects from the whole KML payload each "Placemark" tag (these can be seen as waypoints, mostly providing both an icon and coordinates).

-the name and coord node separate the name and coordinates from the placemark tag and turn it into an array.

-the join node combines both the name and coordinates for a joint array payload.

-the split node separates the coordinates by LAT LON and ALT instead of a combined string.

-the tak json node grabs all placemark data and formats it into cot json, things like name of CoT icon, color, type of CoT icon and stale time are passed here.

-the TAK node receives json cot and sends it as xml CoT for TAK to understand.

-the TCP/UDP nodes are where the CoT xml gets passed to for TAK devices to then receive. 
