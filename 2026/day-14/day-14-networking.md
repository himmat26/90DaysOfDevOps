
## OSI Model
1. Application – You use an app (like WhatsApp or Gmail).
2. Presentation – Data is formatted (like converting emojis or encrypting messages).
3. Session – Starts and manages the conversation (like a call connection).
4. Transport – Ensures data is sent correctly (like tracking delivery).
5. Network – Finds the best route (like Google Maps for data).
6. Data Link – Packs data into frames (like putting a letter in an envelope).
7. Physical – Sends bits over cables or Wi-Fi (like roads for delivery trucks).

## TCP/IP Model
1. Application Layer - User interacts with apps like browsers or email.
2. Transport Layer - Ensures data is delivered correctly and in order.
3. Internet Layer - Chooses the best path for data to travel.
4. Network Access Layer - Sends data through physical devices like routers.

## Protocols:
1. Application Layer
    • HTTP (HyperText Transfer Protocol) – Transfers web pages.
    • HTTPS (HyperText Transfer Protocol Secure) – Secure web communication.
    • FTP (File Transfer Protocol) – Transfers files between systems.
    • SMTP (Simple Mail Transfer Protocol) – Sends emails.
2. Transport Layer
    • TCP (Transmission Control Protocol) – Reliable, connection-oriented data transfer.
    • UDP (User Datagram Protocol) – Fast, connectionless data transfer.
3. Internet Layer
    • IP (Internet Protocol) – Provides addressing and routing.
4. Network Access Layer
    • Ethernet – Wired LAN communication.
    • Wi-Fi (IEEE 802.11) – Wireless LAN communication.

# Identity: 
hostname -> ip-172-31-26-178
ip addr -> 172.31.26.178

# Reachability: ping <target> — mention latency and packet loss.
    ping ip-172-31-26-178
    PING ip-172-31-26-178.eu-north-1.compute.internal (172.31.26.178) 56(84) bytes of data.
    64 bytes from ip-172-31-26-178.eu-north-1.compute.internal (172.31.26.178): icmp_seq=1 ttl=64 time=0.023 ms
    64 bytes from ip-172-31-26-178.eu-north-1.compute.internal (172.31.26.178): icmp_seq=2 ttl=64 time=0.034 ms
    64 bytes from ip-172-31-26-178.eu-north-1.compute.internal (172.31.26.178): icmp_seq=3 ttl=64 time=0.032 ms

    15 packets transmitted, 15 received, 0% packet loss, time 14356ms
    rtt min/avg/max/mdev = 0.023/0.033/0.037/0.003 ms

# Path: traceroute <target> (or tracepath) — note any long hops/timeouts.
traceroute to ip-172-31-26-178 (172.31.26.178), 30 hops max, 60 byte packets
 1  ip-172-31-26-178.eu-north-1.compute.internal (172.31.26.178)  0.019 ms  0.007 ms  0.006 ms

# Ports: ss -tulpn (or netstat -tulpn) — list one listening service and its port.
ss -tulpn
Netid              State               Recv-Q              Send-Q                                Local Address:Port                            Peer Address:Port              Process
udp                UNCONN              0                   0                                         127.0.0.1:323                                  0.0.0.0:*
udp                UNCONN              0                   0                                        127.0.0.54:53                                   0.0.0.0:*
tcp                LISTEN              0                   511                                         0.0.0.0:80                                   0.0.0.0:*
tcp                LISTEN              0                   4096                                        0.0.0.0:22                                   0.0.0.0:*

ss -tulpn |grep 80
tcp   LISTEN 0      511               0.0.0.0:80         0.0.0.0:*
tcp   LISTEN 0      511                  [::]:80            [::]:*

# Name resolution: dig <domain> or nslookup <domain> — record the resolved IP.
dig www.google.com -> SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
nslookup google.com -> Server: 127.0.0.53

# HTTP check: curl -I <http/https-url> — note the HTTP status code.
ubuntu@ip-172-31-26-178:/var/www/html$ curl http://16.16.64.83/
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

# Connections snapshot: netstat -an | head — count ESTABLISHED vs LISTEN (rough).
ubuntu@ip-172-31-26-178:~$ netstat -an | head
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:6011          0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:34547         0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN
tcp        0      0 172.31.26.178:42022     16.171.83.61:443        ESTABLISHED
