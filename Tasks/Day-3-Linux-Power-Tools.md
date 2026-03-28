Linux Research Quiz 

Breakdown of each topic:
1. Linux user management is how the system controls who can use it and what they’re allowed to do.

There are three main types of users:

Root user: has full control over the system.
Regular users: normal accounts with limited permissions.
System users: used by services and processes, usually without login access.

Users can also belong to groups, which makes it easier to manage permissions for multiple users at once.

Important files:

/etc/passwd → stores basic user information
/etc/shadow → stores encrypted passwords
/etc/group → stores group information

Common commands:

adduser username → create a user
deluser username → delete a user
passwd username → change password
usermod -aG group username → add user to a group

Permissions control access to files using:

read (r), write (w), execute (x)

2. Package Installation on Linux
apt — Debian/Ubuntu/Parrot/Kali

sudo apt update               # Refresh package list
sudo apt install <pkg>        # Install
sudo apt remove <pkg>         # Remove
sudo apt upgrade              # Upgrade all packages

pkg — FreeBSD / Termux (Android)

pkg install <pkg>
pkg update && pkg upgrade

pacman — Arch Linux / Manjaro

sudo pacman -Syu              # Sync & upgrade
sudo pacman -S <pkg>          # Install
sudo pacman -R <pkg>          # Remove
sudo pacman -Ss keyword       # Search
yay -S <pkg>                  # AUR helper (like you use on your Arch setup)

3. Script Installation
Basic steps
1. Create or get the script

Example:

nano myscript.sh

Write:

#!/bin/bash
echo "Hello, Azarya"
2. Make it executable
chmod +x myscript.sh
3. Run the script
./myscript.sh
Install script system-wide (so you can run it anywhere)

Move it to a directory in your PATH:

sudo mv myscript.sh /usr/local/bin/myscript

Now you can run it like a command:

myscript
Install scripts from the internet

Usually:

git clone <repo-url>
cd project-folder
chmod +x install.sh
./install.sh
Important note

Always read a script before running it:

cat install.sh

Because scripts can:

install software
change system files
run harmful commands
