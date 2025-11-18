# Memmory Dump

⇒&nbsp;&nbsp; First, we'll dump the process memory of LSASS. Windows allows us to create a dump file, which is a snapshot of a given process. This dump includes loaded libraries and application memory. In this example, we'll create the dump file with Task Manager.  
⇒&nbsp;&nbsp; To open Task Manager we'll right-click the task bar and select it. Next, we'll navigate to the Details tab, locate the `lsass.exe` process, right-click it and choose Create dump file as shown  

<img src="https://imgur.com/Xn3qKIm.png" height="70%" width="75%" alt="Memmory Dump"/>

⇒&nbsp;&nbsp; After dumping the process memory, the location of the dump file is presented in a popup

<img src="https://imgur.com/hS0zZEY.png" height="70%" width="75%" alt="Memmory Dump"/>

⇒&nbsp;&nbsp; Once the dump file is created, we can copy it from the target to our local Windows client where we can parse it with `Mimikatz`.    
⇒&nbsp;&nbsp; When opening a dump file in Mimikatz, the target machine and the processing machine must have a matching OS and architecture. For example, if the dumped LSASS process was from a Windows 10 64-bit machine; we must also parse it on a Windows 10 or Windows 2016/2019 64-bit machine. However, processing the dump file requires neither an elevated command prompt nor `privilege::debug`.  
⇒&nbsp;&nbsp; In this example, we'll simulate offline parsing by copying the dump file to the C:\Toools\Mimikatz\ folder of the Windows 10 victim VM and we'll process it with Mimikatz there.  
⇒&nbsp;&nbsp; First, we'll run `sekurlsa::minidump`, supplying the name of the dump file to parse, followed by sekurlsa::logonpasswords to dump cached credentials:  

<img src="https://imgur.com/aHdVulO.png" height="70%" width="75%" alt="Memmory Dump"/>

⇒ This successfully dumps the admin domain user's credentials, and does not require Mimikatz on the target machine.  
⇒ There is, however, one obvious disadvantage to this technique: Task Manager cannot be run as a command line tool, so we'll need GUI access to the target. Alternatively, we can create the dump file from the command line with `ProcDump` from SysInternals.  





























