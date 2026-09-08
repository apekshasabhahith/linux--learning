\# OverTheWire Bandit — Levels 1–5



\## Level 1

\### Problem

Password is stored in a file named `-`.



\### Solution

cat ./-



\### Concepts

\- `cat` → displays file contents.

\- `./` → current directory.

\- `./-` safely refers to a filename starting with `-`.



\---



\## Level 2

\### Problem

Password is stored in `--spaces in this filename--`.



\### Solution

cat "./--spaces in this filename--"



\### Concepts

\- Quotes `" "` → treat spaces as part of one filename.

\- `./` → safely handles filenames beginning with `-`.



\---



\## Level 3

\### Problem

Password is inside a hidden file in the `inhere` directory.



\### Solution

cd inhere

ls -a

cat ...Hiding-From-You



\### Concepts

\- `cd` → enter a directory.

\- `ls` → list files/directories.

\- `ls -a` → show all files, including hidden files.

\- Name beginning with `.` → hidden file/directory.

\- `.` → current directory.

\- `..` → parent directory.

\- `ls -l`: `d` = directory, `-` = regular file, `l` = symbolic link.



\---



\## Level 4

\### Problem

Inside `inhere`, one of the files is human-readable and contains the password.



\### Solution

file ./-file\*



Then identify the human-readable file and:



cat ./-fileXX



\### Concepts

\- `file` → identifies file type/content.

\- `\*` → wildcard; matches multiple filenames.

\- `./` → safely handles filenames beginning with `-`.

\- `cat` → displays file contents.



\---



\## Level 5

\### Problem

Find a regular file that:

\- is exactly 1033 bytes

\- is not executable

\- is human-readable



\### Solution

find . -type f -size 1033c



Then check the identified file:



ls -l ./maybehere07/.file2

file ./maybehere07/.file2

cat ./maybehere07/.file2



\### Concepts

\- `find .` → search current directory and subdirectories.

\- `-type f` → regular files only.

\- `-size 1033c` → exactly 1033 bytes.

\- `c` → bytes.

\- `ls -l` → detailed information and permissions.

\- `file` → identifies file type.

\- `cat` → displays contents.

\- `.file2` → hidden file because it starts with `.`.



\---



\# Important Commands



cat file        → read file

ls              → list files

ls -a           → list all, including hidden

ls -l           → detailed listing + permissions

cd directory    → enter directory

cd ..           → go to parent directory

cd              → go to home directory

file filename   → identify file type

find .          → search recursively

\*               → wildcard

./              → current directory

.               → current directory

..              → parent directory



\# Key Lessons

1\. Filenames can contain spaces.

2\. Filenames can begin with `-`.

3\. Files/directories can be hidden using `.`.

4\. `ls -l` helps identify files, directories and permissions.

5\. `find` searches recursively.

6\. File size can be used as a search condition.

7\. File permissions show whether a file is executable.

8\. `file` identifies the type/content of a file.

