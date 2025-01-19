# [Software Security](https://fenix.tecnico.ulisboa.pt/disciplinas/SSof11/2024-2025/1-semestre)

> **⚠️ Warning:** Sadly, I don't have availability in the moment to write these notes. As soon as I have some time, I will write them. If you want to contribute, please make a pull request.

## Syllabus

#### Language Based Security

1. Introduction to Information Flow Security
2. Definition of Security Properties
3. Program Analysis for Security
4. Static Analysis
5. Dynamic Analysis
6. Security Verification and Bug-finding

#### Vulnerabilities and Secure Software Development

1. Basic Concepts
2. Protection in Operating Systems
3. Race Conditions
4. Web Vulnerabilities
5. Database Vulnerabilities
6. Validation and Encoding
7. Buffer Overflows
8. Input Validation and Format String Vulnerabilities
9. Dynamic Protection

---

---

# Key Concepts

## Language Based Security

### Introduction to Information Flow Security

- **Language-based security**: techniques based on programming language theory and implementation, including semantics, types, optimization and verification, brought to bear on the security question;
- **Tracking Information Flow**
  - **Perl's Taint Mode**: a mode that marks all data as **tainted**, and only allows it to be used in certain ways; tainted variables **taint** variables explicitly calculated from them; tainted data cannot be used in any **sensitive operation**;
- **Information flow policies**: specify how information should be allowed to flow between objects of each security class; we need **security labels**, **can-flow** relations between labels, and and **operator** to combine them: `(SC, →, ⊕)` where `SC` is the set of security classes, `→` is the can-flow relation, and `⊕` is the operator;
  - **Confidentiality**: prevent unauthorized disclosure of information;
    - **Confidentiality classes** determine who has the right to **read**;
    - Information **can only flow** towards confidentiality classes that are **at least as secret**;
    - Information that is derived from the **combination of two security classes** must take a confidentiality class that is **at least as secret** as each of them: `secret AND public = secret`- **intersection**
  - **Integrity**: prevent unauthorized modification of information;
    - **Integrity classes** determine who has the right to **write**;
    - Information **can only flow** towards integrity classes that are **no more trustworthy**;
    - Information that is derived from the **combination of two integrity classes** takes an integrity class that is **no more trustworthy** than each of them: `trusted OR untrusted = untrusted` - **union**
  - **Partial orders**:
    - **Reflexivity**: `A → A`;
    - **Transitivity**: `A → B` and `B → C` implies `A → C`;
    - **Anti-symmetry**: `A → B` and `B → A` implies `A = B`;
    - **Hasse diagram**: a diagram that represents the partial order; can-flow relations are non-directed arrows, implicitly directed **upwards**;
- **Access Control to Information Flow Control**
  - **Discretionary Access Control (DAC)**: the owner of the object defines the access control;
  - **Mandatory Access Control (MAC)**: the system administrator defines the access control;
- **Encoding and exploiting information flows**
  - **Object**: resource that can be accessed by a subject;
  - **Security class/label**: specifies who can access objects of that class;
  - **Security labelling**: the process of assigning security labels to objects;
  - `xL` denotes a variable `x` with security level `L`;
  - **No illegal flows** property: an attacker cannot infer secret input (confidentiality) or affect critical output (integrity) by inserting inputs into the system and observing its outputs.

### Definition of Security Properties

- **Noninterference, intuitively**: the program is secure if, for any two runs of the program that are given the same low inputs, if the program terminates, it produces the same low outputs;
  - **Deterministic Input-Output Noninterference**: an attacker is a program that is executed sequentially after the observed program, and has access to low outputs - only sensitive to outputs of **terminating** computations;
  - **Possibilistic Input-Output Noninterference**: an attacker is a program that is executed concurrently with the observed program, and has access to low outputs - sensitive to whether the program is capable of terminating and producing certain final outputs;
  - **Intermediate-step-sensitive Noninterference**: possible low outputs do not depend on yH, but the **intermediate steps** of the computation do;
  - **Temporal Noninterference**: the attacker can observe the program's behavior over time, and can distinguish between different low inputs;
  - **Probabilistic Noninterference**: sensitive to the likelihood of outputs;
  - Noninterference is simple and provides strong security guarantees, but sometimes we need to **leak** some information to the attacker (e.g. if password is correct or not);
- **Formal semantics**
  - We will use two techniques:
    - **Denotational semantics** for expressions: **what** is the result of a computation;
    - **Operational semantics** for instructions/statements: **how** the computation is done;
  - **WHILE language**: a simple language with **assignment**, **conditionals** and **loops**:
    - Syntactic categories:
      - `c`: constants (integers);
      - `x`: variables;
      - `a`: arithmetic expressions;
      - `t`: tests;
      - `S`: statements;
    - Grammar:
      - `op ::= + | - | * | /`;
      - `a ::= c | x | a op a`;
      - `cmp ::= = | < | > | ≤ | ≥`;
      - `t ::= a cmp a`;
      - `S ::= x := a | S; S | if t then S else S | while t do S`;
    - Functions:
      - `A`: maps pairs of arithmetic expressions and states to integers;
      - `B`: maps pairs of tests and states to booleans;
      - `S`: partial function that maps pairs of statement and state to states;
        - We will use **big-step transitions** to define `S`: `<S, p> → p'`;
          - **Skip** rule: `<skip, p> → p` - does nothing;
          - **Assignment** rule: `<x := a, p> → p[x |-> A(a, p)]` - assigns the value of `a` to `x`;
          - **Sequential composition** rule: `<S1; S2, p> → p''` if `<S1, p> → p'` and `<S2, p'> → p''`;
          - **Conditional statement** rule: `<if t then S1 else S2, p> → p'` if `<S1, p>` and `B(t, p) = true`, or `<S2, p>` and `B(t, p) = false`;
          - **While loop** rule: `<while t do S, p> → p''` if `<if t then S; while t do S else skip, p> → p''`;
        - Later we will use **small-step transitions** to define `S`: `<S, p> → <S', p'>`;
- **Formalization of noninterference**: A program `S` is secure if for every security level `L` and for all pairs of memories `p1` and `p2` such that `p1~Lp2` we have that `<S, p1> → p1'` and `<S, p2> → p2'` implies `p1'~Lp2'`;

### Program Analysis for Security

- **Enforcement mechanism**: a mechanism that aims at preventing any given program from performing unwanted behaviors;
  - **Program analysis**: process of **automatically** analyzing the behavior of computer programs;
    - Limitations:
      - **Scope**: designed to look for a finite set of problems;
      - **Limited precision**: for many properties, a decidable analysis cannot be precise, its either;
        - **unsound**: too permissive (has false positives);
        - **incomplete**: too restrictive (has false negatives);
- **Power and limitations of program analysis**
  - **The halting problem**: it is **undecidable** to determine if a program will halt (terminate) or not;
  - **Rice's theorem**: it is **undecidable** to determine if a program has a non-trivial property;
  - **Precision**:
    - **False positives**: the analysis reports a problem that does not exist;
    - **False negatives**: the analysis does not report a problem that exists;
    - Considering `S` the set of **secure programs** and `A` the set of **accepted programs**, we have:
      - **Sound**: `A ⊆ S` - all accepted programs are secure - **no false negatives**;
      - **Complete**: `S ⊆ A` - all secure programs are accepted - **no false positives**;
      - **Precise**: `A = S` - no false positives and no false negatives;
    - **Timing**:
      - **Static analysis**: analyzes the program without executing it;
        - Static time overhead;
        - Can defect problems earlier in the development cycle;
        - E.g. control-flow analysis, data-flow analysis, etc;
      - **Dynamic analysis**: analyzes the program while executing it;
        - May impose a cost on execution efficiency, due to runtime checks;
        - Can take advantage of runtime knowledge; Can find problems that are hard to detect statically;
        - E.g. testing, profiling, etc;
      - **Hybrid analysis**: combines static and dynamic analysis;
- **Static analysis mechanisms**
  - Standard compilation stages:
    - **Lexical analysis**: breaks the program into tokens;
    - **Preprocessing**: includes header files, etc;
    - **Semantic analysis**: builds a syntax tree;
    - **Intermediate code generation**: generates an intermediate representation of the program;
    - **Optimized**;
    - **Code generation**: generates the final code;
  - Analyzers:
    - **String matcher**: directly on the source code; e.g. `grep`;
    - **Lexical analyzer**: runs over the tokens generated by the scanner (does not confuse a variable `getshow` with a call to `gets`); e.g. `Flawfinder`;
    - **Semantic analyzer**: runs over the syntax tree generated by the parser (does not confuse a variable `gets` with a call to `gets`- same name but different meaning);
      - **Control fow analysis**: performs checks based on the **possible paths**;
      - **Data flow analysis**: performs checks based on the **possible values**;
      - **Type checking**: verify if the program is **type-safe**;
  - **Interactive analysis**:
    - **Model checking**: checks a model of a program, or the code itself;
      - A **model** is a description of the system, based on states and possible transitions between them;
      - Check if bad states cannot be reached (**safety**), or if good states are always reached (**liveness**);
    - **Program verification**: formally proves a property about a program;
- **Lattice policies**: a class of common information flow policies have some convenient ingredients:
  - Security levels form a partial order;
  - Two security levels can always be combined;
  - There is a highest and a lowest level;
  - Lattice of **confidentiality levels**: `L = (L, ⊑, ⊔, ⊓, ⊤, ⊥)`;
    - `L` is the set of security levels;
    - `⊑` is the **partial order** relation;
    - `⊔` is the **join** operator;
    - `⊓` is the **meet** operator;
    - `⊤` is the **top** element;
    - `⊥` is the **bottom** element;
  - **Upper-bound**: given a partially ordered set (`L, ⊑`), an element `s` is an upper bound of two elements `l1` and `l2` if `l1 ⊑ s` and `l2 ⊑ s`;
  - **Lower-bound**: given a partially ordered set (`L, ⊑`), an element `s` is a lower bound of two elements `l1` and `l2` if `s ⊑ l1` and `s ⊑ l2`;
  - **Join (`⊔`)**: the **least upper bound** of two elements `l1` and `l2` is an element `s` such that `l1 ⊔ l2 ⊑ s`;
  - **Meet (`⊓`)**: the **greatest lower bound** of two elements `l1` and `l2` is an element `s` such that `l1 ⊓ l2 ⊑ s`;

### Static Analysis

1. Definition of the language: **`WHILE` language** with **big-step semantics**;
2. Information flow **policy** of security levels: a **lattice `L` of security levels**;
   1. **High-Low policy** for **confidentiality**;
   2. **Low-High policy** for **integrity**;
   3. **Principal-based policy** for **confidentiality**;
   4. **Principal-based policy** for **integrity**;
3. Classification of objects into security levels: objects are variables that are given security levels by `Γ`;
4. **Security property** of programs: **Deterministic Input-Output Noninterference**;
5. Mechanism of selecting secure programs;
6. Guarantees about the mechanism;
   1. **Lemma 1 - Simple Security**: if `Γ ⊢ a : τ` then expression `a` contains only variables of level `τ` or lower;
   2. **Lemma 2 - Confinement**: if `Γ ⊢ S : τ cmd` then the statement `S` assigns only to variables of level `τ` or higher;
   3. **Theorem - Type Soundness**: if a program `S` is typable, then `S` satisfies Deterministic Input-Output Noninterference;
      - If `Γ ⊢ S : τ cmd` then **for every security level `l`** and memories `p1` and `p2` such that `p1~l p2` we have that `<S, p1> → p1'` and `<S, p2> → p2'` implies `p1'~l p2'`\*\*;

### Dynamic Analysis

- **Accepting vs. Transforming - mechanisms**
  - There are type systems that transform programs into secure programs;
- **Web security and Dynamic Languages**

  - **Same Origin Policy (SOP)**: a script loaded from one origin is not allowed to access or modify resources obtained from another origin;
  - Dynamic approaches to control information flow:
    - **Lock-step monitor**: modify JS engine so that it additionally implements the security monitor - monitor is **inlined`** into the program;
    - **Inlining compiler**: inline the monitor into the original program, which has the advantage of being browser-independent.

- **A monitor for Information ow analysis (Dynamic WHILE)**
  - **Small-step semantics**
  - **Labelled transitions**
  - **Lock-step information flow monitor**

### Security Verification and Bug-finding

- **Program Properties and Noninterference**
  - **Trace properties vs. Hyper properties**
- **Verification and Bug-finding for Noninterference**
  - **Self-composition + Symbolic Execution**

---

## Vulnerabilities and Secure Software Development

### Basic Concepts

- 3 main attributes of security (**CIA**):
  - **Confidentiality**: absence of information disclosure to unauthorized parties;
  - **Integrity**: absence of data modifications by unauthorized parties;
  - **Availability**: readiness of the system to provide services;
- **Vulnerability**: a system defect (SW or HW), that can be exploited by an attacker to subvert security policy.
  - `Attack + Vulnerability -> Intrusion`
  - **0-day vulnerability**: a vulnerability not publicly known:
  - **Exploit**: a piece of software that takes activates the vulnerability;
  - Types of SW vulnerabilities:
    - Design vulnerabilities: inserted during the design phase;
    - Coding vulnerabilities: inserted during the implementation phase;
    - Operational vulnerabilities: caused by the environment where the SW runs;
- **Attacks**:
  - **Technical** (e.g. buffer overflow) vs **Social Engineering** (manipulation of people);
  - **Directed** (specific target) vs **Undirected** (any target);
  - **Manual** (e.g. footprinting, scanning, enumeration) vs **Automated** (e.g. worms, viruses);
    - **Drive-by download**: a download that happens without the user's consent;
    - **Worm**: exploits vulnerabilities to steal sensitive information, install backdoors, etc;
    - **Virus**: similar to a worm, but propagated by physical contact (e.g. USB);
    - **Trojan**: a malicious program disguised as a legitimate one; user runs an infected program;
    - **Backdoor**: a hidden entry point to bypass security mechanisms; allows an attacker to access the system;
    - **Rootkit**: malicious program that hides at low level;
    - **Bot or RAT (Remote Access Trojan)**: listens at a port for commands from a **C&C (Command and Control)** server;
    - **Ransomware**: encrypts files and demands a ransom (money) to decrypt them;
    - **Cryptojacking**: uses the victim's computer to mine cryptocurrencies;
    - **Scareware**: tricks the user into thinking their computer is infected, making them buy fake antivirus software;
    - **Rogueware**: fake software that claims to be useful, but is actually malicious;
    - **Phishing**: a type of social engineering attack that tricks the user into revealing sensitive information;
      - **Spear Phishing**: a targeted phishing attack;
      - **Whaling**: a phishing attack targeted at high-profile individuals;
      - **Smishing**: phishing via SMS;
    - **Torpig**: a botnet that steals sensitive information;

### Protection in Operating Systems

- There are two aspects of protection to ensure that **objects** are not accessed by unauthorized **subjects**:
  - **Separation** - prevent arbitrary access to objects;
    - **Separation in OSes**: there are two modes of operation, enforced by the CPU:
      - **User mode**: restricted access to HW resources; SW needs to call the OS to make privileged operations;
      - **Kernel mode**: full access to HW resources;
      - In Linux, there is the **CPL (Current Privilege Level)**, which is a field in the CPU that indicates the current mode, using only two bits:
        - **0**: kernel mode;
        - **3**: user mode;
    - **Memory separation**:
      - **Physical**: different processes have different devices;
      - **Temporal**: different processes have different time slots;
      - **Logical**: processes operate under the illusion that no other processes exist;
        - **Segmentation**: program split into **segments** (code, data, stack); a process can access its own segments by checking its segment translation table; in Linux, segments are stored in the **GDT (Global Descriptor Table)**; each segment has a **DPL (Descriptor Privilege Level)**, which is a field that indicates the privilege level of the segment, and the user has access if `CPL <= DPL`;
        - **Paging**: program split into **pages**; a process can access its own pages by checking its page translation table; each page has **read/write** flags and **user/supervisor** flags saying if the page is accessible by the user or only by the kernel;
      - **Cryptographic separation**: processes use cryptography to conceal their data;
  - **Mediation** - **access control** to objects;
    - It must be **complete** (impossible to bypass), **isolation** (tamperproof) and **verifiable** (can be checked);
    - Basic Access Control mechanisms:
      - **Access control lists (ACLs)**: each **object** has a list of pairs `(subject, rights)`;
        - This is the most similar to the **UNIX** model, where each file has the permissions for the owner, group and others (9 bits, 3 for each with `rwx` permissions);
      - **Capabilities**: each **subject** has a list of pairs `(object, rights)`;
      - **Access Control Matrix**: a matrix where rows are subjects and columns are objects; each cell contains the rights of the subject over the object;
    - **UNIX Access Control Model**:
      - Each user has a **UID (User ID)** and a **GID (Group ID)**;
      - Objects are accessed by processes, which have an **EUID (Effective UID)** and an **EGID (Effective GID)**;
      - There are also two more access bits: **setuid** and **setgid**: allow access to resources the user cannot access;
    - But who defines the access control?
      - **Discretionary Access Control (DAC)**: the owner of the object defines the access control;
      - **Mandatory Access Control (MAC)**: the system administrator defines the access control;

> **Least Privilege Principle**: a subject should have the minimum set of privileges needed to perform its tasks.

### Race Conditions

- Violation of the **assumption of atomicity**;
  - Problem of concurrency;
  - Lack of synchronization;
  - The attackers races to break the assumption during the **window of opportunity**;
- There are mainly 3 types:
  - **Time-of-check to time-of-use (TOCTOU)**: the attacker changes the object between the check and the use;
    - Typical example: a program checks if the user has the right to access the file, and then opens it; the attacker changes the file between the check and the open, using a **symbolic link** (changes from a file with permissions to a file without permissions);
    - `access` syscall is vulnerable to TOCTOU; it checks using the **real UID** of the process;
    - Usually use a script that tries many times until the window of opportunity is found; or delay the program;
    - `lstat` function retrieves information about a link/file;
    - **Solution**: avoid using file names: use **file descriptors** instead, because if someone changes the link, the file descriptor will still point to the same file;
    - Unsafe: `access`, `stat`, `lstat`, `chmod` and `chown`;
    - Safe: `fstat`, `fchmod` and `fchown`;
  - **Temporary files**
    - Usually in a **shared directory** (e.g. `/tmp`);
    - Typical attack: privileged program checks if a file exists in `/tmp`; attacker races to create a link with the same name, to some file (e.g. `/etc/passwd`); the privileged program opens the file, thinking it is the temporary file;
    - Unsafe: `mktemp`, `tmpfile`, `tempnam`, `tempfile`;
    - Safe: `mkstemp`, `mkdtemp`: atomically checks for the existence of the file, creates it and opens it;
    - Solutions: use long random names, or use safe calls like `fopen`;
  - **Concurrency and reentrant functions**
    - Solutions: locks, mutexes, semaphores, transactions, etc;
    - Problems:
      - **Deadlocks**: two processes are waiting for each other;
      - **Starvation**: a process is always waiting for the lock;
      - **Race conditions**: two processes are trying to access the same resource;
    - **Reentrancy**: a function that it works correctly even if its thread is interrupted by another thread, that calls the same function;
    - Use **signals** and **servlets** (in Java).

### Web Vulnerabilities

- **XML Injection**: attacker injects XML code into the application;
- **PHP Injection**: attacker injects PHP code into the application;
  - `eval` function is vulnerable to PHP injection, allowing to execute arbitrary code;
- **Cross Site Scripting (XSS)**: allows an attacker to execute scripts in the victims browser; the victim **trusts** the website;
  - **Reflected XSS (non-persistent)**: page reflects user supplied data; **injected by the server**
    - E.g. an application that searches for a product, and the search term is reflected in the page;
    - Could be used to steal cookies (unless they are `HTTPOnly`, which prevents JavaScript from accessing them);
    - To obfuscate the script, the attacker can use **URL encoding**;
  - **Stored XSS (persistent)**: hostile data is stored in a database/file/etc and is later sent to the victim's browser;
    - E.g. blog applications, forums, etc;
  - **DOM-based XSS**: manipulates JavaScript code and attributes instead of HMTL;
    - E.g. `document.write` function;
  - Scripts do not have to be inside `<script>` tags; they can be in `onload`, `onerror`, `onmouseover`, etc;
  - **CRLF (Carriage Return Line Feed) Injection**: similar to reflected XSS, but injection is in the **response header** (in reflected XSS is in the **response body**)
    - Attacker inserts a CR and a LF in the header, to inject a new header, creating a new header field, or a second response (**HTTP response splitting**);
    - Similarly to reflected XSS, attacker sends victim a URL of a vulnerable website;
    - Typical on pages that perform **redirects**, making the browser believe that the second response comes from the redirection;
    - `%0D%0A` is the URL encoded version of CR and LF;
  - Protection against XSS:
    - **Input validation**: validate the input data;
    - **Output encoding**: encode the output data;
    - **Content Security Policy (CSP)**: a header that tells the browser what resources are allowed to be loaded;
      - `script-src <source>;` allows scripts from `<source>` and disables inline scripts;
      - `script-src unsafe-inline;` allows inline scripts;
      - `script-src 'nonce-<nonce>';` allows scripts with a specific nonce: `<script nonce="<nonce>">`;
  - **Session hijacking**: attacker steals the session cookie and uses it to impersonate the victim;
    - Solution: IDs have to be unpredictable and have an expiration time;
- **Cross Site Request Forgery (CSRF)**: attacker tricks the victim into performing actions on a website without their consent;
  - E.g. victim is logged in a bank website, and the attacker sends a link to a malicious website, with a script that calls the bank website to transfer money; the browser will execute the script automatically;
  - Protection: use **CSRF tokens**: a token that is sent with the request, and the server checks if the token is valid;
- **Direct Object Reference**: attacker manipulates the URL to access unauthorized data;
  - E.g. a URL like `http://example.com/user?id=1` allows the attacker to change the `id` parameter to access other users;
  - Solution: never expose **object references**;
- **Failure to Restrict URL Access**: some pages are "protected" by being inaccessible from the normal web tree, but are still accessible by changing the URL;
  - Solution: use **access control**.

### Database Vulnerabilities

- **SQLi (SQL Injection)**: attacker injects SQL queries into the application;
  - Caused by **concatenation** of user input with SQL queries;
  - **Tautologies**: inject a condition that is always true;
    - E.g. `SELECT * FROM users WHERE username = 'admin' OR 1=1; -- `(with a space in the end) will return all users;
  - **Union query**: injects a `UNION` query to retrieve data from another table;
    - E.g. `SELECT * FROM users WHERE username = 'admin' UNION SELECT * FROM credit_cards; -- `;
  - **Piggy-backed queries**: injects a second query after the first one;
    - E.g. `SELECT * FROM users; DROP TABLE users; -- `;
  - **Stored procedures** are also vulnerable to SQLi;
  - To get the names of the user tables, it can use `SELECT * FROM sysobjects WHERE xtype = 'U';`;
    - `sysobjects` is a table that contains information about the database objects in MySQL;
- **Inference**: for DBs that do not return data, the attacker attempts to infer the data by observing the behavior of the application;
  - **Blind injection**: information is inferred by asking true/false questions;
  - **Timing attacks**: information is inferred from the **response time** of the application;
    - Usually inject a branch with `WAITFOR DELAY '0:0:5'` to delay the response;
- **Injection mechanisms**:
  - 1st order: the injected data is used in the same query:
    - GET/POST inputs, cookies, headers, etc;
  - 2nd order: the injected data is stored in the DB and used later:
    - E.g. register a user with a malicious name, and then the name is used in a query;
- **Preventing SQLi**:
  - **Prepared statements**: the query is sent to the DBMS and then the data is sent;
  - Input validation;
  - Input sanitization.

### Validation and Encoding

- **Validation**: ensure that an input satisfies its:
  - **Syntax**: the input is well-formed;
  - **Length**;
  - **Type**;
- Where validation should be done:
  - **1st principle** - validate whenever data crosses a **trust boundary**;
  - **2nd principle** - there has to be a small set of well defined **chokepoints** where validation is done;
- Data validation strategies:
  - **Whitelist validation**: only allow known good data;
  - **Blacklist validation**: only block known bad data - **not recommended** - violated principle of **fail-safe defaults** (if the blacklist is incomplete, the system is vulnerable);
  - **Sanitize**: remove or escape dangerous characters;
  - **Regular expressions** are a good way to validate data;
- **Metacharacter evasion**: attacker tries to foul filters by encoding characters;
  - Can be solved by doing **canonicalization** (convert the input to a standard form);
  - E.g. `delete` can be encoded as `d%65lete`, but if the input is canonicalized, it will be converted to `delete`;
- **Encoding**: convert data to a format that is safe to use in a specific context;
  - **ASCII encoding**: convert characters to their ASCII code; uses 8 bits;
  - **UTF-8 encoding**: 8-bit encoding of Unicode characters;
  - **URL encoding**: convert characters to their hexadecimal ASCII code, preceded by `%`; used in URLs;
  - **HTML encoding**: convert characters to their HTML entity; used in HTML;
- What decodings do we need to do for validation?
  - **Do before validation the same decodings the application/interpreter might do after **Validation\*\*;
  - Also, in the **same order**;
- **Sanitization/Encoding**: neutralized the dangerous characters;
  - For example, to prevent XSS, perform **input validation** (throw away bad data) and **output encoding** (encode the output data, e.g. add quotes);

### Buffer Overflows

- **Buffer Overflow (BO)**: a condition where a program writes more data to a buffer than it can hold;
- Protection:
  - Always do **bound checking**;
  - In C, never use `gets`;
- **Stack overflows**:
  - **Stack smashing**: attacker overwrites the stack content (local vars, return address, etc);
  - **Code injection**: attacker injects shell code in the stack;
  - **Arc injection (or return-to-libc)**: attacker injects the address of a function in the stack;
  - **Pointer subterfuge**: attacker overwrites a pointer to point to a different location;
    - **Function-pointer clobbering**: modify a function pointer to point to a different function;
    - **Data-pointer modification**: modify address to assign data;
    - **Exception handler hijacking**: modify the exception handler to point to a different handler;
    - **Virtual pointer smashing**;
  - **Return-Oriented Programming (ROP)**:
    - **ROP gadgets**: small pieces of code that end with a `ret` instruction;
    - **ROP chain**: a sequence of ROP gadgets;
    - **ROP attack**: attacker creates a ROP chain to execute arbitrary code;
- **Integer overflows**:
  - **Overflow**: result of expression exceeds maximum value of the type;
  - **Underflow**: result of expression is less than the minimum value of the type;
  - **Signedness error**: when a signed value is treated as unsigned;
  - **Truncation**: when a value is truncated to fit in a smaller type;
- **Heap overflows**: modify the heap data structures.

### Input Validation and Format String Vulnerabilities

- **Never trust input**;
- **Metadata** can be represented:
  - **In-band**: as part of the data; e.g. strings in C (a string is a sequence of characters followed by a null byte);
    - Use **metacharacters** to represent metadata;
  - **Out-of-band**: separated from the data; e.g. length in Java;
  - Typical attacks using metachars:
    - **Embedded delimiters**: attacker inserts a delimiter to break the data;
      - E.g. `;` in SQLi;
    - **NUL character injection**: inserts a null byte to truncate the data;
      - E.g. in C, strings are null-terminated;
    - **Separator injection**: inserts a separator to allow **command injection**;
- **Format string vulnerabilities**:
  - Used in C in functions of the families `printf`, `err`, `syslog`, etc;
  - If the format string is user-controlled, the attacker can read/write memory;
  - The solution is to use `printf("%s", str)` instead of `printf(str)`;
  - By using `%x`, the attacker can read **4 bytes** of the stack;
  - To write to memory, the attacker can use `%n`, which writes the number of bytes printed so far;
    - We can insert several pairs of `address`/`%n` to write to several addresses;
  - `%Nx` pads bytes printed so far to `N` bytes;
    - `%N$n` writes the `N`th argument; e.g. `%3$n` writes to the 3rd argument.

### Dynamic Protection

- Block attacks that may exploit existing vulnerabilities;
- **Canaries**: a random value placed before the return address in the stack;
  - If the canary is modified, the program will exit;
  - **StackGuard** is a compiler that inserts canaries in the stack;
  - Detects **stack smashing** attacks, but do not detect BO attacks that modify **local variables**, because they are above the canary;
  - A solution to this is to **reorder the stack variables**: put the local variables after the buffer - this way the buffer overflow will not overwrite them;
  - **Function arguments** can also be overwritten by a buffer overflow, and the canary will only detect the BO after the function runs;
    - To protect them, we can copy the arguments to the top of the stack, after the buffer;
- **Non-executable stack and heap**:
  - Many BO attacks involve injecting shell code in the stack/heap;
  - Solution: mark these memory pages as **non-executable (NX)**;
- **Randomization and obfuscation**:
  - **Address Space Layout Randomization (ASLR)**: randomizes the memory layout of the program, in **runtime**;
    - Does not prevent, but **makes it harder** to exploit BO vulnerabilities;
    - Not effective for BOs that take advantage of local variables;
  - **Instruction set randomization**: code injection would be almost impossible if each computer has its own random instruction set;
    - A more practical case: in SQl, an application can add a key to each SQL command and operator to prevent code injection;
  - **Function pointer obfuscation**: the idea is to XOR the function pointer with a random value (a secret);
- **Integrity verification**
  - **SEH (Structured Exception Handling)**: a mechanism in Windows to handle exceptions; when an exception occurs, the OS looks for an exception handler in a list; the attacker can overwrite the list with a malicious handler;
    - **SafeSEH**: a mechanism that checks if the exception handler is valid;
  - **Array bound checking**: check if the index is within the bounds of the array;
  - **Control-flow integrity**: whenever a function is called, the return address is stored in a `GlobalRetStack`; when the function returns, the return address is checked against the `GlobalRetStack`;
    - It has a limit of entries;
- **Filtering**:
  - **WAF (Web Application Firewall)**: a firewall that filters HTTP requests.
