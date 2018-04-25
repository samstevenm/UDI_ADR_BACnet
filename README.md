# UDI_ADR_BACnet
Allow ISY994 to write to BACnet devices.  Combines ISY994 and Lighting Control system to send UDP DR signal to BACnet devices.
This leverages the [ISY994i](https://wiki.universal-devices.com/index.php?title=Main_Page#ISY994i_Series) and the [UDI Network Module](https://wiki.universal-devices.com/index.php?title=Main_Page#Networking) and allows a lighting system to believe receive a BACnet write command.

#### Requirements: 
1. An ISY994 device with the Networking Module Installed
    - I used the ISY994i
    - Since I used the ISY994i I needed the PowerLinc Modem #2413S
2. Networked (Ethernet) Lighting Control Hubs (We're using Vive Hubs)
3. All devices networked together (ethernet)
4. Latest updates (just a good idea in general) [Vive](http://www.lutron.com/en-US/Service-Support/Pages/Technical/SoftwareDownloads/SoftwareDownloads.aspx) [ISY](https://wiki.universal-devices.com/index.php?title=Main_Page#ISY994i_Series)

#### Testing
Sucessfully implemeted basic Demand Response Activate and Deactivate with [Lutron Quantum®](http://www.lutron.com/en-US/Products/Pages/WholeBuildingSystems/Quantum/Overview.aspx) and [Lutron Vive](http://www.lutron.com/en-US/Products/Pages/WholeBuildingSystems/Vive/Overview.aspx) systems networked (ethernet) with and [ISY](https://wiki.universal-devices.com/index.php?title=Main_Page#ISY994i_Series)
, configured as described.

#### Files:
1. `admin.jnlp` Admin Java program for ISY
2. `LutronZone_and_DR.zip` Custom Networking Resource Module for ISY

#### Useful Testing Utilties:
1. [YABE](https://sourceforge.net/projects/yetanotherbacnetexplorer/) - To send the BACnet commands
2. [Wireshark](https://www.wireshark.org/download.htmlhttps://dannagle.com/packetsender) - To watch the BACnet commands
    - Using filter `udp.port==47808`
    - Use YABE to change a Value
    - Copy hex stream from the resulting packet, you want the bytes starting at "BACnet Virtual Link Control"
3. [Packet Sender](https://dannagle.com/packetsender) - Take your hex command, send as UDP
    - Check back in Wireshark and YABE to see if you actually did something

#### ISY Programming
##### NOTE: you'll need the Netowrking module installed, and network resources created, this is covered in the next section.  Read the Docs [ISY](https://wiki.universal-devices.com/index.php?title=Main_Page#ISY994i_Series) 

  1. Create programs (under programs tab) for the following conditions: 
          - DR ON (or Active)
      ```   
            If
                Module OpenADR Status is Active
                AND Module OpenADR Mode is High (you will have to ask SCE which type of signal they are sending)
            Then
                Module Network Resource DR On (Hub 1)
                Module Network Resource DR On (Hub 2)
      ```
        - DR OFF (or Inactive)
      ```   
           If
                Module OpenADR Status is Inactive 
                OR Module OpenADR Status is Pending
           Then
                Module Network Resource DR Off (Hub 1)
                Module Network Resource DR Off (Hub 2)
      ```

##### Using the Networking module, and resources to trigger demand response.

 1. Create Network Resources (under Configuration > Netowrking tab) for the following conditions, for *EACH HUB IP ADDRESS*: 
    - DR ON (Hub 1)
      - Mode: UDP
      - Host: IP address of your lighting hub (192.168.1.101)
      - Port: 47808 (typical for BACnet)
      - Timeout(ms): 500
      - Mode: Binary
      - Body: `129;10;0;23;1;4;0;117;15;15;12;1;64;0;2;25;85;62;145;1;63;73;16`
 
    - DR OFF (Hub 1)
      - Mode: UDP
      - Host: IP address of your lighting hub (192.168.1.101)
      - Port: 47808 (typical for BACnet)
      - Timeout(ms): 500
      - Mode: Binary
      - Body: `129;10;0;23;1;4;0;117;15;15;12;1;64;0;2;25;85;62;145;0;63;73;16`
      
    - DR ON (Hub 2)
      - Mode: UDP
      - Host: IP address of your lighting hub (192.168.1.102)
      - Port: 47808 (typical for BACnet)
      - Timeout(ms): 500
      - Mode: Binary
      - Body: `129;10;0;23;1;4;0;117;15;15;12;1;64;0;2;25;85;62;145;1;63;73;16`
 
    - DR OFF (Hub 2)
      - Mode: UDP
      - Host: IP address of your lighting hub (192.168.1.102)
      - Port: 47808 (typical for BACnet)
      - Timeout(ms): 500
      - Mode: Binary
      - Body: `129;10;0;23;1;4;0;117;15;15;12;1;64;0;2;25;85;62;145;0;63;73;16`

2. Save and test these commands.  Verify that the Hub GUI changes, and Demand Reponse activates. 

#### Setting up your ISY to communicate with SCE: https://wiki.universal-devices.com/index.php?title=Main_Page#OpenADR 
##### NOTE: you'll need server settings and user info from SCE

#### Lighting Hub Setup [Vive](http://www.lutron.com/TechnicalDocumentLibrary/041571_Web.pdf)

 1. Each lighting control Hub will need to have a unique, prefferably static IP address.
 2. Each lighting control Hub will need to have a unique BACnet instance number (recommend offsetting by 100).
 3. Each lighting control Hub will need to have a unique BACnet network number (recommend offsetting by 1).
 4. Hubs do not need to have a System Password set, but it is recommened.



