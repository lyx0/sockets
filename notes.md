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
* Network Access Layer (Ethernet, wi-fi, ...)


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
    

# Structs

Socket descriptor is an int
    
addrinfo
Is used to prep socket address structures for subsequent use.
Also used in host name lookups, and service name lookups.
```c
struct addrinfo {
	int              ai_flags;     // AI_PASSIVE, AI_CANONNAME, etc.
	int              ai_family;    // AF_INET, AF_INET6, AF_UNSPEC
	int              ai_socktype;  // SOCK_STREAM, SOCK_DGRAM
	int              ai_protocol;  // use 0 for "any"
	size_t           ai_addrlen;   // size of ai_addr in bytes
	struct sockaddr *ai_addr;      // struct sockaddr_in or _in6
	char            *ai_canonname; // full canonical hostname

	struct addrinfo *ai_next;      // linked list, next node
};
```
Afterwards you call getaddrinfo() which returns a pointer to a new 
linked list which filles the fields out for you.

sockaddr holds socket address information
```c 
    struct sockaddr {
        unsigned short    sa_family;    // address family, AF_xxx
        char              sa_data[14];  // 14 bytes of protocol address
    }; 
```

Eventhough connect() wants a struct sockaddr*, you can also use
a struct sockaddr_in.
```c 
    // (IPv4 only--see struct sockaddr_in6 for IPv6)
    
    struct sockaddr_in {
        short int          sin_family;  // Address family, AF_INET
        unsigned short int sin_port;    // Port number
        struct in_addr     sin_addr;    // Internet address
        unsigned char      sin_zero[8]; // Same size as struct sockaddr
    };
```

