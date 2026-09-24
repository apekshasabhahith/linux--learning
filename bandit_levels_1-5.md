# OverTheWire Bandit — Levels 1–5

These levels introduced basic Linux file handling, special filenames, hidden
files, file types, and searching for files using specific conditions.

---

# Level 1 → Level 2

## Goal

The password for the next level is stored in a file named:

```text
-
```

The challenge is that `-` has a special meaning in many Linux commands.

---

## Command

```bash
cat ./-
```

---

## Understanding the Command

### `cat`

```bash
cat
```

`cat` is commonly used to display the contents of a file.

For example:

```bash
cat file.txt
```

displays the contents of `file.txt`.

---

### `./`

```text
./
```

means:

> The current directory.

So:

```bash
./-
```

means:

> The file named `-` in the current directory.

---

## Why Can't We Simply Use This?

```bash
cat -
```

The problem is that `-` has a special meaning in many Linux commands.

It can be interpreted as:

> Read input from standard input.

Therefore, Linux may not treat `-` as the filename we intended.

By using:

```bash
./-
```

we explicitly tell Linux:

> I mean the file named `-` located in the current directory.

Therefore:

```bash
cat ./-
```

correctly reads the file.

---

## Breaking Down the Command

```text
cat      → display file contents
./       → current directory
-        → filename
```

So:

```bash
cat ./-
```

means:

> Display the contents of the file named `-` in the current directory.

---

## Key Lesson

A filename can sometimes have a special meaning to a command.

Using a path such as:

```bash
./filename
```

can clearly tell Linux that something is a filename.

---

# Level 2 → Level 3

## Goal

The password is stored in a file named:

```text
spaces in this filename
```

The filename contains spaces.

---

## Command

```bash
cat "./spaces in this filename"
```

---

## Understanding the Problem

Normally, spaces separate arguments in a Linux command.

For example:

```bash
cat file.txt
```

has:

```text
cat       → command
file.txt  → argument
```

But consider:

```bash
cat spaces in this filename
```

Linux interprets this as several separate arguments:

```text
spaces
in
this
filename
```

That is not one filename.

---

## Solution 1 — Use Quotes

```bash
cat "./spaces in this filename"
```

The quotation marks tell the shell:

> Treat everything inside the quotes as one argument.

Therefore:

```text
"./spaces in this filename"
```

is treated as one filename.

---

## Solution 2 — Escape the Spaces

Another method is to use `\` before each space:

```bash
cat ./spaces\ in\ this\ filename
```

The backslash tells the shell that the following space is part of the filename.

---

## Why Quotes Are Easier

Instead of:

```bash
cat ./spaces\ in\ this\ filename
```

we can use:

```bash
cat "./spaces in this filename"
```

Both methods work.

---

## Key Lesson

Spaces normally separate arguments in the shell.

To use a filename containing spaces, we can:

```text
Use quotes
OR
Escape the spaces with \
```

### Remember

```bash
"filename with spaces"
```

treats the complete text as one filename.

---

# Level 3 → Level 4

## Goal

The password is stored in a **hidden file** inside the `inhere` directory.

---

## First Command

```bash
ls
```

This shows the normal files and directories in the current location.

We need to enter the `inhere` directory.

---

## Command

```bash
cd inhere
```

---

## Understanding `cd`

```bash
cd
```

means:

> Change directory.

Therefore:

```bash
cd inhere
```

moves us into the `inhere` directory.

---

## Finding the Hidden File

Use:

```bash
ls -la
```

---

## Understanding `ls -la`

### `ls`

```bash
ls
```

lists files and directories.

### `-l`

```bash
-l
```

means:

> Long listing format.

It provides additional information such as:

```text
permissions
owner
group
size
date
filename
```

### `-a`

```bash
-a
```

means:

> Show all files, including hidden files.

Linux hidden files commonly begin with:

```text
.
```

For example:

```text
.hidden
```

---

## Combined Command

```bash
ls -la
```

means:

> Show all files, including hidden files, using detailed listing format.

This allows us to see the hidden file.

---

## Reading the File

Once the hidden filename is identified:

```bash
cat <filename>
```

The actual filename from the challenge can then be supplied to `cat`.

---

## Why `ls` Alone Is Not Enough

If we use:

```bash
ls
```

hidden files are normally not displayed.

Therefore:

```bash
ls -la
```

is useful when we suspect that a hidden file exists.

---

## Key Lesson

Linux treats filenames beginning with `.` as hidden from normal directory listings.

```bash
ls
```

does not normally show them.

```bash
ls -a
```

does.

---

# Level 4 → Level 5

## Goal

The password is stored in the only **human-readable file** inside the `inhere`
directory.

There are multiple files, so we need to identify which one contains readable
text.

---

## Move Into the Directory

```bash
cd inhere
```

---

## List the Files

```bash
ls -la
```

This shows the files available in the directory.

However, simply seeing the filenames does not tell us which file contains
human-readable text.

---

## Using `file`

We can examine the type of each file using:

```bash
file ./*
```

---

## Understanding `file`

The `file` command examines a file and attempts to determine its type.

For example, it may identify something as:

```text
ASCII text
data
JPEG image data
gzip compressed data
```

In this challenge, we are looking for the file identified as readable text.

---

## Understanding `./*`

```text
./
```

means:

> Current directory.

The:

```text
*
```

is a wildcard.

It means:

> Match the files in this location.

Therefore:

```bash
./*
```

refers to the items matching the pattern in the current directory.

So:

```bash
file ./*
```

means:

> Use the `file` command on the files in the current directory.

---

## Finding the Human-Readable File

Look through the output of:

```bash
file ./*
```

We are looking for a file identified as something like:

```text
ASCII text
```

or another normal readable text format.

That is the file we need.

---

## Reading the File

After identifying the correct file:

```bash
cat ./<filename>
```

This displays its contents.

---

## Why `file` Is Useful

If we have many files and don't know what they contain, opening every file
manually is inefficient.

Instead:

```bash
file
```

can help us identify their types first.

---

## Key Lesson

The filename does not always tell us what type of data a file contains.

The `file` command can inspect a file and provide useful information about
its contents.

---

# Level 5 → Level 6

## Goal

The password is stored somewhere under the `inhere` directory.

We need to find a file that satisfies **all three conditions**:

- It is human-readable.
- It is exactly 1033 bytes in size.
- It is not executable.

---

## Command

```bash
find inhere -type f -size 1033c ! -executable
```

---

## Understanding the Command

This command uses several conditions with `find`.

---

### 1. `find`

```bash
find
```

is used to search for files and directories.

---

### 2. `inhere`

```bash
inhere
```

is the directory where we want to search.

So we are not searching the entire server.

We are searching inside:

```text
inhere
```

and its subdirectories.

---

### 3. `-type f`

```bash
-type f
```

means:

> Search only for regular files.

Linux has different types of filesystem objects.

Here, we specifically want a normal file.

---

### 4. `-size 1033c`

```bash
-size 1033c
```

means:

> Find a file that is exactly 1033 bytes.

The:

```text
c
```

means bytes.

So:

```text
1033c = exactly 1033 bytes
```

---

### 5. `! -executable`

```bash
! -executable
```

means:

> The file must NOT be executable.

The `!` means logical NOT.

So:

```bash
-executable
```

would select executable files.

But:

```bash
! -executable
```

selects files that are not executable.

---

## Complete Logic

The command:

```bash
find inhere -type f -size 1033c ! -executable
```

can be understood as:

```text
Search inside inhere
        ↓
Only regular files
        ↓
Exactly 1033 bytes
        ↓
Not executable
        ↓
Return the matching file
```

---

## Reading the Result

After `find` returns the matching filename, use:

```bash
cat <filename>
```

to display its contents.

---

## Why `find` Is Useful Here

There may be many files and directories.

Checking them manually would take time.

Instead, we describe the properties of the file we want and let `find`
search for it.

This is much more efficient.

---

# Commands Learned in Levels 1–5

| Command | Purpose |
|---|---|
| `cat` | Display file contents |
| `cd` | Change directory |
| `ls` | List files and directories |
| `ls -la` | Show all files with detailed information |
| `file` | Identify the type of a file |
| `find` | Search for files/directories using conditions |

---

# Important Linux Concepts Learned

## 1. Special Filenames

A filename such as:

```text
-
```

can have a special meaning to commands.

Using:

```bash
./-
```

makes it clear that it is a filename.

---

## 2. Spaces in Filenames

Spaces separate arguments in the shell.

Use quotes:

```bash
"file name"
```

or escape the spaces:

```bash
file\ name
```

---

## 3. Hidden Files

Files beginning with:

```text
.
```

are normally hidden from a regular `ls`.

Use:

```bash
ls -a
```

to display them.

---

## 4. File Types

The:

```bash
file
```

command helps determine what type of data a file contains.

---

## 5. Wildcards

The:

```text
*
```

wildcard can represent multiple filenames.

Example:

```bash
file ./*
```

---

## 6. Searching With Conditions

`find` can combine several conditions.

For example:

```bash
find inhere -type f -size 1033c ! -executable
```

allows us to search based on:

```text
location
file type
file size
permissions/properties
```

---

# Overall Learning From Levels 1–5

These levels introduced the basic Linux skills needed to work efficiently from
the command line.

The main progression was:

```text
Special filenames
       ↓
Filenames containing spaces
       ↓
Hidden files
       ↓
File identification
       ↓
Searching using multiple conditions
```

The important lesson from these levels is that Linux commands are not just
things to memorize. Understanding how the shell interprets filenames, paths,
arguments, wildcards, and command options makes it much easier to solve
problems systematically.
