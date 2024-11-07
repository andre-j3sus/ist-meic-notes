# [Forensics Cyber-Security](https://fenix.tecnico.ulisboa.pt/disciplinas/CSF211/2024-2025/1-semestre)

> **⚠️ Warning:** Sadly, I don't have availability in the moment to write these notes. As soon as I have some time, I will write them. If you want to contribute, please make a pull request.

## Syllabus

1. **Foundations of Digital Forensics**

   1. [Introduction](./1.01-introduction.md);
   2. [Legal Framework](./1.02-legal-framework.md) - cybercrime and admissibility of digital evidence;
   3. [Digital Investigation Process](./1.03-digital-investigation-process.md) - Kruse & Heiser model;
   4. [Evidence Acquisition](./1.04-evidence-acquisition.md);

2. **General Techniques and Tools for Digital Forensics**
3. **Specialized Techniques and Tools for Digital Forensics**

_(section under construction)_

---

---

# Key Concepts

## Legal Framework

- **Locard’s Exchange Principle**: _In the commission of a crime, the perpetrator will **bring something into the crime scene and leave with something from it**, and that both can be used as **forensic evidence**_;
  - Same applies in digital forensics. E.g., the criminal that visits a website leaves an entry in the logs of the server, and brings with him a cookie, or adds an entry in the browser history.
- Guidelines for evidence **admissibility in court**:
  - **Relevance**: evidence must be relevant to the case;
    - Should be **material** (directly related to the case) and **probative** (tends to prove or disprove a fact);
  - **Authenticity**: evidence must be what it purports to be - it should not be changed or tampered with;
  - **Credibility**: evidence must be supported by a witness - not hearsay, meaning that knowledge from secondary sources is inadmissible (exceptions to **hearsay rule** are business records and computer-generated data);
  - **Legally obtained**: evidence must be legally obtained - **chain of custody** must be maintained and a **search warrant** must be obtained.

## Digital Investigation Process

### Kruse & Heiser Model

1. **Assessment**: prepare a plan of action;
2. **Acquisition**: collect evidence;
   - Maintain **chain of custody** (with timestamps, signatures, and logs);
   - **Hashing** is used to ensure data integrity;
3. **Analysis**: search for and interpret evidence;
   - Types of evidence:
     - **Inculpatory** evidence is evidence that shows guilt;
     - **Exculpatory** evidence is evidence that shows innocence;
   - Forms of event reconstruction:
     - **Temporal**: timeline with main events;
     - **Relational**: relationships between events;
     - **Functional**: what was possible and impossible to do;
4. **Reporting**: report findings.

## Evidence Acquisition

### Forensic tools

- **Commercial tools** are ✅ rich in features, but ❌ expensive;
- **Open-source tools** are ✅ free, but ❌ not so user-friendly;
- Ideal properties:

  - **Comprehensiveness**: revel all relevant data;
  - **Accuracy**: output error must be minimal;
  - **Deterministic**: same input should always produce the same output;
  - **Verifiability**: results should be reproducible;

### Acquisition in Storage devices

- If you can take the device, **take it** to the lab for data extraction;
- Else, **make a copy** of the data on spot:
  - **Bitstream copy**: exact copy of the data in the device;
    - Use `dd` command in Linux;
    - ✅ Exact copy of the data, more complete;
    - ❌ Slower, more complex, requires more space;
  - **Logical copy**: select relevant files and copy them;
    - ✅ Faster, easier, requires less space;
    - ❌ May miss relevant data, less complete;
- Use **write blockers** to prevent data modification: only allows reading data, not writing:
  - **Hardware write blockers**: physical devices that connect to the storage device;
    - ✅ More reliable, less prone to software errors;
    - ❌ More expensive, less flexible;
  - **Software write blockers**: software that prevents writing to the storage device;
    - ✅ Cheaper, more flexible;
    - ❌ More prone to errors, less reliable;

### Handle a Computer

- If it's off, **leave it off** - files during boot are modified, or malware may be executed;
  - If it's a laptop, **remove the battery**, and take it to the lab;
  - If it's a desktop, **bring the disks** or **make a copy** of the data (boot from a forensic OS);
- If it's on:
  - Check if any **destructive process** is running - if so, **stop it by unplugging the computer**;
  - If it requires a password that you don't know, unplug the computer;
  - If you are logged in, collect volatile data (RAM, network connections, running processes);
    - If it's a Windows machine, **don't turn it off** because the disk may be encrypted by BitLocker - perform a copy of the disk live, or a logical copy of the data; You can also ask the system administrator the encryption key;
    - If it's a Linux machine, **turn it off**, boot from a forensic OS, and make a copy of the disk.

### Handle a Mobile Device

- **Power the device** in order to prevent it from turning off;
- **Disable network connections** to prevent remote wiping - enable **airplane mode** is the best option.

---

## Forensic Data Decoding, Steganography and Watermarking

- Use `file` command to identify the type of a file: it analyzes the **magic numbers** of the file;
- Use `strings` command to extract readable text from a binary file;
- If the file is **corrupted**, try to repair it by checking the metadata;
- **Steganography**: hide a secret (piece of information) within another file;
  - Hide secret after EOF;
  - Hide secret in metadata;
  - Hide secret in the **least significant bits** of the the color channels of an image;
    - **Least significant bits** are the bits that have the least impact on the color of the pixel;
    - E.g., in a 24-bit image, that means that it has 3 color channels (RGB), each with 8 bits, and the last bit of each channel is the least significant bit, so we can hide 3 bits per pixel;
    - How to protect against passive attacks?
      - **Shifting**: shift the bits of the secret message;
      - **Random walk**: change the order of the bits, using a **key** to decrypt the message;
- **Steganalysis**: detect the presence of hidden information;
  - **Structural analysis**: analyze the structure of the file;
  - **Statistical analysis**: analyze the statistics of the file;
  - **Visual analysis**: analyze the visual content of the file;
- **Watermarking**: objective is to prove the ownership of something;
  - Can be **visible** or **invisible**;
  - Several techniques, like LSB embedding (similar to steganography, we place the watermark in the least significant bits of the image);

| **Steganography**                      | **Watermarking**    |
| -------------------------------------- | ------------------- |
| Hide information                       | Prove ownership     |
| Robustness not required                | Robustness required |
| Always invisible                       | Can be visible      |
| Capacity (amount of data) is important | -                   |

## Memory Analysis

- **Volatile data**: data that is lost when the system is turned off; Potential evidence:
  - Running processes - `ps` command;
  - Network connections;
  - Open TCP/UDP ports;
  - Memory mapped files;
  - Caches;
  - Encryption keys;
  - ...
- Use `mdd` tool to extract data from memory;
- There's one **page directory** per process, and a **page table** per page directory - the page table maps the virtual memory to the physical memory;
- Interpretation of memory dumps:
  - **Tree/list traversal**: follow pointers to find data structures of interest;
    - ✅ Can stitch together more related data;
    - ❌ May miss data that is not linked;
  - **Fingerprinting/Pattern matching**: search for known patterns in memory;
    - ✅ Find unlinked data;
    - ❌ Susceptible to rubbish data;
- **Fileless malware**: malware that doesn't write to disk, only to memory;
- Acquisition of volatile memory:
  - Software-based
    - **User-level tools**: use tools for memory dumping;
      - ✅ Easy to use
      - ❌ OS dependent
  - **Kernel-level drivers**: use drivers to access memory; e.g., **LiME**;
    - ❌ Causes changes in the system
  - Hardware-based
    - **Warm boot attack**: reboot the system but power is never removed from the memory module;
    - **Cold boot attack**: freeze the memory module and extract it;
    - **PCIe DMA**: use a dedicated hardware card to access memory;
      - ❌ Expensive and requires prior installation
    - **Special hardware bus**: FireWire, Thunderbolt, etc.
      - ❌ Expensive and requires prior installation, and only permits acquisition of < 4GB of memory

## Storage and Volume Analysis

- `mmls` command to list partitions in a disk image;
- **MBR** (Master Boot Record) is the first sector of a disk, containing the **partition table**, the **bootloader**, and the **disk signature**;
  - **Partition table**: contains the information about the partitions in the disk - **partition entry** contains the **starting sector** and the **size** of the partition;
  - **Bootloader**: code that loads the OS;
  - **Disk signature**: unique identifier of the disk;
  - Supports up to 4 primary partitions, but one can be an **extended partition** that contains **logical partitions**;
- `dd` command to extract a partition from a disk image;
- Disk **slack space**: space between the end of the file and the end of the cluster;
- Disk can be **encrypted** - full volume encryption - e.g., BitLocker encrypts NTFS volumes (Windows);
  - Search for **encryption keys** in memory;
- SSDs are more challenging to analyze:
  - Difficult to recover deleted data - garbage collection;
  - **Wear leveling** - data is spread across the disk;

## File System Analysis Techniques

File system evidence can be grouped into categories:

- **Content category**: file content; techniques:
  - Data unit viewing - `dcat` command;
  - Logical file search - `sigfind` command;
  - Unallocated space search - `dls` and `dcalc` commands;
  - Consistency checking;
- **Metadata category**: file metadata (location, size, timestamps, etc.);
  - Metadata viewing - `istat` command;
  - Logical file viewing - `icat` command;
  - Logical file searching;
  - Unallocated space search - `ils` command;
  - Attribute search and sorting - `ifind` command;
  - Consistency checking;
- **File name category**: file names and directories;
  - File name listing - `fls` command;
  - File name searching - `ffind` command;
  - Consistency checking;
- **File system category**: file system structure (clusters, blocks, etc.);
  - `fsstat` command to view file system statistics;
- **Application category**: application-specific data, journals, etc.;
  - `jls` command to view journal entries;

| Category    | EXTx              | NTFS                  |
| ----------- | ----------------- | --------------------- |
| Content     | Blocks            | Clusters              |
| Metadata    | Inodes            | MFT entries           |
| File name   | Directory entries | $FILE_NAME attributes |
| File system | Superblock        | Boot sector           |
| Application | Journal           | Journal               |

### EXTx File System

- File contents are stored in **blocks**;
- Each file has an **inode** that contains metadata: file type, permissions, timestamps, etc.;
  - Contains **pointers** to **direct blocks** and **indirect blocks** (which contain pointers to data blocks);
- Filesystem divided into:
  - **Superblock**: contains metadata about the filesystem;
  - **Block group descriptor table**: contains metadata about the block groups;
  - **Block bitmap**: contains information about the blocks;
  - **Inode bitmap**: contains information about the inodes;
  - **Inode table**: contains the inodes;
  - **Data blocks**: contains the file contents.

| Layer            | Tool Prefix | Tools                           |
| ---------------- | ----------- | ------------------------------- |
| Content          | `d`         | `dcat`, `dls`, `dcalc`          |
| Metadata         | `i`         | `istat`, `icat`, `ils`, `ifind` |
| File name        | `f`         | `fls`, `ffind`                  |
| File system      | `fs`        | `fsstat`                        |
| Application      | `j`         | `jls`                           |
| Media Management | `mm`        | `mmls`                          |
| Image            | `img`       | `img_stat`                      |

### NTFS File System

- File contents are stored in **clusters**;
- **Master File Table (MFT)** contains an entry for each file, containing metadata;
  - Small files are stored directly in the MFT entry, larger files have pointers to data clusters;
- $DATA attribute contains the file contents;
  - A file can have multiple $DATA attributes;
  - The first $DATA attribute is the **default data stream**;
  - **Alternate data streams** can be used to hide data: `file.txt:stream.txt`;

## File Carving

- Goal: recover files from a disk image without the file system;
- **Fragmentation**: files are split into fragments and stored in different locations;
  - Fragmented files are younger than non-fragmented files;
- Techniques:
  - **One-pass carving**: search for file headers and footers - **structure-based carving**;
    - While True:
      - Read a block;
      - Search for a header;
      - If found, search for a footer (must be from same file type);
      - If found, save the file;
  - **Content-based carving**: search for file content;
  - **Parallel Unique Path (PUP)**: identify the headers of all files; for each header, search for best match using an heuristic; if two matches are found, choose the one with the highest score;
    - ✅ Realistic and effective
    - ❌ Errors propagate in cascade
  - **Bifragment Gap Carving (BGC)**.

## Evidence in Operating Systems

### Windows Registry

- Database that stores configuration settings; virtually, \*_everything_ done on a Windows system is recorded in the registry;
- Contains:
  - System configuration;
  - Devices on the system;
  - User profiles;
  - Personal settings and preferences;
  - Web browsing activity;
  - Files opened;
  - Programs executed;
  - Application settings;
- Organized in key-value pairs:
  - **Hives**: logical group of keys;
    - `HKEY_LOCAL_MACHINE`: hardware and software configuration;
    - `HKEY_CURRENT_USER`: user-specific settings;
    - `HKEY_CLASSES_ROOT`: file associations;
    - `HKEY_USERS`: user profiles;
    - `HKEY_CURRENT_CONFIG`: hardware profile;
  - **Keys**: folders;
  - **Values**: data;
  - **Types**: REG_SZ, REG_DWORD, REG_BINARY, etc.;
- **MRU** lists: Most Recently Used lists, contain entries about the most recently used files, programs, etc.;
  - `RunMRU` contains the most recently executed programs through the Run dialog;
  - `OpenSaveMRU` contains the most recently opened/saved files;
  - `LastVisitedMRU` contains the executable used by an application to open the files documented in `OpenSaveMRU`;
  - `UserAssist` contains information about the programs executed by the user;
  - `RecentDocs` contains the most recently opened files - used to populate data in "Recent" in the Start menu;
- Forensic analysis:
  - `regedit` to view the registry;
  - `RegRipper` to copy the entire registry;
  - `FTK Imager` to extract copies of registry files.

### Windows Event Logs

- **Event logs**: records of events that happen on a system;

## Temporal Analysis

- **Temporal analysis**: analyze the timeline of events;
- Computer timekeeping:
  - **Real-time clock**: hardware clock that keeps time when the system is off;
  - **System time**: time kept by the OS;
  - **Network Time Protocol (NTP)**: protocol to synchronize time across systems;
  - **Network Identity and Time Zone (NITZ)**: protocol to synchronize time across mobile devices;
  - **GPS**: Global Positioning System, used to synchronize time;
- **MAC times**:
  - **Modified time**: last time the file was modified;
  - **Accessed time**: last time the file was accessed;
  - **Changed time**: last time the file metadata was changed;
- **Timestomping**: changing the MAC times of a file;
  - A way to detect, is to check if the timestamp has resolution beyond seconds;
  - Check for inconsistencies between timestamps;

## Network Traffic Analysis

### Packet Analysis

- Analyze the packets exchanged in a network;
- **Packet sniffing**: capture packets from the network;
  - `tcpdump` command;
- Main techniques include:
  - Parsing protocol fields;
  - Packet filtering;
  - Pattern matching;
- Inspecting individual packets:
- **Port scan attacks**: scan for open ports in a system;
  - **SYN scan**: send a SYN packet to the target, if the port is open, the target responds with a SYN-ACK packet - the attacker doesn't need to complete the handshake;
  - **UDP scan**: send a UDP packet to the target, if the port is open, you will not receive nothing, but if the port is closed, you will receive an ICMP packet;
- **DDoS attacks**: Distributed Denial of Service attacks;
  - UDP flood: send a lot of UDP packets to the target;
  - ICMP flood: send a lot of ICMP packets to the target;
  - SYN flood: send a lot of SYN packets to the target;

### Flow Analysis

- A **flow** is a sequence of packets that share the same source and destination;
- **NetFlow**: Cisco's flow analysis protocol;
  - Since it is uni-directional (e.g. for a TCP connection, there are two flows), it reduces the amount of data to be analyzed and it's simpler to identify suspicious traffic;

### Protocol Analysis

- Search for values typical associated with a protocol;
  - Sequence of bits;
- Leverage TCP/UDP port numbers;

## Pitfalls in Network Addressing

- **MAC addresses**: only valid in the local network;

  - Packets sent to the Internet do not contain MAC addresses;
  - Can be **modified**;
  - **ARP poisoning**: attacker sends fake ARP packets to the network, associating the attacker's MAC address with the IP address of the gateway;
    - _ARP (Address Resolution Protocol)_: protocol to map IP addresses to MAC addresses;

- **IP addresses**: beware of their scope:

  - **private IP addresses** have local scope (e.g. `10.0.0.0/8`, `172.16.0.0/12`, `172.168.0.0/16`);
  - **public IP addresses** have global scope, assigned by the ISP;
  - Some change, some don't:
    - **Dynamic IP addresses**: change over time, by DHCP (Dynamic Host Configuration Protocol);
    - **Static IP addresses**: don't change;
  - In unidirectional communication, the source IP is less reliable, as it can be **spoofed**;
  - Can be masked by **middleboxes**:
    - **Web proxies**: forward requests to the Internet;
    - **Firewalls**: filter traffic;
    - **NAT (Network Address Translation)**: allows multiple clients using private IP addresses to access the Internet through a single public IP address;
      - Invented to solve the problem of the exhaustion of IPv4 addresses;
      - In forensics, it makes it difficult to trace the origin, if you don't have access to the NAT table;
    - **Load balancers**: distribute traffic across multiple servers;
    - **VPN (Virtual Private Network)**: encrypts traffic and hides the source IP address;
      - **Tunneling**: encapsulate packets in another protocol;

- Interpreting ports:
  - **Port knocking**: to open a port, the client must send a sequence of packets to other ports;
    - Used to hide services from port scanners;
    - `knockd` is a tool to implement port knocking;

## Investigation of Computer Networks

- Determine IP and MAC addresses
  - `ifconfig` command;
  - `nslookup` command to resolve IP addresses based on DNS;
- Determining path between two hosts
  - `traceroute` command: client sends packets with increasing TTL, routers send ICMP packets back;
- **WhoIs**: service to determine the owner of an IP address;
- SYN scan: use `nmap` to scan for open ports;
  - Connect scan: complete the handshake;
  - FIN scan: send a FIN packet to the target, if the port is open, the target will not respond;
- **Banner grabbing**: retrieve the banner of a service; banner contains information about the service and version;
  - Use `nc` command to connect to a port and retrieve the banner;
- **Ping sweep**: send ICMP packets to all IP addresses in a range;
  - Use `nmap` with `-sn` flag (hosts up and available);
- **Vulnerability scanning**: use a database of signatures to detect vulnerabilities in a system;
  - Should be safe to run, so they don't exploit the vulnerabilities;
  - **Network Intrusion Detection Systems (NIDS)**: detect attacks in real-time; used by companies to protect their networks;
    - Can be used in investigations to detect attacks, check in the logs, etc.;
    - ❌ Can't inspect encrypted traffic;
    - ❌ High false positive rate;
  - **Security Information and Event Management (SIEM)**: collect logs from different sources and correlate them;
    1. Event generation by different sources (firewalls, IDS, etc.);
    2. **Event collection** by the SIEM;
    3. **Normalization** of the events: convert to a common format;
    4. **Enrichment** of the events: add context;
    5. **Transportation**: caching, encryption, compression;
    6. **Indexing** of the events in a database;
    7. **Analytics and correlation** of the events.

## Wireless Network Forensics

### Wi-Fi network investigations

- **Wi-Fi** (Wireless Fidelity) networks are based on the **IEEE 802.11** standard;
- Client communicated with the **Access Point (AP)** or **Base Station (BS)**;
- Common attacks:
  - **Man-in-the-Middle (MitM)**: attacker intercepts the communication between the client and the AP;
  - **Deauthentication attack**: attacker sends deauthentication packets to the client, forcing it to disconnect from the AP;
  - **Evil Twin**: attacker creates a fake AP with the same SSID as the legitimate AP;
  - **Decryption attacks**: attacker captures the traffic and decrypts it - e.g., **WEP** (Wired Equivalent Privacy) encryption is weak;
  - **Wardriving**: driving around to find un-protected Wi-Fi networks;
  - **Rogue AP**: unauthorized AP in the network;
  - **Packet sniffing**: capture packets from the network;
- Evidence from wireless APs:
  - Logs, connection attempts;
  - Track physical movements of a suspect;
  - How attacker gained access to the network;
- Location tracking:
  - **Proximity** to the BS that the device is connected to (use signal strength);
    - ✅ Low const
    - ❌ Low accuracy
  - **Trilateration** signal strength from multiple BS;
    - We can also use **Time Difference of Arrival (TDoA)**;
    - ✅ Better accuracy
    - ❌ May not be possible in low-density areas
  - **Triangulation** signal strength and angle of arrival;
    - ✅ Only needs two BS
    - ❌ Low tolerance to signal interference
  - **Fingerprint**: collect signal strength from multiple locations and create a fingerprint;
    - ✅ High accuracy
    - ❌ Needs maintenance
  - GPS, 5G, etc.

## Email and Web Forensics

### Email

- **Email spamming**: sending unsolicited emails;
- **Email bombing**: sending a large number of emails to a target;
- **Phishing**: sending emails pretending to be from a legitimate source to steal information - redirect to a fake website;
- **Email spoofing**: changing the email header to make it look like it was sent by someone else;
  - **Spear phishing**: targeted phishing - the apparent source is someone the target knows, likely someone in the same organization with authority;
  - **Whaling**: phishing targeted at high-profile targets;
- Helpful information in email headers:
  - Sender of the email;
  - Network path of the email - SMTP servers;
    - Check the `Received` header: the bottom one is the first server that received the email, and the top one is the last server that sent the email;
    - Look for breaks in the chain, which may indicate tampering;
    - Verify all IP addresses;
    - Make a time-line of events;
  - Timestamps;
  - Email client information;
  - Encoding information;
- Antiforensics:
  - **Open relays**: SMTP servers that allow anyone to send emails - it does not need to be a known user;
    - But the attacker's IP address is in the email header;
  - Fake email headers.

### Web

- **Code injection**: inject code in a website to steal information;
  - **SQL injection**: inject SQL code in a form to retrieve information from the database;
  - **Cross-site scripting (XSS)**: inject JavaScript code in a form to steal cookies;
  - **Cross-site request forgery (CSRF)**: trick the user into performing actions in a website;

---

## Deep Web and Anonymity

- **Surface Web**: indexed by search engines;
- **Deep Web**: not indexed by typical search engines;
  - **Dynamic content**: content generated on the fly;
  - **Unlinked content**: content not linked to any other page;
  - **Private content**: content behind a paywall;
  - **Limited access content**: sites with captchas, etc.;
  - Content not found, by using `robots.txt`, etc.;
- **Dark Web**: part of the Deep Web with criminal activity;
- There are specialized search engines for the Deep Web;
  - **Shodan**: lets user find specific types of devices connected to the Internet; many devices use default passwords;
    - **Randomly** picks IP addresses and tries to connect on different ports;

### Anonymity

- Why to be anonymous?
  - Hacker, malicious actor, terrorist;
  - Journalist;
  - Human rights activist;
  - Military;
  - ...
- It's hard to be anonymous:
  - Your IP address can be linked to you;
  - Your browser can be tracked;
  - Wireless traffic can trivially intercepted;
- Types of Anonymity:
  - **Sender anonymity**: source unknown;
  - **Receiver anonymity**: destination unknown;
  - **Sender-receiver anonymity**
- Approaches for anonymity:
  - Anonymity proxies
  - **Anonymity networks**: forward traffic through a chain of network nodes, called **relays**;
    - **Tor** adopts some ideas of **onion routing**: use PK cryptography to establish a circuit with pairwise symmetric keys;
      - The list of Tor relays is named **consensus**;
      - Tor establishes connections one hop at a time, using TLS;
      - Supports **Hidden services** - running a server without disclosing the IP or DNS name;
- Investigation in anonymity networks:
  - **Probe entry and exit relays**: correlate traffic by volume or timing;
  - **Predecessor attack**: investigator controls two relays in the circuit;
  - **Guard relays** prevent attackers from becoming the first relay: Tor client selects 3 guard relays that it uses for 3 months, and after that, 3 new guard relays are selected;
  - Tor does not provide e2e encryption, so the **exit node can see the traffic**;

## Botnets

- Collection of **bots** that run on host computers and are controlled remotely by an attacker;
- Methods od infecting botnet nodes:
  - **Email**: send an email with a malicious attachment;
  - **Pirated software**: distribute software with malware;
  - **Drive-by downloads**: visit a website that downloads malware;
- Once the bot is installed, it typically installs what is known as a **backdoor** - a program that allows the attacker to control the bot;
- Trail obfuscation techniques:
  - Piggyback on existing protocols: use protocols that are already allowed by the firewall;
    - **IRC (Internet Relay Chat)**: use IRC to control the bots;
  - **Fast flux and DGA (Domain Generation Algorithm)**: change the IP address of the C&C server frequently;
  - P2P communication;
  - Encryption;
  - Rootkits;
- Centralized communication is easier to detect that decentralized communication;
- Investigation of botnets:
  - Identify the C&C server and take it down;
  - Use antivirus software to remove the bot;
  - **Honeypots**: fake servers that attract attackers;

## Rootkits

- **Malware**: malicious software;
- **Exploits**: malicious code that takes advantage of a vulnerability;
  - **Exploit kits**: tools that automate the process of finding and exploiting vulnerabilities;
  - **Common vulnerabilities and Exposures (CVE)**: list of known vulnerabilities;
- **Rootkits** are a category of malware which has the ability to hide itself from the operating system;
  - Stealth techniques:
    - **File masquerading**: replace system files with malicious ones;
      - Use filenames like `.` or `..` to hide files;
      - Detect: check the file size, hash, etc.; compare `ps` output with `/proc` directory;
    - **Change system commands**: modify system commands to hide the rootkit; `ifconfig` does not show the network interfaces;
    - **Hooking**: intercept system calls and modify their behavior;
      - **Library-level hooking**: modify the library functions;
      - **Kernel-level hooking**: modify the kernel functions: the rootkit gains complete control over the machine, since it runs in supervisor processor mode;
      - Detect: check for inconsistencies in the output of tools or in their behavior;
    - **Direct Kernel Object Manipulation (DKOM)**: manipulate kernel data structures to hide processes, files, etc.;
    - **Virtualization**: run the OS in a virtual machine, and the rootkit in the host machine, to hide itself;

## Malware Analysis

- **Static analysis**: analyze the malware without running it;
  - Hash the file and check it against a database;
  - Virus scan;
  - List strings;
  - Inspect raw bytes;
  - List symbols;
  - View shared objects;
  - Disassemble or decompile the code;
  - **Anti-static analysis techniques**:
    - Disassembly desynchronization: modify the disassembly output;
    - Opcode obfuscation: encode or encrypt the actual instructions;
- **Dynamic analysis**: run the malware in a controlled environment;
  - Use old PCs in an isolated network;
  - Use virtualization techniques;
    - But, the attacker can change it's behavior if detects that it's in a virtual machine;
    - Your IP might become a target for the attacker;
    - Virtualization software is not perfect;
  - Monitor file changes, network activity, etc.;
  - **Anti-dynamic analysis techniques**:
    - Detect virtualization: check for the presence of virtualization software;
    - Detect instrumentation: detect if it's being monitored;
    - Detect debugger: detect if it's being debugged and generate various exceptions when a SEH is set.

## Cryptocurrency Investigations - Bitcoin

- Every account address has an associated **public-private key pair** for signing transactions;
- Trail obfuscation techniques:
  - Bitcoin addresses are not mapped to the real user identity;
  - Bitcoin transactions don't contain personal information;
  - IP address of client not included in new transactions;
- Analysis of the Bitcoin protocol and network:
  - **Protocol sniffer**: Bitcoin protocol is not encrypted - establish relationship between Bitcoin address and IP address.
  - **Sybil attack**: investigator attempts to fill the network with nodes controlled by him to control the network.

> Check the [Highly Dependable Systems](../../2nd-semester/sec/README.md) course for more information about blockchain and bitcoin.

## Mobile Forensics - Android

- How is data stored?
  - **Shared preferences**: key-value pairs; many apps use them to store sensitive data;
  - **Internal storage**: data stored in the app's private directory;
  - **External storage**: data stored in the SD card;
  - **SQLite databases**: relational databases;
  - Network data;
- Evidence extraction from Android devices:
  - Challenges:
    - Cannot remove persistent memory off the device;
    - The OS can restrict data access;
    - Device linked to the outside world via wireless networks;
    - Depends on battery for power supply;
  - **Smudge attack**: attacker can see the pattern of the screen lock;
  - Isolate by putting the device in **airplane mode**;
  - **Logical acquisition**: extract data from the device using the device's OS; contacts, call logs, messages, etc.;
  - **ADB (Android Debug Bridge) data acquisition**: extract data from the device using the ADB tool;
  - Screen capture;
  - **Physical techniques**:
    - Software: Techniques: extract data using imaging tool, like `dd`;
    - Hardware: **JTAG (Joint Test Action Group)**: use JTAG interface to access the device memory;
      - **Chip-off**: remove the memory chip from the device and read it;

## Cloud Forensics

- Challenges:
  - Storage is distributed;
  - Obtaining a warrant is difficult;
  - Most computation in cloud runs on virtual machines, that get wiped after use;
  - Few tools available.
