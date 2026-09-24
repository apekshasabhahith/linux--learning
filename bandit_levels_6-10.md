# OverTheWire Bandit — Levels 6–10

This section covers Bandit Levels 6 to 10. These levels introduced more advanced
Linux file searching, text processing, pipes, readable strings, and Base64 decoding.

---

# Level 6 → Level 7

## Goal

The password for the next level is stored somewhere on the server.

We need to find a file that satisfies **all three conditions**:

- Owned by the user `bandit7`
- Owned by the group `bandit6`
- Exactly 33 bytes in size

---

## Command

```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

---

## Understanding the Command

The main command is:

```bash
find
```

`find` is used to search for files and directories.

The complete command contains several conditions, so Linux searches for a file
that matches **all of them**.

### 1. `/`

```bash
find /
```

`/` means the **root directory**.

Instead of searching only the current directory, we are telling `find` to
search from the root of the entire filesystem.

So it can look through directories such as:

```text
/home
/etc
/tmp
/var
...
```

---

### 2. `-user bandit7`

```bash
-user bandit7
```

This tells `find`:

> Find files whose owner is the user `bandit7`.

Linux files have an owner.

For example:

```text
owner = bandit7
```

The condition makes sure that we don't get files owned by other users.

---

### 3. `-group bandit6`

```bash
-group bandit6
```

This tells `find`:

> Find files whose group owner is `bandit6`.

Linux files can have both:

```text
User owner
Group owner
```

So the file must have:

```text
User  → bandit7
Group → bandit6
```

---

### 4. `-size 33c`

```bash
-size 33c
```

This searches for a file whose size is exactly **33 bytes**.

The `c` means **bytes**.

So:

```text
33c = exactly 33 bytes
```

Without the `c`, `find` uses a different size unit.

---

### 5. `2>/dev/null`

```bash
2>/dev/null
```

This part does not search for the file.

It deals with error messages.

When searching from `/`, we may encounter directories that the current user
cannot access. Linux would normally display messages such as:

```text
Permission denied
```

Linux has three standard streams:

```text
0 → standard input
1 → standard output
2 → standard error
```

Therefore:

```bash
2>
```

means:

> Redirect standard error.

And:

```bash
/dev/null
```

is a special location that discards anything sent to it.

Therefore:

```bash
2>/dev/null
```

means:

> Hide error messages.

---

## Why We Used This Command

There could be a huge number of files on the server.

Instead of manually checking every file, we gave `find` three conditions:

```text
Owner  → bandit7
Group  → bandit6
Size   → 33 bytes
```

Only a file satisfying all the conditions is relevant.

---

## Key Lesson

`find` becomes very powerful when multiple conditions are combined.

```text
find
 ↓
search location
 ↓
filter by owner
 ↓
filter by group
 ↓
filter by size
 ↓
find the required file
```

---

# Level 7 → Level 8

## Goal

The password for the next level is stored in `data.txt`.

The password is located next to the word:

```text
millionth
```

The file contains many lines, so manually searching it would be inefficient.

---

## Command

```bash
grep millionth data.txt
```

---

## Understanding the Command

The command has two main parts:

```bash
grep millionth data.txt
```

### `grep`

`grep` is used to **search text for a particular pattern**.

### `millionth`

This is the word we are searching for.

### `data.txt`

This is the file where we want to search.

So the complete command means:

> Search `data.txt` for the word `millionth`.

---

## Why Not Open the File and Search Manually?

The file contains a large amount of data.

Instead of:

```bash
cat data.txt
```

and manually looking through the output, we can directly search for the
important word.

```bash
grep millionth data.txt
```

This gives us the relevant line immediately.

---

## Understanding the Output

The line will contain something similar to:

```text
millionth <password>
```

The value next to `millionth` is the password for the next level.

---

## Key Lesson

```bash
grep
```

is one of the most useful Linux commands for working with text.

It allows us to search large files quickly.

### Remember

```text
grep = search for a pattern in text
```

---

# Level 8 → Level 9

## Goal

The password is stored in `data.txt`.

This time, we need to find the **only line that occurs exactly once**.

The file contains many repeated lines.

---

## Command

```bash
sort data.txt | uniq -u
```

---

## Understanding the Command

There are two commands here:

```bash
sort data.txt
```

and

```bash
uniq -u
```

They are connected using:

```text
|
```

This is called a **pipe**.

---

## Part 1 — `sort`

```bash
sort data.txt
```

`sort` arranges the lines in sorted order.

For example, imagine the file contains:

```text
apple
banana
apple
orange
banana
```

After:

```bash
sort data.txt
```

the lines become grouped:

```text
apple
apple
banana
banana
orange
```

This is important because `uniq` works by comparing **adjacent lines**.

---

## Part 2 — `|`

```bash
|
```

The pipe takes the output of one command and sends it as input to another
command.

So:

```bash
sort data.txt | uniq -u
```

means:

```text
data.txt
   ↓
sort
   ↓
sorted output
   ↓
uniq -u
   ↓
unique line
```

---

## Part 3 — `uniq -u`

```bash
uniq -u
```

`uniq` is used to deal with repeated lines.

The option:

```text
-u
```

means:

```text
unique
```

So:

```bash
uniq -u
```

prints only lines that occur exactly once.

---

## Why Do We Need `sort` First?

This is an important Linux concept.

`uniq` checks **adjacent duplicate lines**.

For example:

```text
apple
banana
apple
```

The two `apple` lines are not next to each other.

`uniq` will not treat them as a duplicate pair.

But after:

```bash
sort
```

we get:

```text
apple
apple
banana
```

Now the duplicates are next to each other.

Therefore:

```bash
sort data.txt | uniq -u
```

first groups identical lines and then finds the line that appears only once.

---

## Key Lesson

This level teaches how Linux commands can be combined.

```bash
command1 | command2
```

means:

> Take the output of `command1` and give it to `command2`.

### Remember

```text
sort → arrange the lines
uniq -u → show lines occurring only once
| → connect commands
```

---

# Level 9 → Level 10

## Goal

The password is stored in `data.txt`.

The file contains mostly non-readable/binary data.

We need to find the human-readable string that contains several `=` characters.

---

## Command

```bash
strings data.txt | grep "==="
```

---

## Understanding the Command

This command combines:

```text
strings
```

and:

```text
grep
```

using a pipe.

---

## Part 1 — `strings`

```bash
strings data.txt
```

Normally, commands such as:

```bash
cat data.txt
```

are useful for normal text files.

But `data.txt` in this level contains binary/non-readable data.

`strings` searches the file and extracts sequences of **printable/readable
characters**.

For example, a binary file might contain something like:

```text
(binary data)
(binary data)
Hello
(binary data)
Password
(binary data)
```

`strings` can extract readable parts such as:

```text
Hello
Password
```

---

## Part 2 — Pipe

```bash
|
```

The output from:

```bash
strings data.txt
```

is passed into:

```bash
grep "==="
```

So we don't have to manually inspect every readable string.

---

## Part 3 — `grep "==="`

```bash
grep "==="
```

`grep` searches for the specified pattern.

Here we are looking for:

```text
===
```

The quotation marks make the pattern clear as a single search expression.

Therefore:

```bash
strings data.txt | grep "==="
```

means:

> Extract readable strings from `data.txt`, then search those strings for
> a sequence containing several `=` characters.

---

## Why We Used `strings`

The file is not a normal text file.

Therefore, instead of trying to read the entire file directly, we use:

```bash
strings
```

to extract the readable information.

Then:

```bash
grep
```

helps us locate the relevant information.

---

## Key Lesson

Commands can be chained together to solve a problem step by step:

```text
Binary/non-readable file
        ↓
     strings
        ↓
Readable strings
        ↓
      grep
        ↓
Required information
```

### Remember

```text
strings → extract readable text
grep → search/filter text
```

---

# Level 10 → Level 11

## Goal

The password is stored in `data.txt`.

The contents are **Base64 encoded**.

We need to decode the contents to obtain the password.

---

## Command

```bash
base64 -d data.txt
```

---

## Understanding Base64

Base64 is an **encoding method**.

It converts data into a representation using characters such as:

```text
A-Z
a-z
0-9
+
/
=
```

Encoded data may look like:

```text
VGhlIHBhc3N3b3Jk
```

It does not mean the original data has been securely encrypted.

It has simply been encoded into another representation.

---

## `base64`

The command:

```bash
base64
```

is used to encode or decode Base64 data.

---

## `-d`

The option:

```bash
-d
```

means:

```text
decode
```

Therefore:

```bash
base64 -d data.txt
```

means:

> Decode the Base64 contents stored in `data.txt`.

---

## Why We Didn't Use `cat`

We could use:

```bash
cat data.txt
```

to display the encoded contents.

But that would only show the Base64 representation.

We need to convert it back to the original form.

Therefore we use:

```bash
base64 -d data.txt
```

---

## Encoding vs Encryption

This is an important concept.

### Encoding

Encoding changes the representation of data.

Example:

```text
Original data
     ↓
   Base64
     ↓
Encoded data
```

Anyone who knows the encoding method can decode it.

### Encryption

Encryption is designed to protect information using a key.

So:

```text
Base64 ≠ Encryption
```

Base64 provides **no confidentiality by itself**.

---

# Commands Learned in Levels 6–10

| Command | Purpose |
|---|---|
| `find` | Search for files/directories using conditions |
| `grep` | Search for a pattern in text |
| `sort` | Sort lines |
| `uniq -u` | Display lines occurring only once |
| `strings` | Extract readable strings from binary/non-text data |
| `base64 -d` | Decode Base64 data |
| `\|` | Send output of one command to another |
| `2>/dev/null` | Redirect and hide error messages |

---

# Important Concepts Learned

## 1. Searching Files

```bash
find
```

can search for files based on properties such as:

```text
Name
Owner
Group
Size
Permissions
```

---

## 2. Searching Text

```bash
grep
```

allows us to search for specific patterns inside files.

---

## 3. Pipes

```bash
|
```

allow multiple commands to work together.

Example:

```bash
sort data.txt | uniq -u
```

---

## 4. Sorting and Unique Values

```bash
sort
```

can arrange data so that related/duplicate lines become adjacent.

```bash
uniq -u
```

can then identify lines that occur only once.

---

## 5. Reading Binary Data

```bash
strings
```

can extract readable text from files that aren't normal text files.

---

## 6. Encoding and Decoding

Base64 is an encoding technique.

```bash
base64 -d
```

decodes Base64 data.

---

# Overall Learning From Levels 6–10

These levels helped me understand that Linux commands are much more powerful
when combined rather than used individually.

Instead of manually searching through files, I learned to:

```text
Search
  ↓
Filter
  ↓
Process
  ↓
Extract
  ↓
Decode
```

The main commands practiced were:

```bash
find
grep
sort
uniq
strings
base64
```

The biggest concept I learned was that understanding **why a command is being
used** is more useful than simply memorizing the command.
