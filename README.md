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
- `|` pipe can be used to carry over the output from the left command to the right one.

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

## Level 7 -> 8
**Goal:** Find correct password within data.txt (long length file), the correct one next to the word "millionth":

**Steps:**
1. Check file location:
   `ls -la`
2. data.txt file is found with 4MB:
   `la -la | wc -l` to check number of text lines, the result show over 90k lines contained.
   `head -n 10 data.txt` to check inside data and its stucture. there are words followed by space and code or password.
3. The correct password is next to the word "millionth", hence `grep` command (Global Regular Expression Print) is a good choice for searching a text or pattern within a file:
   `cat data.txt | grep "millionth"`

**Key Takeaway:**
- `grep` is a command for seaching a specific words or phases or patterns.
- `wc` is a command for counting a word or number of line within a file.

## Level 8 -> 9
**Goal:** Find the password within a file (data.txt), the password is the line of text which occurs only once

**Steps:**
1. Check file location
   `ls -la`
2. Key of this challenge is "line of text that occurs only once", so that `uniq` command can play a role in this. 
   `sort data.txt | uniq -u`

**Key Takeaway:**
- Sole `uniq` is not be able to detect the duplicate lines unless they are adjacent to each other. Simply using `sort` command to arrange or group all duplicated lines before applying `uniq` command.
- To get distinct line of text, `sort -u` command can be used instead of `sort data.txt | uniq`

## Level 9 -> 10
**Goal:** Find a password in a file. It is human-readable strings, preceded by several "=".

**Steps:**
1. Check file location
   `ls -la`
2. Check file content
   `head -n 5 data.txt`
3. Check file type
   `file data.txt`
4. According pre-check, it is binary file, to capture human-readable text or strings, `strings` command can be used for extracting string (human-readable text) within a file, then grab the password using `grep` command.
   `strings data.txt | grep -E '={2,}'`

**Key Takeaway:**
- `strings` command can encode binary file to text or string (based on selected option, by defaults, encoding based on a single-byte (ASCII)), only sequences of characters (default is 4 characters) will be extracted, the rest shall be eliminated or not displayed.
- `grep -E '={2,}'` is a command to grab "=" character that occurs more than 2 in the line.

## Level 10 -> 11
**Goal:** Get the password within a file, which encoded with base64.

**Steps:**
1. check file location and content.
   `ls -la`
   `cat data.txt`
2. the content encoded by base64, so that `base64` command can decode and solve this.
   `base64 -d data.txt`

**Key Takeaway:**
- `base64` command can convert text format to base64 format and vise versa (encoding and decoding).

## Level 11 -> 12
**Goal:** Get the password within a file using [ROT13](https://en.wikipedia.org/wiki/ROT13)

**Steps:**
1. Check file location and content.
   `ls -la`
   `cat data.txt`
2. Using `tr` command to translate using ROT13 or Rotate13, this transforms a text using defined lookup table
   `tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt`

**Key Takeaway:**
- `tr` command is text transforming command using 2 sets of the character as lookup function. 

## Level 12 -> 13
**Goal:** Get the password which compressed repeatedly in hexdump file ("data.txt").

**Steps:**
1. Check file existing.
   `ls -la`
2. This task requires temp directory, so create new directory in `/tmp/`, then change working directory to created one.
   `mktemp -d`
   `cd /tmp/{new directory}`
3. Copy hexdump file (working file) from home directory to current working directory.
   `cp ~/data.txt .`
4. Reverse hexdump to binary file.
   `xxd -r data.txt > binary`
5. Check file type for extraction (based on file type; wheither it is gzip, or bzip2, or tar).
   `file binary` then;
   - `gzip -d {file name}.gz` to decompress gzip file
   - `bzip2 -d {file name}.bz2` to decompress bzip2 file
   - `tar -xf {file name}.tar` to extract tar file
6. This file is compressed multiple times, instead of manually decompress and extract, writing a script (bash script) is interesting choice.

```
#!/bin/bash

FILE=$1
FTYPE=$(file $FILE)

while ! grep -q "ASCII" <<< $FTYPE; do
        # print file and its type
                # check file type then decompress or extract archive
                if grep -q "gzip" <<< $FTYPE; then
                        echo "Current file: ${FILE} is gzip type -> decompressed"
                        mv $FILE "${FILE}.gz";
                        gzip -d "${FILE}.gz";
                elif grep -q "bzip2" <<< $FTYPE; then
                        echo "Current file: ${FILE} is bzip2 type -> decompressed"
                        mv $FILE "${FILE}.bz2";
                        bzip2 -d "${FILE}.bz2";
                elif grep -q "tar" <<< $FTYPE; then
                        echo "Current file: ${FILE} is tar type -> extracted"
                        mv $FILE "${FILE}.tar";
                        tar -xf "${FILE}.tar";
                        rm "${FILE}.tar";
                else echo "Unknown type -> error";
                        exit 1;
                fi
        FILE=$(ls -t | grep -vE "(level12_script.sh|data.txt)" | head -n 1);
        FTYPE=$(file $FILE);
done
echo ""
echo "Final file is ${FILE}."
cat $FILE
```

7. To run a written script, change permission mode, then run bash script.
   `chmod u+x level12_script.sh`
   `./level12_script.sh binary`

**Key Takeaway:**
- File extension do not determine the actual file type, always use `file` command to real file structure
- Replacing manual action with automation script is powerful choice for solving a task which has pattern of repeated action, reduced time comsuming and tidious action. 
- `gzip`, `bzip2`, `tar`, and `xxd` are commands to compress, decompress, archive, extract, and convert hexdump to binary respectively, must be put to the right job.  

## Level 13 -> 14
**Goal:** There is no password for level 14 in level 13 task, using private SSH Key provided in home directory to log in the next level

**Steps:**
1. Check files existing in home directory, there are only sshkey.private and HINT files.
   `ls -la`
2. HINT file mentioned that logging in from level to another via localhost is prevented, please log out. To get into next level without password, SSH Key is required, "sshkey.private" file shall be copied from server to local machine via `scp`.
   `scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private .`
3. Copied private key (sshkey.private) can be used to log in next level (level 14) without password.
   `ssh -i sshkey.private -p 2220 bandit14@bandit.labs.overthewire.org`
4. To get a password for level 14, it is stored in `/etc/bandit_pass/bandit14` just use `cat` to grap it

**Key Takeaway:**
- `scp` is a command to copy a file via secure shell.
- Aside from password authentication, `ssh` can be also connected or established via key-pair authetication (private key and public key). Host server shall hold a public key, and private key must be held by client side.  

## Level 14 -> 15
**Goal:** Get password from localhost's response after submitting the password of the current level to port 30000.

**Steps:**
1. After login to level14, there is nothing in home directory.
2. There are many way to sent a text to localhost on a specific port (30000) to get a password of the next level, `nc` or netcat command is recommended.
   `nc localhost 30000 <<< "MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS"`

**Key Takeaway:**
- `nc` is a command for network socket management using raw data transfering.
- `telnet` is also interesting tool, but this connection protocol (telnet protocol) before text submission, this may cause an error if localhost does not understand control characters of telnet during telnet negotiation stage.

## Level 15 -> 16
**Goal:** Get password from localhost's response after submitting the password of the current level to port 30001 using TLS/SSL encryption.

**Steps:**
1. Login to level15, there is nothing as same as level14 task, but this time requires using TLS/SSL encryption.
2. One of recommended method for this level is `openssl s_client` command to establish TLS/SSL connection, then just enter the level15 password.
   `openssl s_client -connect localhost:30001` 

**Key Takeaway:**
- `openssl s_client` command is useful for TSL/SSL analysis, all details of TLS Handshake process will be displayed as output including TSL version, Ciper Suite, and Certificate Chain.
- Another method for TLS connection is using `ncat` command: `ncat --ssl localhost 30001`, this is the same command as `nc` but more secure.

## Level 16 -> 17
**Goal:** Get password from localhost's response after submitting the password of the current level to port in range 31000 to 32000 using TLS/SSL encryption.

**Steps:**
1. According previous task, `ncat` command is another command that can be used to send a message to a server using TLS/SSL encryption, but manually sending to each port in range 31000 to 32000 is impossible. Port scanner command like `nmap` can overcome this.
   `nmap localhost -p 31000-32000`
2. After listening ports are found, `ncat` could be used to get credential for the next level. But this could be better if this is worked automatically.

```
# create list of listening ports as variable
ports=$(nmap localhost -p 31000-32000 | grep -E "[0-9]{5}/" | awk -F"/" '{print $1}')

# apply for loop to such list
for port in $ports; do echo "Checking Port: ${port}"; ncat --ssl localhost $port <<< "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx"; echo ""; done
```

3. The correct credential will be displayed as private key for ssh access to the next level, so copy it.
4. Create a file in local machine using copied credential, then use it to login the next level.
   `ssh -i ./sshkey.private -p 2220 bandit17@bandit.labs.overthewire.org`

**Key Takeaway:**
- `nmap` is a command for port scanner, even though `nc` command can do it, but `nmap` is more rigid and recommended.

## Level 17 -> 18
**Goal:** Get the password in "password.new" file, it is the only line that has been changed between ".old" and ".new" files

**Steps:**
1. The only powerful command to check text file different is `diff`, simply type:
   `diff passwords.new passwords.old`
2. The output will show the symbol "<" followed by left file's text, and ">" followed by right file's text. This indicates the text line different, and the password is in the ".new" file.

**Key Takeaway:**
- Although, `cat passwords.new passwords.old | sort | uniq -u` can define the different lines, but this is not effective as same as `diff` command, which is more powerful and analysable.

## Level 18 -> 19
**Goal:** Get a password in "readme" file in home directory, but server will log out immediately after log in.

**Steps:**
1. This task is just getting a "readme" file without logging in the server, but copying and transferring a file is still possible. Using `scp` command with level 18 password to get a password for the next level.
   `scp -P 2220 bandit18@bandit.labs.overthewire.org:~/readme .`
2. After readme file is downloaded to local machine, then using `cat` to read a password within.
   `cat readme` 

**Key Takeaway:**
- After login as bandit19 user, investigating `cat ../bandit18/.bashrc` found `exit 0` command at the bottom line. this causes all logging in as bandit18 user will immediately logout after ".bashrc" script is running.
- ".bashrc" is personal script for a specific user, this script will be automatically run everytime terminal is opened or `ssh` login. Avoiding a script runnning, `scp` comes a role in this task.

## Level 19 -> 20
**Goal:** Use setuid binary (owned by bandit20) in home directory to get a password of the next level in "/etc/bandit_pass".

**Steps:**
1. Even though the executable file is owned by another user, but it us setuid permission which allows any users can execute this file as the file's owner (the process takes the effective UID of the file's owner)
2. The provided executable file allows any user to run a command through arguments with the permissions of the file's owner. So that the password can be read in "/etc/bandit_pass" using this executable file.
   `./bandit20-do cat /etc/bandit_pass/bandit20`

**Key Takeaway:**
- Setuid is one of the special file permission in Linux, this allows a user to execute a program with the permissions of the file’s owner, rather than their own.
- Besides, there are other special permission such as setgid that allows a user to execute a program with the permission of the file's group, When applied to a directory, it ensures that all newly created files and subdirectories inherit the group ownership of that parent directory and sticky bit that allows any user to read, write, and execute a file except deletion.
