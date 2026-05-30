# OverTheWire: Bandit

This is a note, that written up during learning Besic Linux and Commmand on 
[OverTheWise: Bandit](https://overthewire.org/wargames/bandit/)

## Level 0 -> 1
**Goal:** Connect to the server and retrieve the password.

**Steps:**
1. Connect via SSH:
   `ssh bandit0@bandit.labs.overthewire.org -p 2220`
2. Look around the directory:
   `ls -la`
3. Read the readme file:
   `cat readme`

**Key Takeaway:**
- Learned basic SSH connection syntax.
- Understanding how to list files and read contents.

## Level 1 -> 2
**Goal:** Read password in file named "-" [special character](https://linux.die.net/abs-guide/special-chars.html) 

**Steps:**
1. Look for a file named "-" in home directory:
   `ls -la`
2. Deal with filename begining with "-", requires prefix such path name:
   `cat ./-`

**Key Takeaway:**
- "-" is a special character for piping stdout to other commmand, option or prefix in command, rrefer previous directory, and minus sign.

## Level 2 -> 3
**Goal:** Read password in file named "--spaces in this filename--" (Filename contains space)

**Steps:**
1. Look for a file named "--spaces in this filename--" in home directory:
   `ls -la`
2. Deal with filename contains space, requires escape charater, backslash ("\"), for each space. 
   `cat ~/--spaces\ in\ this\ filename--`

**Key Takeaway:**
- Backslash ("\") can be used to escape spaces in file name as escape character.

## Level 3 -> 4
**Goal:** Read password within hidden file in directory named "inhere".

**Steps:**
1. Look for a file within "inhere" directory using `ls` command and "-a" option to display all files including hidden one, its name precedes with dot ".".
   `ls -la ~/inhere/`
2. A hidden file named "...Hiding-From-You" is found, to read just use `cat` command.
   `cat ~/inhere/...Hiding-From-You`

**Key Takeaway:**
- To display hidden file require `ls` command with "-a" option
- A file named with dot "." preceding, is considered as hidden file in Linux system 

## Level 4 -> 5
**Goal:** Find a human-readable file (ASCII Text), which contains a password.

**Steps:**
1. Check all files within "inhere" directory.
   `ls -la ~/inhere/`
2. There are 10 files found, to check file type (wheither it is human-readable; ASCII Text), combination of `for loop` and `file` commands can be applied to check those 10 file type.
   `for file in ~/inhere/*; do file $file; done`
3. File"-file07" found ASCII Text type (human-readable), to read a password within, just use `cat` command
   `cat ~/inhere/-file07` 

**Key Takeaway:**
- "For loop" syntax is `for {variable} in {list}; do {action}; done` for iteration.
- To check file type, 'file' command can be used.

 ## Level 5 -> 6
**Goal:** Find a password in a file with 3 following properties:
          1. Human-Readable format (ASCII Text)
          2. 1033 bytes in size
          3. non-executable

**Steps:**
1. After check all files existing by 
   `ls -la ~/inhere/`
   Found that there are 20 subdirectories, each directory contains 9 files in 3 different patterns (-file, .file, and space file), so that the total is 20x9=180 files
2. The key is file size (1033) and non-executable file properties, `ls -la` output can display both file permission and size of the file in column 1 and 5 respectively.
3. To filter file size (equal to 1033) in column 5, `awk` command is a good choice. Use 'for loop' and `ls -la` to get the output of each subdirectory for applying `awk` command. 
   `for file in ./inhere/*; do echo $file; ls -la $file | awk '$5==1033 {print $0}'; done`  
4. The output shows that "./inhere/maybehere07" directory contains a file with 1033 bytes and no execution permission. Then checks it out with `cat` command.
   `cat ./inhere/maybehere07/.file2` 

**Key Takeaway:**
- `ls -la` can display necessary file properties; such file size and permission.
- `awk` command is a good choice for working table format output, especially filtering.

## Level 6 -> 7
**Goal:** Find a password in a file with 3 following properties:
          1. owned by user bandit7
          2. owned by group bandit6
          3. 33 bytes in size

**Steps:**
1. To get file within a server, `find` command is a best choice. This command has options to get files based on their size and owner (both user and group).
   `find / -size 33c -group bandit6 -user bandit7`
2. To eliminate stderr, just add `2>/dev/null`.
   `find / -size 33c -group bandit6 -user bandit7 2>/dev/null`
3. Finally, there is only one file, which is matched to requirement, then read with `cat`
   `cat /var/lib/dpkg/info/bandit7.password`

**Key Takeaway:**
- `find` command is powerful for file searching especially when file properties are required for searching.
- `find` command can be applied in previous task (Level 5 -> 6) as well, just type `find . -type f -size 1033c -not -executable -exec file {} +`
