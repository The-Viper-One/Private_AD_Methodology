**Description**

Domain Controllers should be strictly isolated from general internet access and must never be used for routine web browsing. During the assessment, it was identified that the in-scope Domain Controllers had unrestricted internet connectivity, **including one which had Google Chrome installed**, strongly suggesting they may be used for web browsing activities. 

As Domain Controllers are typically accessed using highly privileged accounts (e.g., Domain or Enterprise Admins). Engaging in general browsing from such systems dramatically increases the risk of compromise, due to increased exposure to external threats such as malware, phishing, and browser-based exploits.

**Remediation**

Perimeter firewalls should be configured to block outbound internet access from Domain Controllers. While Domain Controllers may require intersite communication, such traffic can be explicitly permitted by following Microsoft’s recommended port and protocol guidelines. Additionally, application control mechanisms such as AppLocker or Windows Defender Application Control (WDAC)should be enforced on Domain Controllers to prevent the execution of web browsers and other non-essential software, thereby reducing the risk of misuse and limiting the system's exposure to internet-borne threats.