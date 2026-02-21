#  Challenge Tasks
# Task 1: DNS – How Names Become IPs
Explain in 3–4 lines: what happens when you type google.com in a browser?
When we type google.com, system first check local DNS cache. If not found it queries to DNS resolver whch contacts root servers, them .com and finally reaches the Google's authoritative name server, which return the IP address, browser then connects to that IP.

What are these record types? Write one line each:
A	- Maps a domain name to IPv4 address
AAAA 	- Maps a domain name to IPv6 address
CNAME	- Points one domain name to another domain name (Alias)
MX 	- Specifies the mail server responsible for receiving emails for domain.
NS	- Defines which name server is authoritative for the domain.

Run: dig google.com 

;; ANSWER SECTION:
google.com.             228     IN      A       172.217.19.238
A - 172.217.19.238
TTL - 228

--------------------------------------------------------------------------------------------------------------------------------------------------------
# Task 2: IP Addressing
1. What is an IPv4 address? How is it structured? (e.g., 192.168.1.10)
- A 32 bit numerical address used to identify devices on network. Written in dotted decimal format divided in four  octet separated by '.'.
  Range - 0 to 255, 8 buts, total 4 octet x 8bits = 32bits 

2. Difference between public and private IPs — give one example of each
- Public IPs - accessible over the internet, globally unique
- Private IPs - used inside local networks, not accessible directly from internet, used in home, offices, VPCs.

What are the private IP ranges?
10.x.x.x		- 10.0.0.0 to 10.255.255.255 
172.16.x.x – 172.31.x.x	- 172.16.0.0 to 172.31.255.255  
192.168.x.x		- 192.168.255.255

Run: ip addr show — identify which of your IPs are private
172.31.26.178/20 

--------------------------------------------------------------------------------------------------------------------------------------------------------
# Task 3: CIDR & Subnetting
What does /24 mean in 192.168.1.0/24?
this means 24 bits are reserved for the network portion and remaining 8 bits are host

How many usable hosts in a 
/24?	- 254 usable hosts 
/16?	- 65534
/28?	- 14


Explain in your own words: why do we subnet?
Subnetting is basically dividing large network in smaller network. If we put all the devices in one big network it will be hard to manage, traffic increases, security risk, Poor performance.
hence Subnetting is done to divide a large network into smaller manageable networks for better performance, security, and IP optimization.

Quick exercise — fill in:
CIDR	Subnet Mask	Total IPs	Usable Hosts
/24	255.255.255.0	256		254				
/16	255.255.0.0	65536		65534
/28	255.255.240.0	16		14

--------------------------------------------------------------------------------------------------------------------------------------------------------
# Task 4: Ports – The Doors to Services
What is a port? Why do we need them?
A port is communication endpoint on a system, tells the server which application should receive it.
One server can run many services at the same time like Website, DB, SSH, etc. all these uses same IP ports help to separate them.

Document these common ports:
Port	Service
22	SSH
80	HTTP
443	HTTPS
53	DNS
3306	MySQL
6379	Redis
27017	MongoDB

Run ss -tulpn — match at least 2 listening ports to their services
127.0.0.54:53 - DNS
0.0.0.0:22	SSH

--------------------------------------------------------------------------------------------------------------------------------------------------------
# Task 5: Putting It Together

You run curl http://myapp.com:8080 — what networking concepts from today are involved?
DNS - it resolves the domain name into an IP address.
IP address - the communication happens between source ip(local machine) and destination ip(website)
Port - 8080 port is exposed for my app to send the traffic to specified port.

Your app can't reach a database at 10.0.1.50:3306 — what would you check first?
- test port reachability if the service is running  
- Port number 3306 is added into inbound rule in security group or not.
