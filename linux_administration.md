# Netowrk and Security


## Contents

* [SSH](#ssh)
* [GPG](#gpg)
* [Network Topology](#topology)
* [Next-Gen Firewalls (NGFW)](#ngfw)

* [...](#...)



<br/><a name="def"></a>
## Definisiton



### Glossary

* 802.1X
  - Enhance security of WLAN by IEEE, provides authentication frame work, allows users to be authenticated by a central authority. wireless.
* Authenticated DHCP
  - First network access control, authenticating user id/password be for delivering a DHCP.
* Backbone
  - Primary connectivity mechanism of a hierarchical distribution system. All systems that have connectivity to the backbone are assured of connectivity to each other.
* Blacklisting
  - An access control system that denies entry to specific users, programs, or net work addresses
* Berkeley Internet Name Domain (BIND)
  - The most commonly used DNS service of the internet
* Broadcasting
  - A packet that is received by all stations in the domain

* Cyclic Redundant Check (CRC)
  - A mathematical calculation on a frame work or cell that is used for error detection. If two CRCs don't match, there is an error.
* DDI
  - a unified service or solution that integrate DNS, DHCP, and IPAM (IP Address Management) into one.
* Dynamic Host Configuration Protocol
  - Assigning IP address to device
* Domain Name System
  - the system of domain names. eg. google.com (no www) godaddy.com
* Frame
  - A unit of data transmission in layer two, containing a packet to layer three
* File Transfer Protocol (FTP)
  - the protocol to transfer files from one host to another. eg. cyberduck (assure the security of transfer). Now people use FTP.
* Hypertext Transfer Protocol (HTTP)
  - Protocol that supports request-response from a server. eg. a browser sends hyper text "www.google.com" to google.com through HTTP, then google.com returns a HTML to our browser
* Hop
  - Each time a packet is forwarded, it undergoes a "hop". (traceroute www.google.com)
* IP Address Management (IPAM)
  - The administration of DNC and DHCP. It means Planing, tracking, and managing the Internet Protocol space used in a network. eg. DNS knowing the IP address taken via DHCP, and updating itself.
* Local Area Network (LAN)
  - Its a Network that connects computers and devices in a limited geographical area. oppose with WANs (Wide Area Network). eg. home and school. Smaller area, faster speed, no need for telecommunication line.
* deep packet inspection
  - routers looking inside the data packet other than just read the ip address, take very slow


<br/><a name="network-terms"></a>
## Network Terms

### Hub, Switch and Router
* Hub
  - connects all the network devices together
  - multiple ports
  - not intelligent, do not know where data going to be sent
  - data is copied to all its ports -- broadcasting
* Switch
  - like a hub, accepts ethernet connections from network devices
  - it is intelligent, knows the physical address(MAC address) in switch table.
  - when a data is sent, it is directed to to intended port
  - reduce unnecessary traffic
* Hub and switch are not capable of exchanging data outside its own network, because to be able to reach outside network a device need to be able to read IP addresses
* Router
  - A router routes data from one network to another based on its IP address
  - The gateway of a network
* Hub and switches are used create networks while routers are used to connect networks

### Domain Name System (DNS)
* resolves domain names to IP addresses
  1. domain name typed in
  2. DNS server search through its database to find its matching IP address
  3. DNS will resolve the domain name into IP addresses
* works like a phone book

#### Detailed Steps:
* type in the Domain Name in web browser
* if the computer can't find its IP address in its cache memory, it will send the query to the Resolver server(basically your ISP)
* Resolver will check its own cache memory, if not, it will send the query to Root server, the top or the root of the DNS hierarchy, 13 sets of root servers around the world, operated by 12 organizations. each set has its own IP address
* The root server will direct the resolver the Top Level Domain server (TLD), for the .com, .net, .org(top level domains) domain.
* TLD will direct the resolver to the Authoritative Name Server(ANS), and the resolver will ask the ANS for the IP address
* ANS is responsible for knowing everything including the IP address of the domain
* ANS will respond with IP address
* the resolver will tell the computer the IP address
* the resolver will store the IP Address in its cache memory



<br/><a name="topology"></a>
## Network Topology

* network topology is a layout of how a network communicates with different devices
* wired and wireless

### Wired Topologies

#### Star
* all devices connected to one hub or switch
* pro: one devices failed to connect will not affect other devices
* con: if the central hub or switch failed, it will affect every all devices on that point. single point failure

#### Ring
* connected in a circle, every computer has two neighbors, every packet is sent through the ring
* rarely used today
* easy to install and fix
* one point failure

#### Bus
* each device is connected to the back bone
* the back bone is a coaxial cable, connected to the computers using BNC connector (T connectors)
* pro: cheap and easy to implement
* con: needs terminators at both end of back bone, if not there will be signal reflection, causing data flow disrupted

#### Mesh
* each computer is connected to each other
* con: high redundancy level, rare failure
* pro: expensive
* rarely used on LAN, mainly used on WAN(like internet)


### Wireless Topologies

#### Infrastructure
* a wireless port connected to one of switch or hub like a star topology








<br/><a name="def"></a>
## Security


<br/><a name="ssh"></a>
#### Secure Shell SSH


SSH is a communication Protocol. The traffic is encrypted
SSHD is the server (Open SSH Daemon) and SSH is the client.
the server must have sshd installed and running


*  ~/.ssh/id_rsa (private key)
   ~/.ssh/id_rsa.pub (public key)
  - public key goes into server "authorized_keys" file

* Generating a new SSH key
    create

    ```
    $ ssh-keygen -t ed25519 -C "your_email@example.com"
    $ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
    ```

    adding your SSH key to the ssh-agent
    ```
    $ sudo -s -H
    $ exec ssh-agent bash
    $ eval "$(ssh-agent -s)"
    ```

    for MacOS do modify config to update keychain automatically,
    ```
    $ touch ~/.ssh/config
    | Host *
    |     AddKeysToAgent yes
    |     UseKeychain yes
    |     IdentityFile ~/.ssh/id_ed25519
    ```  

    Add your SSH private key to the ssh-agent and store your passphrase in the keychain
    ```
    $ ssh-add -K ~/.ssh/id_ed25519
    ```

    public key goes into server "authorized_keys" file
    ```
    ~/.ssh/id_rsa (private key)
    ~/.ssh/id_rsa.pub (public key)
    ```

<br/><a name="gpg"></a>

#### GPG encryption and signing tool
gpg2 is the OpenPGP part of the GNU Privacy Guard (GnuPG). It is a tool to provide digital encryption and signing services using the OpenPGP standard.
* GPG algorithms
    RSA
    ElGamal
    DSA
    ECDH
    ECDSA
    EdDSA


* creating GPG key
  create key

  ```
  $ gpg --full-generate-key
    $ gpg --default-new-key-algo rsa4096 --gen-key
  ```
  to list the long form of the GPG keys for both a public and private key
  ```
  $ gpg --list-secret-keys --keyid-format=long
  |/Users/hubot/.gnupg/secring.gpg
  |------------------------------------
  |sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2050-03-10]
  |uid                          Hubot
  |ssb   4096R/42B317FD4BA89E7A 2016-03-10   
  ```  
  export the public key
  ```
  $ gpg --armor --export 3AA5C34371567BD2
  # Prints the GPG key ID, in ASCII armor format
  ```

#### SSL/TLS
    ===============================================================================================
    SSL/TLS
    ===============================================================================================
    - Self signed vs Let’s Encrypt vs StartSSL.com

	  You can use CA tools in SSL
	  $ cd /etc/pki/tls/misc
	  $ CA -newca   : create your private key, cakey.pem (private key)
	  $ CA -newreq  : a signing request, newreq.pem(request CSR) & newkey.pem(new private key)
	  $ CA -signreq : sign the request, newcert.pem (CA signed certificate)
	  you will find the list of generated files under
	  $ cd /etc/pki/CA/private/

	  $ sudo apt-get install letsencrypt
	  $ sudo letsencrypt certonly -a webroot --webroot-path=/var/www/html
			 -d example.com -d www.example.com
    $ sudo ls /etc/letsencrypt/live/example.com
	  | cert.pem              : publick key, server certificate only.
	  | chain.pem             : root and intermediate certificates only.
	  | fullchain.pem         : full trust chain
	  | privkey.pem           : private key

    Googld chrome StartSSL.com
    ca.pem        - StartSSL's Root certificate
    private.key   - The unencrypted version of your private key (be very careful)
    server.ca.pem - The intermediate certificate for StartSSL
    ssl.key       - The encrypted private key (does not need to be copied to server)
    ssl.crt       - Your new certificate

    To convert a certificate or certificate chain from DER to PEM
   	$ openssl x509 -inform DER -in Certificate.der -outform PEM -out Certificate.pem

   	To convert a private key from DER to PEM    
    $ openssl rsa -inform DER -in PrivateKey.der -outform PEM -out PrivateKey.pem

    To decrypt an encrypted private key (remove the password or passphrase)
   	$ openssl rsa -in EncryptedPrivateKey.pem -out PrivateKey.pem

   	To convert a certificate bundle from PKCS#12 (PFX) to PEM
   	$ openssl pkcs12 -in CertificateBundle.p12 -out CertificateBundle.pem -nodes

   	To convert a certificate bundle from PKCS#7 to PEM
   	$ openssl pkcs7 -in CertificateBundle.p7b -print_certs -out CertificateBundle.pem



    In server
    generate key
    openssl req -nodes -x509 -sha256 -newkey rsa:4096 -keyout "general_key.key" -out "general_key.pub"
      -days 365 -subj "/C=DE/ST=SAP SE/L=Walldorf/O=bssdb/OU=dbcat/CN=general_key"

    Sign the file
    openssl dgst -sha256 -sign "general_key.key" -out .checksum.sha256 .checksum.md5

    In local machine
    verify the signature
    openssl dgst -sha256 -verify <(openssl x509 -in "general_key.pub"  -pubkey -noout) -signature dbaenv.sha256 .checksum.md5

    then will see OK
    Verified OK

    openssl dgst -sha256 -verify <(openssl x509 -in "/home/c5258293/git/dbcat/certs/SAPGlobalSSLCA.crt"  -pubkey -noout) \
    -signature /var/tmp/dbcatTrans/dbaenv-fetch-1.7/.checksum.sha256  /var/tmp/dbcatTrans/dbaenv-fetch-1.7/.checksum.md5


    ==============================================================================================
    SSL vs GPG comparison
    ===============================================================================================
    Hi all,

    The following instructions assume that you are the administrator of swshare repository.
    (https://dba.wdf.sap.corp/swshare/)

    • SSL authentication (administrator level)

        Create a certificate for the landscape's domain
        $ openssl req -nodes -x509 -sha256 -newkey rsa:4096
        -keyout "SAPGlobalSSLSign.crt"
        -out "SAPGlobalSSLSign.key"
        -days 365
        -subj "/C=DE/ST=SAP SE/L=Walldorf/O=bssbd/OU=dbcat/CN=dbcat's Sign Key"

        You should now have 2 certificates, keep key file in safe region. And copy the public key file to the swshare
            "SAPGlobalSSLSign.crt" private certificate key move to /root/SAPGlobalSSLSign.crt
            "SAPGlobalSSLSign.key" public certificate key move to /swshare/dbcat/v3.8/certs/SAPGlobalSSLSign.key

        Generate self-signed certificate files, here is an example for signing of checksum list of ASE
        $ export signfile=/swshare/ase/16.0.02.06/linux_x86_64
        $ openssl dgst -sha256 -sign "/root/SAPGlobalSSLSign.crt" -out "${signfile}"/.checksum5.md5.sig "${signfile}"/.checksum.md5

        Verification of signed file will be performed in the factory
        modFactoryTransferValidateSignature( ) in factoryTransfer.sh


    • GPG authentication (administrator level)

        Generate a keypair using gpg2 command, enter name, email, keysize and choose a passphrase at the end (please keep all information in the note!)
            gpg2 --gen-key
            | .....................................
            | .....................................
            | Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
            | You need a Passphrase to protect your secret key.
            | .....................................

        keep the public master GPG key at the line on "pub 2048R/2AFFE2C5 2017-08-02" (2AFFE2C5 is key-id)
            | gpg: checking the trustdb
            | gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
            | gpg: depth: 0 valid: 1 signed: 0 trust: 0-, 0q, 0n, 0m, 0f, 1u
            | pub 2048R/2AFFE2C5 2017-08-02
            | Key fingerprint = F619 5E0E 4B8A 0675 22A3 55FA ACBA 0973 2AFF E2C5
            | uid SAPGlobalSign (GPG authentification) SAPGlobalSign@sap.com
            | sub 2048R/00B0933C 2017-08-02


        export your public key to use in signing
            gpg2 --no-armor --output SAPGlobalGPGSign.key --export 2AFFE2C5
            "SAPGlobalGPGSign.key" public key move to /swshare/dbcat/v3.8/certs/SAPGlobalGPGSign.key

        signing your documents e.g. checksum file, you should need a passphrase in this stage. You can also use a batch mode without typing passphrase
            export signfile=/swshare/ase/16.0.02.06/linux_x86_64
            gpg2 --armor --output "${signfile}"/.checksum.md5.asc --detach-sign "${signfile}"/.checksum.md5
            gpg2 --armor --output "${signfile}"/.checksum.md5.asc --batch --passphrase "Pa\$\$w0rd" --detach-sign "${signfile}"/.checksum.md5

        In dbcat, verification of signed documents will be perforemd
            gpg2 --no-default-keyring --keyring SAPGlobalGPGSign.key --verify .checksum.md5.asc .checksum.md5


        ===============================================================================================
        Practice and output
        ===============================================================================================
        test environment...........................
        sudo su -
        cd /var/tmp/dbcatTrans/zfinal/
        openssl req -nodes -x509 -sha256 -newkey rsa:4096 -keyout "SAPGlobalSSLCA.key" -out "SAPGlobalSSLCA.crt" \
                -days 365 -subj "/C=DE/ST=SAP SE/L=Walldorf/O=bssbd/OU=dbcat/CN=dbcat's Sign Key"
        openssl dgst -sha256 -sign SAPGlobalSSLSign.crt -out .checksum.md5.sig .checksum.md5
        openssl dgst -sha256 -verify <(openssl x509 -in /var/tmp/dbcatTrans/zfinal/SAPGlobalSSLSing.key -pubkey -noout) \
        -signature .checksum.md5.sig .checksum.md5.sig

        --------------------------------------------
        gpg2 --gen-key
        gpg: checking the trustdb
        gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
        gpg: depth: 0  valid:   3  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 3u
        gpg: next trustdb check due at 2018-08-03
             pub   2048R/2288A15E 2017-10-09
                Key fingerprint = A51E BA24 9E16 3041 9D80  E42F 15F7 33D5 2288 A15E
             uid                  Donghee Kang (test2) <donghee.kang@sap.com>
        --------------------------------------------
        gpg2 --armor --output SAPGlobalGPGSign.key --export 9ED95FE6
        gpg2 --armor --output SAPGlobalGPGPrivate.key --export-secret-keys 9ED95FE7
        gpg2 --armor --output .checksum.md5.gpg --batch --passphrase "Pa\$\$w0rd" --sign .checksum.md5
        gpg2 --no-default-keyring --keyring /var/tmp/dbcatTrans/zfinal/SAPGlobalGPGSign.key --verify .checksum.md5.asc .checksum.md5
        gpg2 --verify .checksum.md5.asc .checksum.md5
        --------------------------------------------
        gpg2 --no-armor output SAPGlobalGPGSign.gpg --export 9ED95FE6
        gpg2 --armor --output .checksum.md5.asc --batch --passphrase "Pa\$\$w0rd" --detach-sig .checksum.md5
        gpg2 --no-default-keyring --keyring /var/tmp/dbcatTrans/zfinal/SAPGlobalGPGSign.gpg --verify .checksum.md5.asc .checksum.md5
        --------------------------------------------
        gpg: [don not know]: invalid packet (ctb=78)
        gpg: keydb_search failed: Invalid packet
        gpg: Can't check signature: No public key

        --------------------------------------------
        gpg --list-keys
        gpg --delete-secret-key key-ID
        gpg --delete-key key-ID

        --------------------------------------------
        public key... don#t need at all...
        gpg2 -k
        gpg2 --armor --output gpg2-public-key.rsa --export donghee.kang@sap.com
        gpg2 --armor --export donghee.kang@sap.com | tee gpg2-public-key.rsa

        --------------------------------------------
        private key...
        gpg -a -o exportedKeyFilename.asc --export-secret-keys keyIDNumber
        gpg -a -o gpg_private_key.asc --export-secret-keys 60F9D6E1

        --------------------------------------------
        encypytion
        gpg --ouput a.txt.gpg --encrypt --recipient administrator@sap.com a.txt

        --------------------------------------------
        without passphrase......default output (--ouput a.txt.gpg)
        gpg2 -se --passphrase yourpassword --batch --encrypt --recipient xxxxxx@sap.com a.txt
        gpg2 --passphrase "Veritas47" --batch --encrypt --recipient donghee.kang@sap.com example2.txt

        --------------------------------------------
        descrytion
        This is important and have to be set into the file...
        gpg2 --output a.txt --batch --passphrase yourpassword --decrypt a.txt.gpg
        gpg2 --output a.txt --batch -no-default-keyring --secret-keyring /path/gpg_private_key.asc --passphrase yourpassword --decrypt a.txt.gpg





#### Stateless vs Stateful Firewall
    ===============================================================================================
    Stateless vs Stateful Firewall
    ===============================================================================================
    - Stateless firewall
	  treats each network frame or packet individually. Such packet filters operate at the
	  OSI Network Layer (layer 3) and function more efficiently because they only look at the
	  header part of a packet. They do not keep track of the packet context. Such a firewall has
	  no way of knowing if any given packet is part of an existing connection

    - A stateful firewall
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



#### Firewall

* scans each little packet of data
* physical(routers) or software
* can me exceptions by users

####	firewalld @ CentOS

####	iptables

#### Intrusion Detection Prevention (IDP)


#### Intrusion Detection System (IDS)
is based on a specific pattern to detect the attacker's intrusion

* connect to one of the port at a switch
* IDS determine whether the traffic that is going to the web service is dangerous. eg. compare the signatures, anomalies with in quantity and types.
* It does not stop the attack from happening. it simply alerts the attack

#### Intrusion Prevention System (IPS)
is an active defence to block the attacs detected

* plug Between the firewall and switch.
* can be virtual or physical
* prevent attack from the begin, protect the computer or server

#### Hosted Intrusion Detection/Prevention System (HIDS)
* a IDS/IPS system sometime cost money, if we want to just protect one server, we can run prevent system as an software in tha server
* We can install it into many devices in our networl. eg. routers, firewalls(eg. UTM(Unified Threat Management):Palo Alto, checkpoint)

####  Snort (IDS/IPS, NIDS)
is an open source network intrusion detection system (NIDS)
to detect a variety of attacks, such as buffer overflows, stealth port scans,
CGI attacks, SMB probes, OS fingerprinting attempts and much more.
listen direct to the Network card, while fail2ban is not.
Snort System can monitor traffic on the local network, since the network is well
portected by the NAT router, outside attacks against the local(samba, nfs, ssh)
are unlikely to reach the protected network, so external attacks won't be detected


####	OpenVAS (Open Vulnerability Assessment System)
is a framework of several tools offering a vulnerability management solution
over 30,000 in total


####	fail2ban (IPS)
Fail2Ban is then used to update firewall rules to reject the IP addresses for a
specified amount of time, scans log(e.g. /var/log/apache/error_log) and bans IPs that
show the malicious signs too many password failures, seeking for exploits, etc.


#### 	IPSec
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
	IPsec supports network-level peer authentication, data origin authentication,
	data integrity, data confidentiality (encryption), and replay protection.

	IPsec is an end-to-end security scheme operating in the Internet Layer of the
	Internet Protocol Suite, while some other Internet security systems in widespread use,
	such as Transport Layer Security (TLS) and Secure Shell (SSH), operate in the upper
	layers at the Transport Layer (TLS) and the Application layer (SSH). Hence, only IPsec
	protects all application traffic over an IP network. Applications can be automatically
	secured by IPsec at the IP layer.

- IPsec	related program
	sysctl+racoon
	isk_psk
	tcpdump
	then....do firewall setting
	iptables=netfilter=packetfilter
	HAProxy
	NAT


#### Malware

* Virus are a little piece of code, that can copy itself to other programs when triggered. corrupt datas. Often attached to an excutable file.
* Malware are software crashing systems, stealing important information.
* Trojans are harmful software that can steal information, user are usually lead to open the software.
* Ransomware host pc hostage, threatening to destroy data
* Spyware secretly gathers private information such as passwords
* Worms replicate themselves and attack other devices in the network, slowing down traffic and
* Malware today is an conclusion of all above and more.



#### Next-Gen Firewalls (NGFW)
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


#### VPN
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

    -OpenVPN
    	$ sudo apt-get update
    	$ sudo apt-get install openvpn easy-rsa
    	$ gunzip -c /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz \
     		> /etc/openvpn/server.conf

    	$ vim /etc/openvpn/server.conf
    	| dh2048.pem
    	| push "redirect-gateway def1 bypass-dhcp"

    	Options for Open
    	| Packet Forwarding ip_forward
    	| ufw...or iptables

    	Creating a Certificate Authority and Server-Side Certificate & Key
      RSA or IPSec
    	Generate a Certificate and Key for the Server
    	Move the Server Certificates and Keys
    	Generate Certificates and Keys for Clients
    	Transferring Certificates and Keys to Client Devices
    	Creating a Unified OpenVPN Profile for Client Devices


<div><br/>
&raquo; Back to <a href="#contents">Contents</a> | <a href="../docs/README.md">Docs</a>
</div>
