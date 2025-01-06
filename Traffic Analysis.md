# Traffic Analysis

## Wireshark

### Filters

```c++
ip.addr == x.x.x.x
ip.src_host == domain.any 
ip.src_host >= x.x.x.x && ip.src_host <= x.x.x.x
!(ip.addr == x.x.x.x)   // filter out address
ip.dst == x.x.x.x
ip.src == x.x.x.x
tcp.port = xxx
tcp.dstport = xxx
http.request.method == "GET"
http.host contains ".com"
icmp
arp
dns
kerberos
udp
https
http
http2
smb
smb2
dhcp
tls
tls.app_data
tls.handshake
```

### Tools

- Analyze &rarr; Follow TCP Stream
- Analyze &rarr; Expert Information
- Statistics &rarr; HTTP Requests
- File &rarr; Export Objects &rarr; HTTP
- File &rarr; Export Objects &rarr; SMB
- Statistics &rarr; Resolved Addresses &rarr; (choose Hosts from dropdown)

### Shortcuts / Key bindings

- <kbd>Ctrl</kbd> + <kbd>m</kbd>    (mark packet)
- <kbd>Ctrl</kbd> + <kbd>g</kbd>    (go to packet)

### Tips

#### TLS

Everything you need to know is explained thoroughly in [TLS Wireshark Wiki](https://wiki.wireshark.org/TLS) and [Using SSL key log file Wireshark](https://sharkfesteurope.wireshark.org/assets/presentations17eu/15.pdf).

#### ICMP

Check for ICMP Flood Attack "icmp.type == 8"

#### ARP

[ARP Attack Detection](https://medium.com/@arifinamalul/arp-attack-detection-using-traffic-analysis-tools-1b236518a5c)

#### Extra

Check out [Wireshark GOAT](https://www.youtube.com/@ChrisGreer)

## Honorable Mentions

### Burp Suite

[History Converter](https://github.com/mrts/burp-suite-http-proxy-history-converter)

### pyshark

Example of pyshark:

```python
import time
import os
import nest_asyncio
import pyshark

nest_asyncio.apply()

def live_capture(interface, timeout=60):
    """
    Live capture packets from given interface.
    Print tls packets.    

    Args:
        interface (string): Network interface. 
        timeout (int, optional): Defaults to 60.
    """

    start = time.time()
    try:
        capture = pyshark.LiveCapture(
            interface=interface, output_file="output.log")
    except:
        print("Error: Cannot capture packets with Pyshark.")

    for item in capture.sniff_continuously():
        try:
            item.tls
            print(f"Item: {item.tls}")
            print(f"Record length: {item.tls.record_length}")
            print(f"Source: {item.ip.src} | Destination: {item.ip.dst}")
            print("\n")
        except:
            pass
        if timeout and time.time() - start > timeout:
            print("Error: Timeout occured.")
            break
```

### tcpdump

[tcpdump Cheatsheet](https://gist.github.com/jforge/27962c52223ea9b8003b22b8189d93fb)

### tshark

[USB keyboard parser](https://github.com/TeamRocketIst/ctf-usb-keyboard-parser)
