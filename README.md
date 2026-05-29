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
1. Connect via SSH:
   `ssh bandit1@bandit.labs.overthewire.org -p 2220`
2. Look for a file named "-" in home directory:
   `ls -la`
3. Deal with filename begining with "-", requires prefix such path name:
   `cat ./-`

**Key Takeaway:**
- "-" is a special character for piping stdout to other commmand, option or prefix in command, rrefer previous directory, and minus sign.

## Level 2 -> 3
**Goal:** Read password in file named "--spaces in this filename--" (Filename contains space)

**Steps:**
1. Connect via SSH:
   `ssh bandit1@bandit.labs.overthewire.org -p 2220`
2. Look for a file named "--spaces in this filename--" in home directory:
   `ls -la`
3. Deal with filename contains space, requires escape charater, backslash ("\"), for each space. 
   `cat ~/--spaces\ in\ this\ filename--`

**Key Takeaway:**
- Backslash ("\") can be used to escape spaces in file name as escape character.
