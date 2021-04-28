my notes while going through 
https://beej.us/guide/bgnet/html/

### Data Encapsulation

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

### IP Adresses

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

    
### Subnets

192.0.2.12
Host 12 on network 192.0.2.0

Old style Netmask:
    IP: 192.0.2.12
    Network: 192.0.2.12 AND 255.255.255.0 which is 192.0.2.0

New Style Netmask:
    IPv4: 129.0.2.12/30
    IPv6: 2001:db8::/32
    IPv6: 2001:db8:5413:4028::9db9/64

### Port numbers

Is a 16 bit number.
IP is like street number and Port is like Apartment number.

/etc/services
HTTP: 80
telnet: 23
SMTP: 25
DOOM: 666

### Byte Order

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
    

### Structs

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

sin_addr 
```c
    // (IPv4 only--see struct in6_addr for IPv6)
    
    // Internet address (a structure for historical reasons)
    struct in_addr {
        uint32_t s_addr; // that's a 32-bit int (4 bytes)
    };
```

```c
    // (IPv6 only--see struct sockaddr_in and struct in_addr for IPv4)
    
    struct sockaddr_in6 {
        u_int16_t       sin6_family;   // address family, AF_INET6
        u_int16_t       sin6_port;     // port number, Network Byte Order
        u_int32_t       sin6_flowinfo; // IPv6 flow information
        struct in6_addr sin6_addr;     // IPv6 address
        u_int32_t       sin6_scope_id; // Scope ID
    };
    
    struct in6_addr {
        unsigned char   s6_addr[16];   // IPv6 address
    };
```

struct sockaddr_storage is designed to be large enough to hold IPv4 
and IPv6 structures. Sometimes you don't know in advance if it fills out 
your struct sockaddr with anIPv4 or IPv6 address. So you pass in this 
parallel structure and cast it to the type you need.
```c 
    struct sockaddr_storage {
        sa_family_t  ss_family;     // address family
    
        // all this is padding, implementation specific, ignore it:
        char      __ss_pad1[_SS_PAD1SIZE];
        int64_t   __ss_align;
        char      __ss_pad2[_SS_PAD2SIZE];
    };
```

### IP Addresses, Part 2

There are a lot of function that allow you to manipulate IP addresses.
The function inet_pton() (pton stands for presentation to network) converts
an IP address in numbers-and-dots notationinto either a struct in_addr 
or a struct in6_addr depending on whether you specify AF_INET or AF_INET6

If you have a IP address "10.12.110.57" or "2001:db8:63b3:1::3490" that you
want to store in it the conversion can be made like this:
```c
    // IPv4:
    
    char ip4[INET_ADDRSTRLEN];  // space to hold the IPv4 string
    struct sockaddr_in sa;      // pretend this is loaded with something
    
    inet_ntop(AF_INET, &(sa.sin_addr), ip4, INET_ADDRSTRLEN);
    
    printf("The IPv4 address is: %s\n", ip4);
    
    
    // IPv6:
    
    char ip6[INET6_ADDRSTRLEN]; // space to hold the IPv6 string
    struct sockaddr_in6 sa6;    // pretend this is loaded with something
    
    inet_ntop(AF_INET6, &(sa6.sin6_addr), ip6, INET6_ADDRSTRLEN);
    
    printf("The address is: %s\n", ip6);
```
When you call it you pass the address type (IPv4 or IPv6), the address, 
a pointer to a string to hold the result, and the maximum length of the
string.
These functions only work with numeric IP addresses, "www.example.com" wont work.
For "www.example.com" you will need to use getaddrinfo().

### Private (Or Disconnected) Networks

Lots of places have firewalls that hides the network from the rest of the world
for their own protection. Oftentimes the firewall translates "internal" IP addresses
to "external" IP addresses using a process called Network Address Translation (NAT)

10.x.x.x is one of a few reserved networks taht are only used either on fully 
disconnected networks, or on networks that are behind firewalls.
Most common ones are 10.x.x.x and 192.168.x.x
Less common is 172.y.x.x (where y is between 16 and 31)

### Jumping from IPv4 to IPv6


