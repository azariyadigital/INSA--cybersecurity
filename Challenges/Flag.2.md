Flag 1 — Metadata

Tool: exiftool

exiftool challenge.jpg

Found in the Comment field.

FLAG{ALWAYS_Check_Metadata}
Flag 2 — Strings

Tool: strings + grep

strings challenge.jpg | grep "FLAG"

FLAG{Strings_leak_information}
Flag 3 — Steghide (Password Protected)

Tool: stegseek (bruteforce), steghide

# Bruteforce the passphrase
stegseek challenge.jpg rockyou.txt

# Passphrase found: password123
# Extracted to: challenge.jpg.out

cat challenge.jpg.out

FLAG{steghide_protected}
Flag 4 — Appended ZIP

Tool: binwalk, dd, unzip, base64

# Detect hidden file
binwalk challenge.jpg

# Extract ZIP at offset 75667
dd if=challenge.jpg bs=1 skip=75667 of=hidden.zip

# Unzip
unzip hidden.zip

# Decode double Base64
echo "Umt4QlIzdGhjSEJsYm1SbFpGOTZhWEI5Q2c9PQo=" | base64 -d | base64 -d

FLAG{appended_zip}
Flag 5 — I didn't found it.
