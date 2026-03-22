Day 2 — Linux Operations & File Permissions

Date: Sunday, March 8, 2026
-we had Morning Revision

    Reinforced the rule: "In Linux, everything is a file"
    Reviewed Red Team vs Blue Team directory relevance
    Revisited the Linux file hierarchy
    Discussed how understanding the file system is the foundation of both offense and defense

🔑 File Permissions — all about 4-2-1
Why do we use permission?

because File permissions controls who can read, write, or execute a file.

Permissions are applied to three groups basically:

[Owner]  [Group]  [Others]

The Octal        Symbol    (Numeric) System
Permission
Read 	           r 	         4
Write 	           w 	         2
Execute 	   x             1	
No access 	   -             0

You add the values together for each group:
Octal 	Meaning 	Binary
7 	Read + Write + Execute 	111
6 	Read + Write 	110
5 	Read + Execute 	101
4 	Read only 	100
0 	No permission 	000
Common Permission Sets
Notation 	      Their Meaning 	
755 	          Owner: all / Group+Others: read+execute 	
644 	          Owner: read+write / Group+Others: read 
700 	          Owner: all / Group+Others: none 	
777 	          Everyone: all 
400 	          Owner: read only 	
How to Read Symbolic Permissions

-rwxr-xr-x
│└──┘└──┘└──┘
│  │    │    └── Others (r-x = 5)
│  │    └─────── Group  (r-x = 5)
│  └──────────── Owner  (rwx = 7)
└─────────────── File type (- = regular file, d = directory)

Special Permissions (Advanced)
Permission 	Octal 	Symbol 	Effect
SUID 	4000 	s in owner execute 	Runs with owner's privileges
SGID 	2000 	s in group execute 	Runs with group's privileges
Sticky Bit 	1000 	t at end 	Only owner can delete (used in /tmp)
💻 Essential Terminal Commands
Navigation

pwd                  # Print Working Directory — where am I?
ls                   # List files
ls -la               # List all files including hidden (dotfiles)
ls -lt               # List by time (newest first)
cd /path/to/dir      # Change directory
cd ..                # Go up one level
cd ~                 # Go to home directory

File & Directory Management

mkdir foldername             # Create a directory
mkdir -p parent/child/sub    # Create nested directories
touch filename.txt           # Create empty file
cat filename.txt             # View file contents
cp source dest               # Copy file
mv source dest               # Move or rename file
rm filename.txt              # Delete file
rm -rf foldername            # Force delete directory
echo "text" > file.txt       # Write text to file
echo "text" >> file.txt      # Append text to file

Permissions

chmod 755 script.sh          # Set permissions with octal
chmod +x script.sh           # Add execute permission (symbolic)
chmod -w file.txt            # Remove write permission
chown user:group file.txt    # Change file owner

Brace Expansion — Creating Structures in One Command

# Create a full nested structure in one command:
mkdir -p project/{src,bin,lib,docs/{pdf,txt}}

# Create multiple files at once:
touch test{1..5}.txt

- Challenge — Flag Hunting
The Challenge

Mentors gave us a payload (it was an xecutable file). We ran it, and it hid 3 flags at different locations on the system. 

:Useful Tools for Flag Hunting
ExifTool
👉 Reads hidden metadata in images, PDFs, etc.
💡 You already used it to find a flag 🔥

file
👉 Tells you what a file really is

file challenge.jpg

strings
👉 Pulls readable text from files

strings file.bin
🖼️ 2. Steganography Tools

For hidden data inside images/audio.

StegSeek
👉 Fast password cracking for steghide files
Steghide
👉 Embed/extract hidden data manually
zsteg
👉 Finds hidden data in PNG/BMP
🔐 3. Password Cracking

When something is locked 🔒

John the Ripper
👉 Crack hashes/passwords
Hashcat
👉 GPU-powered cracking (very fast ⚡)
📂 Wordlists:
rockyou.txt (your best friend)
📦 4. File Extraction & Forensics

For hidden layers inside files

Binwalk
👉 Finds embedded files inside files

binwalk -e file.bin
foremost
👉 Recovers hidden/deleted files
🌐 5. Web Challenges

When flags hide in websites 🌍

Burp Suite
👉 Intercept and modify requests
curl
👉 Send requests manually
🧪 6. Encoding & Decoding

Flags love disguises 🎭

CyberChef
👉 Decode Base64, hex, ROT13, etc.

Examples:

echo "SGVsbG8=" | base64 -d
🧠 7. Basic Linux Commands (UNDERRATED 💀)

These win challenges more than tools sometimes:

ls        # list files
cat       # read file
grep      # search text
find      # locate files
cd        # navigate
🧠 Golden Strategy (IMPORTANT)

Always go in this order:

🔍 Check metadata (ExifTool)
📄 Check file type (file, strings)
🖼️ Try stego tools
🔐 Try passwords/wordlists
📦 Extract hidden layers
strings filename        
grep -i "flag" file      
find / -name "*flag*"   
binwalk filename        
hexdump -C file | less   
exiftool file            

📝  Homeworks we had on that day:

    -Find Flag 3 from the payload challenge
    -Practice chmod with different permission combinations
    -Try Bandit levels 5–10 (uses find and grep heavily)
    -Push today's notes to GitHub
