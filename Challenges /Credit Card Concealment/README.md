 # XP Cyber Challenge Write-Up
# Challenge: Credit Card Concealment

## Overview

This challenge involved the forensic investigation of a USB storage device recovered from an employee named Rob after he was observed leaving company premises with unauthorized removable media.

According to the initial security report, Rob stated that he was only carrying the USB drive because he wanted to have pictures of his cat available while working. Security reviewed the contents of the USB drive and reported that only cat pictures were present.

Due to the potential risk of sensitive company information leaving the organization, the security team requested a deeper forensic examination of the USB device to determine whether additional hidden or concealed information existed.

The objective of this investigation was to analyze the USB drive, identify any concealed sensitive information, and submit the required evidence through the XP Cyber incident response form.

The challenge required answering the following question:

**What is Penny Harris' credit card number?**

The investigation successfully identified the concealed credit card information:

**62945496681673473**



# Scenario

An employee named Rob was caught leaving the company premises with a USB storage device. Company policy prohibited the use of removable storage devices entering or leaving the building due to the risk of unauthorized data transfer.

During the initial security investigation, the contents of the USB drive appeared to contain only pictures of Rob's cat. However, due to concerns that sensitive information may have been concealed, a forensic investigation was initiated.

The security team requested that the USB drive be analyzed to determine whether Rob was hiding additional data within the device.

The investigation was performed from the Security-Desk virtual machine using forensic analysis tools available in the XP Cyber environment.



# Investigation Environment

The investigation was conducted using:

**Virtual Machine:**
- Security-Desk

**Operating System Tools:**
- Linux command line utilities<br>
- Sleuth Kit forensic tools

**Evidence File Created:**
rob_usb.dd

The USB drive was analyzed using a forensic copy rather than the original device to preserve evidence integrity.



# Phase 1: Identifying the USB Device

The first step was identifying the connected storage devices available on the Security-Desk system.

The following command was used: lsblk

This command displayed all block devices connected to the system and allowed identification of the USB storage device.

The output showed that the USB device was connected as: /dev/sdb

Additional confirmation was performed using:

sudo fdisk -1

The fdisk command was used to view detailed disk information including device size and partition layout. The USB device was confirmed as the target evidence source.

![alt text](<Screenshot 2026-07-26 at 10.34.47 PM.png>)

# Phase 2: Creating a Forensic Image

To preserve the original evidence, a bit-for-bit forensic image of the USB device was created.

The following command was used:

sudo dd if=/dev/sdb of=rob_usb.dd bs=4M status=progress <br>

if=/dev/sdb Specifies the input USB device <br>

of=rob_usb.dd Specifies the output forensic image file <br>

bs=4M Copies data in 4 megabyte blocks <br>

status=progress Displays acquisition progress <br>

### The resulting file:

### rob_usb.dd

served as the forensic copy used throughout the investigation.

![alt text](<Screenshot 2026-07-26 at 10.37.36 PM.png>)


# Phase 3: Verifying Evidence Integrity

After acquiring the forensic image, a SHA-256 hash was generated:

sha256sum rob_usb.dd

Hashing is an important forensic process because it confirms that the evidence image remains unchanged during analysis. The calculated hash value was recorded as part of the evidence handling process.


# Phase 4: Partition Analysis Using Sleuth Kit

The forensic image was analyzed using Sleuth Kit.
The first step was identifying the partition structure:
 
mmls rob_usb.dd

mmls displays partition information from a disk image, including: <br>
• Partition type<br>
• Starting sector<br>
• Ending sector<br>
• Partition size

The output showed that the main filesystem partition began at sector: 2048

This offset was required for further filesystem analysis.

![alt text](<Screenshot 2026-07-26 at 10.39.24 PM.png>)

# Phase 5: Filesystem Examination

The contents of the filesystem were examined using:<br>

fls -o 2048 -d rob_usb.dd<br>

-o 2048 Specifies the filesystem offset<br>
-d Displays deleted entries<br>
-rob_usb.dd Forensic disk image<br>

The output did not reveal any obvious suspicious files. The visible filesystem contents appeared consistent with the original explanation that the drive contained only cat pictures.
At this point, the investigation continued using a lower-level analysis method.


# Phase 6: Raw Data Analysis

Since filesystem analysis did not reveal suspicious files, the forensic image itself was examined.
The following command was used:

strings rob_usb.dd | less

The strings utility extracts readable ASCIl and Unicode text from binary files.
This technique can reveal:<br>
• Deleted text fragments<br>
• Embedded information<br>
• Data hidden outside normal filesystem structures<br>
• Residual information from previous files<br>
During the review of the extracted strings, numerous references to credit card information were discovered.

Further examination revealed sensitive financial information associated with: Penny Harris
The concealed credit card number identified was: 62945496681673473

![alt text](<Screenshot 2026-07-26 at 10.51.07 PM.png>)

![alt text](<Screenshot 2026-07-26 at 10.46.12 PM.png>)


# Phase 7: Incident Response Submission

The discovered information was entered into the XP Cyber challenge incident response form.
Question:

What is Penny Harris' credit card number?

Answer submitted:62945496681673473

The submission was accepted, confirming successful completion of the investigation objective.

![alt text](<Screenshot 2026-07-26 at 10.47.15 PM.png>)

# Lessons Learned

This investigation demonstrated the importance of performing layered forensic analysis when investigating removable media.
Although the USB drive appeared to contain only harmless images, deeper examination of the raw disk image revealed sensitive information that was
not visible through normal file system inspection. 

### Key forensic lessons:<br>
• Visible files do not always represent all stored data.<br>
• Deleted or concealed information may remain recoverable from raw storage.<br>
• Multiple analysis techniques should be used during digital investigations.<br>
• Evidence should always be acquired and analyzed using forensic copies to maintain integrity.


# Tools Used

### Tools 
**lsblk**   Identified connected storage devices<br>
**fdisk**   Displayed disk partition information<br>
**dd**      Created forensic disk image<br>
**sha256sum** Verified evidence integrity<br>
**mmls**       Analyzed partition structure<br>
**fls**     Examined filesystem contents<br>
**strings** Extracted readable data from raw disk image



[XP Cyber Challenge Summary](AndrewRhoden_NCPReport161834.pdf)