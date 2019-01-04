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


## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)