# Wireguard Admin Scripts
#### Both of the scripts are copied and developed out from https://github.com/davidgross/ - so all creds to David Gross :)

Scripts to provision Wireguard clients. Written to run on the same Linux computer as the Wireguard server.
Focus is on daily use of the basics - add and delete peers/clients.

There are mainly 1 bash script and 2 template scripts to help you administer your wireguard instance:
- make_install.sh - let's user configure with their own ip's, file paths and more
- add-client-template.sh
- delete-client-template.sh

The make_install.sh generates two scripts based on user input and template scripts:
- add-client.sh
- delete-client.sh

And last but not least a directory called 'clients' containing:
- last-ip.txt - keeps state of last used wireguard peer's ip
- wg0-template.conf - template to generate a basic peer's wg0.conf
- wg0-template-prekey.conf - template to generate a peer's wg0.conf with a preshared key

### Considerations
I've had BIG headaches trying to **configure** a **self-hosted VPN server**, and I've had many errors in the process,
so to help out some possible mistakes I will make a few bulletpoints to take into consideration that **might help**:

- Don't use a VPN while configuring Wireguard, it just makes things harder and can possibly trigger some bad config.
- Check that your **server ports** are **enabled and configured** (mostly 51820 by default or any other you use).
- Check that you have a **static DNS IP address** or better, a **static IP address**, this can be configured by your **ISP** 
by calling them, usually they will give you a DDNS, which is a **Dynamic public static IP**, so make sure you have a **DDNS config** 
for **updating** your **IP incase it changes**, or else you'll be doomed.
- To check if you have a CGNET IP address assigned to your internet connection, check the WAN IP address on your Unifi dashboard. 
If the IP address falls between this range of 100.64.0.0 and 100.127.255.255 then you have a CGNET address.
- Be sure to **adjust** your **DNS** from your server, so if you've changed your server's DNS (maybe to Cloudflare --> 1.1.1.1, 1.0.0.1) 
be sure to add that to the config file, if you haven't changed it, or you have no idea what this means, just put 'auto' and you will 
be fine.

### Pre requirements
1. Wireguard, installed on server and basic wg0 is configured (https://www.wireguard.com/install/).
2. qrencode, for more ease of use when generating and distributing client configurations. (`apt install qrencode`)

##### add-client
###### Description
Script to generate client keys and config files as well as set it up on the wireguard server.
The script also makes a check if your client name already is in use.
It generates a qr code in the terminal for easy setup on a mobile phone. It also saves a qr png for a more portable use.
All client configurations is stored on the server, by default under '/etc/wireguard/clients/'.

Supports;
- check if client/peer name already exists
- tunnel split (LAN) or route all traffic
- use preshared key or not

###### Variable Examples
```bash
WG_DIR='/etc/wireguard'
SERVER_ADDRESS='public ip'
SERVER_PORT='51820'
SERVER_WG_IF='wg0'
SERVER_PUBLIC_KEY='server_public.key'
SERVER_PUB_KEY=$(cat $WG_DIR/$SERVER_PUBLIC_KEY)
CLIENT_WG_IF='wg0'
CLIENT_DIR='/etc/wireguard/clients'
CLIENT_IP='192.168.5.' # Last octet is automagically filled with correct IP read from the file last-ip.txt
WG_TEMPLATE=$WG_DIR/wg0-template.conf
WG_TEMPLATE_PREKEY=$CLIENT_DIR/wg0-template-prekey.conf
LAST_IP=$CLIENT_DIR/last-ip.txt
CLIENT_NAME=''
CLIENTS=($(wg show $SERVER_WG_IF peers | awk '{print $2}' | tr -d '()' | sed '/^[[:blank:]]*$/d'))
WG_REREAD='YES'
WG_PREKEY='YES'
```

##### delete-client
###### Description
Script to delete the a client from the wireguard server. It also deletes any previously generated and stored configuration files such as public/private key and wg0.conf-file.


###### Variable Examples
```bash
WG_DIR='/etc/wireguard'
CLIENT_DIR='/etc/wireguard/clients'
SERVER_WG_CONF='wg0.conf'
SERVER_WG_IF='wg0'
ARRAY_FILE='/tmp/WG_array'
WG_DELETE_PEER='/tmp/WG_delete_peer'
```

#### Contributing
I'm open for any feedback. If you have any questions - don't hesitate to ask.
Feel free to copy and use whatever you need in your own script. :smiley:
