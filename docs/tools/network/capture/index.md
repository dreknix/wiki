# Packet Capture

## Capturing

### Windows

Show available interfaces:

``` powershell
pktmon list
```

Start capturing:

``` powershell
pktmon start --capture --comp any --file-name C:\tmp\trace.etl
```

Stop capturing:

``` powershell
pktmon stop
```

Convert to PCAP format:


``` powershell
pktmon etl2pcap C:\tmp\trace.etl
```

## Wireshark

### Filter

#### Remove Broadcast and other noise

``` plaintext
!(eth.dst == ff:ff:ff:ff:ff:ff) && !(eth.dst[0:3] == 01:00:0c) && !(eth.dst[0:3] == 01:80:c2)
```

``` plaintext
!(eth.dst == ff:ff:ff:ff:ff:ff or arp or stp or lldp or llc or cdp)
```
