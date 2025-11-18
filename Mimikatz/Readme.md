# Mimikatz

⇒ &nbsp;&nbsp; We'll use mimikatz to check for stored credentials on the system  
⇒ &nbsp;&nbsp; Each of mimikatz commands consist of a module and a command delimited by two colons, for example the `privilege::debug` enables `SeDebugPrivilege` access rights, which are required for the following password and hash extraction commands  

<img src="https://imgur.com/jPg5xEb.png" height="70%" width="75%" alt="Mimikatz Steps"/>

⇒&nbsp;&nbsp; We can use various commands to extract passwords from the system e.g sekurlsa::logonpasswords which attempts to extract plaintext passwords and password hashes from all available sources and hence generates a huge amount of output, for better opsec we'll instead use lsadump::sam, which will extract the NTLM hashes from the SAM  
⇒&nbsp;&nbsp; For this command to work as intended, we must first enter token::elevate command to elevate to SYSTEM user privileges  

<img src="https://imgur.com/JoSl7Pq.png" height="70%" width="75%" alt="Mimikatz Steps"/>

<img src="https://imgur.com/6HcDyJ4.png" height="70%" width="75%" alt="Mimikatz Steps"/>

<img src="https://imgur.com/2E8iymo.png" height="70%" width="75%" alt="Mimikatz Steps"/>

⇒&nbsp;&nbsp; Now lets copy this hash value to our kali machine to attempt cracking it as shown  

<img src="https://imgur.com/hjL3xWI.png" height="70%" width="75%" alt="Mimikatz Steps"/>

⇒&nbsp;&nbsp; Next, we'll retrive the correct hash mode from Hashcat's help output  

<img src="https://imgur.com/0qOK7jL.png" height="70%" width="75%" alt="Mimikatz Steps"/>

⇒&nbsp;&nbsp; Next, is choosing a wordlist and rule file  

<img src="https://imgur.com/djWTZrL.png" height="70%" width="75%" alt="Mimikatz Steps"/>

<img src="https://imgur.com/s4agG41.png" height="70%" width="75%" alt="Mimikatz Steps"/>

⇒&nbsp;&nbsp; We'll use mimikatz to check for stored credentials on the system  
⇒&nbsp;&nbsp; Each of mimikatz commands consist of a module and a command delimited by two colons, for example the `privilege::debug` enables `SeDebugPrivilege` access rights, which are required for the following password and hash extraction commands  

<img src="https://imgur.com/w9InJWB.png" height="70%" width="75%" alt="Mimikatz Steps"/>

## Bypassing Securities  
⇒&nbsp;&nbsp; Since 2012 (when Mimikatz was released and cached credential dumping was popularized), Microsoft has developed mitigation techniques: LSA Protection and Windows Defender Credential Guard. In this module, we will focus on LSA protection.  
⇒&nbsp;&nbsp; As previously mentioned, Windows divides its processes into four distinct integrity levels. An additional mitigation level, `Protected Processes Light` (`PPL`) was introduced from Windows 8 onwards, which can be layered on top of the current integrity level.  
⇒&nbsp;&nbsp; In essence, this means that a process running at SYSTEM integrity cannot access or modify the memory space of a process executing at SYSTEM integrity with PPL enabled. To demonstrate this, we'll log on to the Windows 2019 server appsrv01 as the admin user.
⇒&nbsp;&nbsp; LSASS supports PPL protection, which can be enabled in the registry. This is done through the RunAsPPL DWORD value in HKLM\SYSTEM\CurrentControlSet\Control\Lsa with a value of 1  
⇒&nbsp;&nbsp; This protection mechanism is disabled by default due to third-party compatibility issues. On `appsrv01` LSA Protection has already been configured.  
⇒&nbsp;&nbsp; When LSASS is executing as a `Protected Process Light`, Mimikatz fails due to insufficient permissions as shown  

<img src="https://imgur.com/Rz9X8zW.png" height="70%" width="75%" alt="Mimikatz Steps"/>

⇒&nbsp;&nbsp; The sekurlsa::logonpasswords command returns the error value 0x00000005 (`Access denied`).  
⇒&nbsp;&nbsp; PPL protection is controlled by a bit residing in the EPROCESS kernel object associated with the target process. If we could obtain code execution in kernel space, we could disable the LSA protection and dump the credentials.  
⇒&nbsp;&nbsp; Luckily, this can be achieved with Mimikatz since it comes bundled with the `mimidrv.sys` driver.  
⇒&nbsp;&nbsp; We must be local administrator or SYSTEM to dump the credentials, which means we will also have the SeLoadDriverPrivilege privilege and the ability to load any signed drivers. Mimikatz can load the `mimidrv.sys` driver with the `!+` command:  

<img src="https://imgur.com/N0fItqO.png" height="70%" width="75%" alt="Mimikatz Steps"/>

⇒&nbsp;&nbsp; Once the driver is loaded, we can use it to disable the PPL protection for LSASS through the `!processprotect` command while supplying the `/process`: option to specify the name of the process and the `/remove` flag to disable PPL as shown

<img src="https://imgur.com/6tK6BMM.png" height="70%" width="75%" alt="Mimikatz Steps"/>

⇒&nbsp;&nbsp; While this technique will disable the LSA Protection it does require that we upload the mimidrv.sys driver to the victim machine, which may trigger antivirus.
⇒&nbsp;&nbsp; Next, we'll again attempt to dump the cached credentials with `sekurlsa::logonpasswords:`

<img src="https://imgur.com/k5BnfdN.png" height="70%" width="75%" alt="Mimikatz Steps"/>

⇒&nbsp;&nbsp; According to this output, we have bypassed LSA protection and have obtained the domain admin's user NTLM hash.  





