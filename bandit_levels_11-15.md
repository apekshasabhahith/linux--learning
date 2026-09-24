# OverTheWire Bandit — Levels 11–15

These levels introduced character encoding and decoding, hexadecimal data,
compression, SSH private keys, network services, localhost connections, and
SSL/TLS.

---

# Level 11 → Level 12

## Goal

The password for the next level is stored in `data.txt`.

The contents of the file have been encrypted using **ROT13**.

We need to decode the contents to obtain the password.

---

## First, View the File

```bash
cat data.txt
```

This displays the contents of `data.txt`.

The output looks like text, but the characters have been transformed using
ROT13.

---

## Command

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

---

## Understanding `tr`

`tr` stands for **translate**.

It is used to replace characters from one set with characters from another set.

The command:

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

creates the ROT13 character mapping.

---

## Understanding the First Character Set

```text
A-Za-z
```

represents all English alphabet characters:

```text
A-Z → uppercase letters
a-z → lowercase letters
```

---

## Understanding the Second Character Set

```text
N-ZA-Mn-za-m
```

represents the characters after shifting each letter by 13 positions.

For example:

```text
A → N
B → O
C → P
...
M → Z
N → A
O → B
...
Z → M
```

The same transformation is applied to lowercase letters.

---

## Understanding `<`

```bash
< data.txt
```

is called **input redirection**.

It means:

> Take the contents of `data.txt` and provide them as input to the command.

So:

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

means:

> Read `data.txt`, apply the ROT13 character translation, and display the
> decoded result.

---

## `<` vs `>`

This is important.

```bash
command < file
```

means:

> Take input from the file.

Whereas:

```bash
command > file
```

means:

> Send the command's output into the file.

Therefore:

```text
< → input
> → output
```

---

## Key Lesson

ROT13 is a simple character-substitution technique.

The important command is:

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

---

# Level 12 → Level 13

## Goal

The password is stored in `data.txt`.

The file has been repeatedly compressed and archived using different
compression formats.

We need to identify the file type, decompress or extract it, and repeat the
process until we reach the actual readable data.

---

## Step 1 — Create a Temporary Directory

```bash
mktemp -d
```

This creates a temporary directory and prints its path.

For example:

```text
/tmp/tmp.xxxxx
```

The exact name will be different each time.

---

## Why Use a Temporary Directory?

The original file will be modified several times during the challenge.

Using a temporary directory keeps the work organized and prevents us from
unnecessarily modifying files in the original location.

---

## Step 2 — Move Into the Temporary Directory

After creating the directory:

```bash
cd /tmp/tmp.xxxxx
```

Use the actual directory path produced by `mktemp -d`.

---

## Step 3 — Copy the Challenge File

Copy the original file into the temporary directory:

```bash
cp ~/data.txt .
```

The `.` means:

> Copy the file into the current directory.

---

## Step 4 — Inspect the File

```bash
file data.txt
```

The `file` command tells us what type of data the file contains.

This is important because the file may not actually be a text file.

---

## Step 5 — Hexadecimal Data

At one stage, the file is represented as hexadecimal data.

Rename it:

```bash
mv data.txt data.hex
```

Then reverse the hexadecimal representation:

```bash
xxd -r data.hex data
```

---

## Understanding `xxd`

`xxd` is used to create hexadecimal representations of files and can also
reverse them.

The:

```text
-r
```

option means:

> Reverse the hexadecimal representation.

Therefore:

```bash
xxd -r data.hex data
```

converts the hexadecimal data back into binary data.

---

## Step 6 — Identify the New File

```bash
file data
```

This tells us what compression or archive format we have now.

The important rule is:

> Don't guess the next command. Use `file` to identify the format.

---

## gzip

If the file is identified as gzip compressed data:

```bash
mv data data.gz
gunzip data.gz
```

`gunzip` decompresses gzip files.

---

## bzip2

If the file is identified as bzip2 compressed data:

```bash
mv data data.bz2
bunzip2 data.bz2
```

`bunzip2` decompresses bzip2 files.

---

## tar

If the file is identified as a tar archive:

```bash
mv data data.tar
tar -xf data.tar
```

### Understanding `tar -xf`

```text
-x → extract
-f → specify the archive file
```

Therefore:

```bash
tar -xf data.tar
```

means:

> Extract the contents of `data.tar`.

---

## Repeat the Process

After every operation:

```bash
file <filename>
```

Check the new file type.

Then use the appropriate command.

The overall process is:

```text
file
 ↓
Identify format
 ↓
Use correct tool
 ↓
file again
 ↓
Identify new format
 ↓
Decompress/extract
 ↓
Repeat
```

---

## Useful Commands

```bash
file data
```

Identify the file type.

```bash
xxd -r data.hex data
```

Reverse hexadecimal data.

```bash
gunzip data.gz
```

Decompress gzip.

```bash
bunzip2 data.bz2
```

Decompress bzip2.

```bash
tar -xf data.tar
```

Extract a tar archive.

---

## Key Lesson

File extensions cannot always be trusted.

Use:

```bash
file filename
```

to determine what the file actually contains.

The main skill from this level is:

```text
Identify → Choose tool → Transform → Identify again
```

---

# Level 13 → Level 14

## Goal

Instead of directly providing the password, this level provides an
**SSH private key**.

We need to use this private key to authenticate as the next Bandit user.

---

## Important Concept — SSH Keys

SSH authentication can be performed using a key pair:

```text
Public key  → stored on the server
Private key → kept secret by the user
```

The private key is used to prove that we are authorized to authenticate.

---

## Identify the Private Key

First list the files:

```bash
ls
```

Locate the private-key file provided by the challenge.

---

## SSH Command

The general format is:

```bash
ssh -i <private-key> <username>@<host> -p <port>
```

For example:

```bash
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

---

## Understanding `ssh`

```bash
ssh
```

stands for **Secure Shell**.

It is used to securely connect to a remote computer.

---

## Understanding `-i`

```bash
-i sshkey.private
```

tells SSH:

> Use this private key for authentication.

`-i` means **identity file**.

---

## Understanding the Username

```text
bandit14@
```

specifies the user account we want to log in as.

---

## Understanding the Host

```text
bandit.labs.overthewire.org
```

is the remote server.

---

## Understanding `-p`

```bash
-p 2220
```

specifies the port used by the SSH server.

SSH normally uses:

```text
22
```

but the Bandit challenge uses:

```text
2220
```

---

## Complete Command Breakdown

```bash
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

means:

```text
ssh
 ↓
Connect using SSH

-i sshkey.private
 ↓
Use this private key

bandit14@
 ↓
Login as bandit14

bandit.labs.overthewire.org
 ↓
Remote server

-p 2220
 ↓
Use port 2220
```

---

## Security Lesson

A private key is sensitive information.

Never upload:

```text
Private keys
Passwords
API keys
Tokens
```

to GitHub.

For learning notes, document the method and command structure, but never
publish the actual private key or challenge password.

---

## Key Lesson

SSH does not always require password authentication.

It can use cryptographic key pairs:

```text
Public key  → server
Private key → user
```

The private key must remain secret.

---

# Level 14 → Level 15

## Goal

The password for the next level is not stored in a normal file.

Instead, we must send the current password to a service running on:

```text
localhost:30000
```

---

## Understanding `localhost`

```text
localhost
```

refers to the same computer on which the command is running.

It commonly refers to:

```text
127.0.0.1
```

Therefore:

```text
localhost:30000
```

means:

```text
Host → localhost
Port → 30000
```

---

## Command

```bash
nc localhost 30000
```

---

## Understanding `nc`

`nc` stands for **Netcat**.

Netcat is a command-line networking tool that can create network connections.

The general syntax is:

```bash
nc <host> <port>
```

Therefore:

```bash
nc localhost 30000
```

means:

> Connect to port 30000 on the local machine.

---

## Understanding Ports

A port identifies a particular network service.

For example:

```text
localhost:30000
```

can be broken into:

```text
localhost → host
30000     → port
```

The connection looks like:

```text
Your machine
     ↓
localhost
     ↓
port 30000
     ↓
running service
```

---

## What Happens After Connecting?

The service waits for the current password.

We provide the current Bandit password through the connection.

If it is correct, the service returns the information required for the next
level.

---

## Why This Level Is Different

Earlier levels mainly involved files.

Here, the information is provided by a **network service**.

This introduces the idea that:

> Information does not always have to be stored in a file. A program can
> provide information through a network port.

---

## Key Lesson

`nc` can be used to communicate with network services.

```bash
nc localhost 30000
```

connects to a service running locally on port 30000.

---

# Level 15 → Level 16

## Goal

The password for the next level must be submitted to a service running on:

```text
localhost:30001
```

However, this service requires an **SSL/TLS encrypted connection**.

---

## Why `nc` Is Not Enough

In Level 14, the service used a normal connection:

```bash
nc localhost 30000
```

Level 15 is different because the service requires SSL/TLS.

Therefore, we need a tool that understands TLS.

---

## Command

```bash
openssl s_client -connect localhost:30001
```

---

## Understanding `openssl`

OpenSSL is a toolkit used for cryptography and SSL/TLS.

It can work with:

```text
Certificates
Keys
Encryption
TLS connections
Cryptographic operations
```

---

## Understanding `s_client`

```bash
s_client
```

is an OpenSSL client that can establish an SSL/TLS connection to a server.

It is useful for testing and interacting with TLS-enabled services.

---

## Understanding `-connect`

```bash
-connect localhost:30001
```

specifies:

```text
Host → localhost
Port → 30001
```

Therefore:

```bash
openssl s_client -connect localhost:30001
```

means:

> Establish an SSL/TLS connection to port 30001 on localhost.

---

## What You May See

After running the command, OpenSSL may display information about the TLS
connection, such as:

```text
Certificate
Protocol
Cipher
Verification
```

This is normal.

The important point is that the TLS connection has been established and the
service is ready to receive input.

---

## Key Lesson

A network connection can be:

```text
Normal TCP connection
```

or:

```text
Encrypted SSL/TLS connection
```

For a normal connection:

```bash
nc localhost 30000
```

For the TLS connection:

```bash
openssl s_client -connect localhost:30001
```

---

# Commands Learned in Levels 11–15

| Command | Purpose |
|---|---|
| `tr` | Translate characters |
| `file` | Identify file type |
| `mktemp -d` | Create a temporary directory |
| `cp` | Copy files |
| `mv` | Move or rename files |
| `xxd -r` | Reverse hexadecimal data |
| `gunzip` | Decompress gzip files |
| `bunzip2` | Decompress bzip2 files |
| `tar -xf` | Extract tar archives |
| `ssh` | Connect securely to a remote system |
| `nc` | Connect to network services |
| `openssl s_client` | Establish an SSL/TLS connection |

---

# Important Concepts Learned

## 1. ROT13

Character substitution used to transform letters.

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

---

## 2. Input Redirection

```text
<
```

takes input from a file.

```bash
command < file
```

---

## 3. File Identification

```bash
file filename
```

helps determine the actual format of a file.

---

## 4. Compression and Archives

Different formats require different tools:

```text
gzip  → gunzip
bzip2 → bunzip2
tar   → tar -xf
```

---

## 5. SSH Key Authentication

SSH can use a private key instead of a password:

```bash
ssh -i <private-key> <user>@<host> -p <port>
```

---

## 6. Localhost and Ports

```text
localhost
```

means the current machine.

A port identifies a service:

```text
localhost:30000
```

---

## 7. Network Connections

```bash
nc localhost 30000
```

creates a connection to a normal network service.

---

## 8. SSL/TLS

```bash
openssl s_client -connect localhost:30001
```

creates an SSL/TLS client connection.

---

# Overall Learning From Levels 11–15

These levels moved from basic Linux file manipulation into more advanced
command-line and networking concepts.

The progression was:

```text
ROT13 decoding
      ↓
Hexadecimal data
      ↓
Compression and archives
      ↓
SSH private-key authentication
      ↓
Network services
      ↓
Ports and localhost
      ↓
SSL/TLS connections
```

The main problem-solving approach I learned was:

```text
Identify what I am dealing with
          ↓
Understand its format/protocol
          ↓
Choose the correct Linux tool
          ↓
Run the command
          ↓
Inspect the result
          ↓
Continue to the next step
```

These levels provided hands-on practice with Linux, authentication,
networking, and encrypted connections.
