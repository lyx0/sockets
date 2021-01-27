my notes while going through 
https://beej.us/guide/bgnet/html/

# Data Encapsulation

SOCK_DGRAM packets: 
Ethernet <- IP <- UDP <- TFTP <- Data

When received: 
    Hardware strips the Ethernet header
    Kernel strips IP and UDP headers
    TFTP Program strips the TFTP header

Layers of a full blown-model:
* Application
* Presentation
* Session
* Transport
* Network
* Data Link
* Physical (hardware, serial, Ethernet etc)


Layered Model:
* Application Layer (telnet, ftp, etc.)
* Host-to-Host Transport Layer (TCP, UDP)
* Internet Layer (IP and routing)
* Network Access Layer (Ethernet, wi-fi...)


Stream sockets: send()
Datagram Sockets: Encapsulate the packet and sendto()

# IP Adresses

IPv4: 192.0.2.111 
Available: 2^32 (4294967296)

IPv6: 2001:0db8:c9d2:aee5:73e3:934a:a5ae:9551 
Available: 2^128 (340282366920938463463374607431768211456)

2001:0db8:c9d2:0012:0000:0000:0000:0051
2001:db8:c9d2:12::51

2001:0db8:ab00:0000:0000:0000:0000:0000
2001:db8:ab00::

0000:0000:0000:0000:0000:0000:0000:0001
::1

Loopback address:
    IPv4: 127.0.0.1
    IPv6: ::1

Conversion between IPv4 and IPv6:
    IPv4:        192.0.2.23
    IPv6: ::ffff:192.0.2.23

    
# Subnets

192.0.2.12
Host 12 on network 192.0.2.0

Old style Netmask:
    IP: 192.0.2.12
    Network: 192.0.2.12 AND 255.255.255.0 which is 192.0.2.0

New Style Netmask:
    IPv4: 129.0.2.12/30
    IPv6: 2001:db8::/32
    IPv6: 2001:db8:5413:4028::9db9/64

# Port numbers

Is a 16 bit number.
IP is like street number and Port is like Apartment number.

/etc/services
HTTP: 80
telnet: 23
SMTP: 25
DOOM: 666

# Byte Order

Example two-byte hex number: b34f
Stored as 2 sequential bytes in order
Big Endian: b3 4f
Little Endian: 4f b3
    Used by Intel-compatible processors

Need to be in Host Byte Order:
    Assume host byte order is not right and always run them
    through a function to set it to the Network Byte Order

Two types of numbers you can convert:
    both signed and unsigned
    short (two bytes)
    long  (four bytes)

    Convert a short from Host Byte Order to Network Byte Order
    h for host, to, then n for network
    htons(), Host to Network Short
    htonl(), Host to Network Long
    ntohs(), Network to Host Short
    ntohl(), Network to Host Long
    

# structs

socket descriptor is an int
