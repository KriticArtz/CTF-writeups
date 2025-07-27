Sneaky Patch

Platform:TryHackMe – Premium Room  
Date Completed: July 26, 2025  
Difficulty Rating: Easy  
Tags / Skills Practiced: Kernel Module Analysis, `dmesg`, `strings`, LKM Backdoors, Hex Decoding, Rootkit Detection

1️. Objective  
      A high-value Linux system was compromised by an attacker who implanted a kernel-level backdoor. Traditional detection methods failed to catch it. My task was to investigate the live system, uncover the backdoor, and retrieve the flag.

2. Scoping & Information Gathering  
      - Accessed the live compromised system  
      - Ran `dmesg | tail -n 50` to inspect recent kernel messages  
      - Noticed suspicious module being loaded:
      /lib/modules/6.8.0-1016-aws/kernel/drivers/misc/spatch.ko
      - Confirmed I was `ubuntu` user with `sudo` access
      - Switched to root using `sudo -i`

3. Key Analysis & Backdoor Discovery  
      [Step 1] Targeting the Kernel Module
        - Located the suspicious `.ko` file: `spatch.ko`
        - Recognized that `.ko` files are kernel object binaries and not human-readable
        - Used `strings` to extract readable content from the binary:
          `strings spatch.ko | grep -i backdoor`
      [Step 2] Understanding the Backdoor
        Discovered messages revealing the module was creating a backdoor through /proc/cipher_bd
        Found this key line:
        [CIPHER BACKDOOR] Here's the secret: 54484d7b73757033725f736e33346b795f643030727d0a
        Recognized the string as hex-encoded
      [Step 3] Decoding the Flag
        Decoded the hex using:
        `echo 54484d7b73757033725f736e33346b795f643030727d0a | xxd -r -p`
      Result:
        THM{sup3r_sn34ky_d00r}
4. Flag
      `THM{sup3r_sn34ky_d00r}`
   
5. Lessons Learned
      How to detect suspicious kernel modules via dmesg and lsmod
      Reinforced that .ko files can hide malicious behavior at a deep system level
      Practiced extracting strings from binary files for forensic analysis
      Learned how attackers use /proc as a covert channel to control rootkits
      Honed quick detection techniques using grep and hex decoding

6. Tools Used
      Linux Terminal – dmesg, sudo, strings, grep, xxd
      Basic Hex Decoder – CLI method to convert hex to plaintext
      Root Access – For inspecting and analyzing kernel-level components

7. Remediation & Recommendations
      1. Harden Kernel Module Handling
        Prevent unauthorized kernel modules from being loaded
        Use kernel lockdown modes or load signing mechanisms
        Monitor dmesg logs in real-time for unusual entries

      2. Monitor /proc Activity
        Use host-based intrusion detection to track unusual /proc entries
        Backdoors often use /proc as a stealth communication path

      3. Restrict Root Access
        Only allow trusted users to elevate privileges
        Use audit logs to track when sudo or root access is used

      4. Static Code and Behavior Analysis
        Perform string extraction and static analysis on new .ko files
        Automate scanning of kernel modules for signs of tampering
