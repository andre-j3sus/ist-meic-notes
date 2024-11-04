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

- **EXTx** file system:
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

## Temporal Analysis

## Network Traffic Analysis

## Pitfalls in Network Addressing

## Investigation of Computer Networks

## Wireless Network Forensics

## Email and Web Forensics

---

## Deep Web and Anonymity

## Online Anonymity

## Botnets

## Rootkits

## Malware Analysis

## Cryptocurrency Investigations

## Mobile Forensics

## Cloud Forensics
