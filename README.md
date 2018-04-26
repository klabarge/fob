# fob
USB over IP

## Setup USB server on LAN

### USB Server Setup (Raspberry Pi, ARM, VirtualHere)

1. `wget http://www.virtualhere.com/sites/default/files/usbserver/vhusbdarm`
1. `chmod +x ./vhusbdarm`
1. `sudo ./vhusbddarm`
1. USB Devices can now be reached

##### Make persistant
1. Open `crontab` as root `sudo crontab -e`
1. Add entry `@reboot /path/to/vhusbdarm`

### USB Client Setup (CLI, Linux, VirtualHere -- Fixme free version, no daemon support)

#### macOS
1. Download macOS installer from [VirtualHere](https://www.virtualhere.com/usb_client_software)
1. VirtualHere Client should automatically detect server

#### Windows
1. Download macOS installer from [VirtualHere](https://www.virtualhere.com/usb_client_software)
1. Run `vhui64.exe`
1. Right click Hubs, Specify Hubs
1. Enter in IP address of the server and port (7575)
   * e.g. 192.168.0.104:7575

#### Linux
1. `mkdir vhclient && cd vhclient`
1. `wget http://virtualhere.com/sites/default/files/usbclient/vhclientx86_64`
1. `chmod +x vhclientx86_64`
1. `sudo ./vhclientx86_64 &`

##### Interact as normal user
1. `./vhclientx86_64 -t "MANUAL HUB ADD,HOSTNAME:PORT"` #####FIXME #####
1. `./vhclientx86_64 -t "LIST"`
1. `./vhclientx86_64 -t "USE,device.num"` #####FIXME #####
1. Should say "OK". Device will now list in USB devices
1. Once device is done being used: `./vhclientx86_64 -t "STOP USING,device.num"` #####FIXME #####

##### USB Client Setup (GUI, Windows, VirtualHere)

1. Install cilent [here](https://www.virtualhere.com/usb_client_software)
1. To connect to WAN (Windows):
 * Right Click, Specify Hubs, Add
 * Type in `HOST:PORT` #####FIXME #####

## WAN Configuration

##### Dynamic DNS Update (CLI, Linux, DuckDNS)
1. `mkdir duckdns && cd duckdns`
1. `vi duck.sh`
1. Add the line: #####FIXME #####

 ```bash
 echo url=”https://www.duckdns.org/update?domains=DOMAIN&token=TOKEN&ip=” | curl -k -o ~/duckdns/duck.log -K -
 ```
 * `domains=DOMAINS` provided by duckdns
 * `token=TOKEN` provided by duckdns
 * `ip=` is to be an empty string, duckdns automatically detects client's IP address if left empty

1. `chmod 700 duck.sh`
1. Test the script
 * `./duck.sh`
 * `cat duck.log` should only contain the word "OK"


1. Create a cronjob to run the script every 5 minutes:

 * `crontab -e`
 * `*/5 * * * * ~/duckdns/duck.sh >/dev/null 2>&1`

##### Setup Auth
Virtual Here documentation can be found [here](https://www.virtualhere.com/authorization)

1. Make sure `./vhusbddarm` is not running before editing `config.ini`
1. `sudo vi config.ini`
1. Add the line:

 ```bash
 clientAuthorization=/home/pi/auth.sh “$PASSWORD$”
 ```

1. `vi auth.sh` to create a script
1. Add: ######FIXME ######

 ```bash
 #!/bin/bash
 # Example script for performing basic user authorization for virtualhere
 # Also includes a simple password protection mechanism for accessing a device
 # Return 2 if the user needs to provide a password (or the password is incorrec$
 # Return 1 if the user is allowed to access this device
 # Return 0 if the user is not allowed to access this device
 # Parameters are passed in as:
 # $1 = PASSWORD

 logger "Authorizing -> '$1'"
 # "mypassword" = " " as an MD5 hash

 if [ "$6" == " " ]; then
 logger "Authorized!"
 exit 1
 else
 exit 2

 fi
 ```
 
##### Setup SSL

https://www.virtualhere.com/ssl_setup
