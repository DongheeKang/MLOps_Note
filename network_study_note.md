### Next-Gen Firewalls (NGFW)
    ===============================================================================================
    Next-Gen Firewalls (NGFW)
    ===============================================================================================    
    similarities
    - static packet filtering
    - Stateful inspection or dynamic packet filtering, which checks every connection on every interface of a firewall for validity
    - Network address translation for re-mapping the IP addresses included in packet headers (NAT)
    - Port address translation that facilitates the mapping of multiple devices on a LAN to a single IP address (PAT)
    - Virtual private network (VPN) support, which maintains the same safety and security features of a private network over the portion of a connection that traverses the internet or other public network

    * differences
      - block to add application-level inspectioin
      - IPS
      - bringing intelligence from outside the firewall


### IPSec
    ===============================================================================================
    IPSec (Internet Protocol Security)
    ===============================================================================================
    - IPSec
      provides encryption and authentication at IP level.
      IPsec can run on routers, firewall machines, and application servers.
      ESP(Encapsulating Security Payload), AH(Autehntication Header) are standard protocol
      IKE(Internet Key Exchange) is used to handle tunneling as a higher level protocol.
      In configuraiton file, you should find left and right node for IPsec connection

      IPsec is a protocol suite for secure Internet Protocol(IP) communications that works by
      authenticating and encrypting each IP packet of a communication session. IPsec includes
      protocols for establishing mutual authentication between agents at the beginning of the
      session and negotiation of cryptographic keys to be used during the session. IPsec can be
      used in protecting data flows between a pair of hosts (host-to-host), between a pair of
      security gateways (network-to-network), or between a security gateway and a host
      (network-to-host).

      IPsec uses cryptographic security services to protect communications over IP networks.
      IPsec supports network-level peer authentication, data origin authentication, data integrity, data confidentiality (encryption), and replay protection.

      IPsec is an end-to-end security scheme operating in the Internet Layer of the
      Internet Protocol Suite, while some other Internet security systems in widespread use,
      such as Transport Layer Security (TLS) and Secure Shell (SSH), operate in the upper
      layers at the Transport Layer (TLS) and the Application layer (SSH). Hence, only IPsec
      protects all application traffic over an IP network. Applications can be automatically
      secured by IPsec at the IP layer

    - Tip:
    do configure IPsec related program first
      sysctl+racoon
      isk_psk
      tcpdump

    then....do firewall setting with iptables
      iptables=netfilter=packetfilter
      HAProxy
      NAT


### VPN Description
    ===============================================================================================
    VPN (Virtual Private Network)
    ===============================================================================================
    allows you to connect remote networks securely over an insecure connection, e.g.
    public internet. The network connection acts a physical connection, but actually may
    traverse many physical networks and system. That is why we call "virtual" Solution and
    VPN implementations include IPSEC, VPND, SSH, Cisco Routers, SSL/TLS(Secure Sockets Layer/
    Transport Layer Security) as a cryptographic protocol Port 1194

    VPN is a full-featured SSL VPN which implements OSI layer 2 or 3 secure network extension
    using the industry standard SSL/TLS protocol, supports flexible client authentication
    methods based on certificates, smart cards, and/or username/password credentials, and
    allows user or group-specific access control policies using firewall rules applied to the
    VPN virtual interface. OpenVPN is not a web application proxy and does not operate through
    a web browser.


### Stateless vs Stateful Firewall
    ===============================================================================================
    Stateless vs Stateful Firewall
    ===============================================================================================
    • Stateless firewall
    treats each network frame or packet individually. Such packet filters operate at the
    OSI Network Layer (layer 3) and function more efficiently because they only look at the
    header part of a packet. They do not keep track of the packet context. Such a firewall has
    no way of knowing if any given packet is part of an existing connection

    • A stateful firewall
    keeps track of the state of network connections (such as TCP streams or UDP communication)
    and is able to hold significant attributes of each connection in memory.  Stateful inspection
    monitors incoming and outgoing packets over time, as well as the state of the connection,
    and stores the data in dynamic state tables.

    a stateful firewall is a network firewall that tracks the operating state and characteristics
    of network connections traversing it. The firewall is configured to distinguish legitimate
    packets for different types of connections. Only packets matching a known active connection
    are allowed to pass the firewall.

    Stateful packet inspection (SPI), also referred to as dynamic packet filtering,
    Stateful firewall technology was introduced by Check Point


### Malware
    Virus are a little piece of code, that can copy itself to other programs when triggered. corrupt datas. Often attached to an excutable file.

    - Malware are software crashing systems, stealing important information.
    - Trojans are harmful software that can steal information, user are usually lead to open the software.
    - Ransomware host pc hostage, threatening to destroy data
    - Spyware secretly gathers private information such as passwords
    - Worms replicate themselves and attack other devices in the network, slowing down traffic and

    - Malware today is an conclusion of all above and more.


### Intrusion Detection Prevention (IDP)


### Intrusion Detection System (IDS)
is based on a specific pattern to detect the attacker's intrusion
* connect to one of the port at a switch
* IDS determine whether the traffic that is going to the web service is dangerous. eg. compare the signatures, anomalies with in quantity and types.
* It does not stop the attack from happening. it simply alerts the attack

### Intrusion Prevention System (IPS)
is an active defence to block the attacs detected
* plug Between the firewall and switch.
* can be virtual or physical
* prevent attack from the begin, protect the computer or server

### Hosted Intrusion Detection/Prevention System (HIDS)
* a IDS/IPS system sometime cost money, if we want to just protect one server, we can run prevent system as an software in tha server
* We can install it into many devices in our networl. eg. routers, firewalls(eg. UTM(Unified Threat Management):Palo Alto, checkpoint)

###  Snort (IDS/IPS, NIDS)
is an open source network intrusion detection system (NIDS)
* to detect a variety of attacks, such as buffer overflows, stealth port scans,
CGI attacks, SMB probes, OS fingerprinting attempts and much more.
* listen direct to the Network card, while fail2ban is not.
* Snort System can monitor traffic on the local network, since the network is well
portected by the NAT router, outside attacks against the local(samba, nfs, ssh)
are unlikely to reach the protected network, so external attacks won't be detected


###	OpenVAS (Open Vulnerability Assessment System)
is a framework of several tools offering a vulnerability management solution over 30,000 in total


###	fail2ban (IPS)
Fail2Ban is then used to update firewall rules to reject the IP addresses for a specified amount of time, scans log(e.g. /var/log/apache/error_log) and bans IPs that show the malicious signs too many password failures, seeking for exploits, etc.

