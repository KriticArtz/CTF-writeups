CTF Title: Stolen Mount
Platform: TryHackMe - Premium room
Date Completed: July 22, 2025
Difficulty Rating: Easy
Tags / Skills Practiced: Wireshark, PCAP Analysis, NFS Enumeration, Data Exfiltration, Hash Cracking, File Recovery

1. Objective: 
      An attacker broke into a network, mounted an NFS share, and exfiltrated a sensitive file. I was given a .pcapng file (packet capture of the incident) told to find out what was stolen & how.
2. Scoping and Info Gathering:
      Loaded the packet capture from ~/Desktop/challenge.pcapng into Wireshark
      Set display filters to target NFS-related traffic:
      “nfs || mount || rpc”
      Identified unauthenticated NFS mount attempts and file transfer activity
      Observed several READ_PLUS operations — strong indicator of data exfiltration
3. Key Analysis & Data Recovery
      [Step 1] Digging into the NFS Traffic
        Saw the attacker successfully mounted the NFS share without authentication
        Traffic showed them reading files like packet214.bin and packet286.bin
        Flagged those as high-interest and started investigating both
      [Step 2] Cracking the Archive Password
        Ran strings on packet214.bin and piped it through grep:
        “strings packet214.bin | grep -i pass”
        Found this line:
        9Archive Password: 90eb7723a657b6597100aafef171d9f2
        That’s an MD5 hash : I used CrackStation to crack it
        Password turned out to be: avengers
      [Step 3] Extracting the Stolen File
        Opened packet286.bin in Wireshark
        Found what looked like the start of a PNG file (89 50 4E 47)
        Used Export Packet Bytes to pull out the file
        Tried to open it, got prompted for a password
        Used avengers — it worked
        File was secrets.png — the stolen data 
  	[Step 4] Getting Hidden Flag
        Got prompted with a QR code that gave flag

4. Flag:
	“THM{n0t_S3cur3_f1l3_sh4r1ng}”

5. Lesson Learned:
      How to track NFS exploitation through Wireshark
      Importance of understanding READ_PLUS operations in network forensics
      Real-world use case for strings, hash cracking, and data carving
      Always pay attention to what protocols are open and unauthenticated

6. Tools Used:
      Wireshark
      Linux terminal: strings, grep
      CrackStation (MD5 cracking)
      7-Zip (archive extraction

7. Remediation / Recommendations:
If this was a real-world pen test or assessment, this is what I’d recommend to the company to avoid getting hit like this again:
      [Step 1] Lock Down NFS
        Never expose NFS shares without authentication
        Only export to specific IPs or trusted subnets
        Use settings like root_squash and noexec to limit abuse
      [Step 2] Encrypt & Secure Sensitive Files
        Don't leave sensitive archives or backups unencrypted or protected with weak hashing (MD5 is outdated)
        Use AES encryption or password managers — not just plain zips with basic passwords
      [Step 3] Network Segmentation
        Critical systems like backup servers should be isolated from the main network
        Use VLANs and firewalls to stop lateral movement
      [Step 4] Monitor File Transfers
        Set up logging for NFS activity
        Alert on high-volume reads or abnormal access times
        Consider deploying Zeek or Suricata for deeper traffic inspection
