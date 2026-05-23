D
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
Reads hidden metadata in images, PDFs, etc.

file
Tells you what a file really is

file challenge.jpg

strings
Pulls readable text from files

strings file.bin
2 . Steganography Tools

For hidden data inside images/audio.

StegSeek
Fast password cracking for steghide files
Steghide Embed/extract hidden data manually
zsteg
 Finds hidden data in PNG/BMP
 3 . Password Cracking

When something is locked 

John the Ripper
Crack hashes/passwords
Hashcat
GPU-powered cracking
Wordlists:
rockyou.txt (your best friend)
4 . File Extraction & Forensics

For hidden layers inside files

Binwalk
 Finds embedded files inside files

binwalk -e file.bin
foremost
 Recovers hidden/deleted files
5 . Web Challenges

When flags hide in websites 

Burp Suite
 Intercept and modify requests
curl
 Send requests manually
 6 . Encoding & Decoding

Flags love disguises 

CyberChef
 Decode Base64, hex, ROT13, etc.

Examples:

echo "SGVsbG8=" | base64 -d
 7 . Basic Linux Commands
commands that uses a lot during a challenge:

ls        # list files
cat       # read file
grep      # search text
find      # locate files
cd        # navigate

Homeworks we had on that day:

    -Find Flag 3 from the payload challenge
    -Practice chmod with different permission combinations
    -Try Bandit levels 5–10 (uses find and grep heavily)
    -Push today's notes to GitHub
