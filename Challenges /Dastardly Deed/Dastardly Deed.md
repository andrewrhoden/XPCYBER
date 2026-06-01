# Dastardly Deed with Disappeared Data 

## Overview

This lab simulates a digital forensics investigation where encrypted and hidden data must be recovered from a terminated employee's seized files. The objective is to uncover and decrypt sensitive information that was deliberately. obscured using steganography, encoding techniques, and encryption.
The investigation involves extracting hidden data from a PNG file, decoding layered encoded content, decrypting AES-protected files, and recovering a password-protected ZIP archive using a known-plaintext attack. The workflow demonstrates practical digital forensics methods used in real-world incident response scenarios. The final goal is to fully recover and document the decrypted data while following a structured forensic analysis process across Windows and Kali Linux environments.


## Tools Used

* Java Runtime Environment (JRE)
* Digital Invisible Ink Toolkit (DIIT)
* 7-Zip
* CyberChef
* AESCrypt
* SMB (Windows file sharing)
* Kali Linux
* PkCrack

⸻

### Step 1: Install Java (Windows)

DIIT requires Java to run, as it is a Java-based steganography tool.

Verify installation:

java -version

If not installed, Java can be downloaded from:
https://www.java.com

⸻

### Step 2: Extract Hidden Data from PNG Using DIIT

A PNG image (dino.png) contains a hidden message embedded using steganography.

Launch DIIT manually:

java -jar "E:\Ione\tools\steganography\diit\diit-1.5.jar"

DIIT Configuration:

* Open Decode tab
* Select Image: E:\Ione\dino.png
* Output file: C:\Users\playerone\Desktop\hidden.txt
* Select algorithm: BlindHide
* Execute decoding

The default algorithm (BattleSteg) fails to detect the hidden message, but BlindHide successfully extracts it.

Extracted Output:

141CB0BD4EB6B9EDC57188F3B7859A05D9744C64FCAA91A312A60B4135E0DD8B

This value is used as the password for the next stage.

⸻

### Step 3: Extract Protected ZIP File

The extracted hash is used to unlock safe.zip.

Using 7-Zip:

& "C:\Program Files\7-Zip\7z.exe" e E:\Ione\safe.zip `
-p141cb0bd4eb6b9edc57188f3b7859a05d9744c64fcaa91a312a60b4135e0dd8b `-o"E:\Ione\Safe"

Extracted Contents:

* astral.jpg.aes
* secretkey.txt

⸻

### Step 4: Decode Secret Key Using CyberChef

The file secretkey.txt contains a Base64-encoded string.

Instead of manual decoding, CyberChef was used for faster analysis.

CyberChef operation:

* From Base64 → UTF-8 decode

Resulting output:

* A cryptographic key used for AES decryption

⸻

### Step 5: Decrypt AES File

The encrypted file astral.jpg.aes is decrypted using AESCrypt with the recovered key:

aescrypt.exe -d -p <AES_KEY> E:\Ione\Safe\astral.jpg.aes

Output:

* astral.jpg

⸻

### Step 6: Transfer Files to Kali via SMB

Since both machines were on the same network, SMB file sharing was used for transfer.

Shared folder setup:

* Windows share containing project files
* Mounted on Kali Linux via SMB client

Files transferred:

* astral.jpg
* exfiltratroll.zip

⸻

### Step 7: Install and Prepare PkCrack (Kali Linux)

PkCrack was used for a known-plaintext attack on the encrypted ZIP file.

Clone repository:

cd ~
git clone https://github.com/keyunluo/pkcrack

Set execute permissions for PkCrack
chmod +x ~/pkcrack/bin/pkcrack

⸻

### Step 8: Create Plaintext ZIP Archive

A plaintext ZIP archive was created using 7-Zip to ensure compatibility with PkCrack’s compression requirements.

cd ~/Desktop/Ione
7z a astral.zip astral.jpg

⸻

### Step 9: Run PkCrack (Known-Plaintext Attack)

~/pkcrack/bin/pkcrack -C ~/Desktop/Ione/exfiltratroll.zip -c astral.jpg -P ~/Desktop/Ione/astral.zip -p astral.jpg -d ~/Desktop/Ione/cracked.zip -a

Explanation of parameters:

Flag	Description
-C	Encrypted ZIP file
-c	Known file inside encrypted archive
-P	Plaintext ZIP archive
-p	File inside plaintext ZIP
-d	Output decrypted ZIP
-a	Scan all files for match

⸻

### Step 10: Extract Decrypted Archive

Once the attack completes successfully, extract the decrypted archive:

cd ~/Desktop/Ione
unzip cracked.zip

⸻

### Step 11: Final File Handling

The recovered files from the decrypted archive were extracted and copied to the Windows C: drive as part of the final output staging process.


 

 [Lab Report PDF](AndrewRhoden_NCPReport160067.pdf)