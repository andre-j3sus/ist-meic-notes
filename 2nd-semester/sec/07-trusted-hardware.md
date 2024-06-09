# Trusted Hardware

> Trusted hardware is a hardware component that can be **trusted** to perform a specific function **securely**. This trust is established by the hardware's ability to enforce **security properties**, such as confidentiality, integrity, and availability, even when the hardware is under the control of an adversary. Trusted hardware is used to **protect sensitive data** and to provide **secure execution environments** for applications.

### Trusted Computing Base (TCB)

- Set of **hardware, software, and firmware** components on whose correctness the **security of the system depends**;
- **Larger** TCBs are **harder** to secure;
  - e.g., a **smart card** has a **small TCB**;
  - e.g., OS has a **large TCB**.

---

## Smart Cards

- **Secure** way of storing a small amount of **sensitive data** and performing **secure operations** - **cryptographic** operations without exposing the **private key**;
- **Small TCB**;
- **Small** size, **cheap**, **low power**, small but **sufficient amount of memory + CPU horsepower**;
- Is made with plastic and has a **chip** inside;
- Applications: **banking**, **SIM cards**, **access control**, **healthcare**, **public transport**, **identity cards**;
  - e.g., Citizen Card in Portugal, stores personal data, like **private key**, and can be used to **sign** documents;
- Characteristics:
  - Simple processors: 8-32 bits;
  - Small memory:
    - 127kB of **ROM** - card's **operating system**;
    - 4kB of **RAM** - 'scratchpad' memory;
    - 128kb of **EEPROM** - **cryptographic keys**, PINs, biometric data, balance, etc.;
  - **Cryptographic coprocessors**: **RSA**, **DES**, **AES**;
  - Low manufacturing cost.

### Smart Card Security

- **Multi-factor authentication** - combines:
  - **Something you have**: the card;
  - **Something you know**: the PIN;
  - **Something you are**: biometrics;
- Ensured by 4 main components:
  1. **Operating System** - manages the card's resources and provides a **secure environment** for applications;
  2. **Card body** - **tamper-resistant** (hard to open without destroying it);
  3. **Chip hardware** - **secure** and **tamper-resistant**;
  4. **Application**.
- There are 3 main **attacks**:
  - **Physical attacks**:
    - **Active attacks** - attacker manipulates the data transmission process;
    - **Passive attacks** - attacker may make measurements on the semiconductors;
  - **Logical attacks**;
  - **Social level attacks**.

### Side Channel Analysis

- **Physical** attacks that exploit **information leakage** from the **implementation** of a **cryptographic algorithm** - **power consumption**, **electromagnetic radiation**, **timing**;
- Use an **oscilloscope** to measure the **power consumption** of the card;
- **Non-invasive passive** attacks;
- As a countermeasure, **energy consumption must be independent** of the data being processed, e.g., artificially adding noise to the power consumption.

---

## Trusted Platform Module (TPM)

- Standard for a **secure cryptoprocessor** running alongside the main processor;
- The goal is to ensure that the **machine boots** in a **trusted state**;
- **PCR** - Platform Configuration Registers - store **hashes** of the **boot process**;
  - **Remote attestation** - the TPM can **prove** to a **remote party** that the machine booted in a **trusted state**;
- The steps of a **secure boot**:

  1. Hardware computes hash of **BIOS** and stores it in PCR0;
  2. BIOS computes hash of **boot loader** and stores it in PCR1;
  3. Boot loader computes hash of **kernel** and stores it in PCR2;
  4. Kernel computes hash of **initrd** and stores it in PCR3;
  5. Kernel computes hash of **user space** and stores it in PCR4;
  6. User space computes hash of **applications** and stores it in PCR5;
  7. **Remote party** can **verify** the **chain of trust**;

- Highly criticized by **privacy advocates** because the owner of the machine **cannot control** the TPM - claims that it can be used for **DRM** - **Digital Rights Management**;
  - **Arguments against TPM**: TPMs constrain what users can do with their own hardware;
  - **Arguments in favor of TPM**: some situations the control is useful - e.g., modifying odometer before selling a car.

---

## Trusted Execution Environments (TEE)

- TPMs turned out to be fairly inflexible;
- **TEEs** allow for **strong confidentiality and integrity guarantees** for **applications** running on the **main processor** - not focus on availability;
- Same strong thread model: do not trust the owner of the machine;
- Supported by several processors:
  - **Intel SGX** - **SGX** stands for **Software Guard Extensions** - **enclaves**;
    - **Enclave** - a **protected region** of memory that is **encrypted** and **authenticated**;
  - ARM TrustZone;
  - AMD SEV.

### SGX Enclaves

- **Enclaves** are **protected regions** of memory that are **encrypted** and **authenticated**;
- Security mechanisms of enclaves:
  - **Isolation** - **enclave memory** cannot be seen (confidentiality) or modified (integrity) by the **OS** or **hypervisor**;
  - **Attestation** - the **enclave** can **prove** to a **remote party** that it is running in a **trusted environment**;
  - **Sealing** - **enclave** can **encrypt** data so that it can only be decrypted by the **same enclave** running on the **same machine**;
