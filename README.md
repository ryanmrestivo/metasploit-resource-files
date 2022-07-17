## METASPLOIT RESOURCE FILES

<blockquote>Resource scripts provides an easy way for us to automate repetitive tasks in Metasploit. Conceptually they're just like batch scripts, they contain a set of commands that are automatically and sequentially executed when you load the script in Metasploit. You can create a resource script by chaining together a series of Metasploit console commands or by directly embedding Ruby to do things like call APIs, interact with objects in the database, modules and iterate actions. This repository is designed to demonstrate what resource files [ERB](https://www.offensive-security.com/metasploit-unleashed/custom-scripting/) can accomplish when automating tasks in msfconsole, and they are written to take advantage of multi-hosts-exploitation-scan tasks (manage large databases of hosts) from scanning the LAN for alive hosts, scan attackers input rhosts or scan wan networks in search of rhosts to exploit.</blockquote>

! metasploit-resource-files-image

<br/>

## Mosquito - Automating reconnaissance and brute force attacks

! metasploit-resource-files-image

<br/>

### Index
[1] [Project History](https://github.com/ryanmrestivo/metasploit-resource-files#project-history)<br/>
[2] [Framework Description](https://github.com/ryanmrestivo/metasploit-resource-files#framework-description)<br/>
[3] [Framework Dictionary files](https://github.com/ryanmrestivo/metasploit-resource-files#framework-dictionary-files)<br/>
[4] [Framework Dependencies](https://github.com/ryanmrestivo/metasploit-resource-files#framework-dependencies)<br/>
[5] [Framework Limitations](https://github.com/ryanmrestivo/metasploit-resource-files#framework-limitations)<br/>
[6] [Framework Download] (https://github.com/ryanmrestivo/metasploit-resource-files#framework-download)<br/>
[7] [Framework help-update-install-execution] (https://github.com/ryanmrestivo/metasploit-resource-files#framework-help-update-install-execution)<br/>
[8] [Project References url's](https://github.com/ryanmrestivo/metasploit-resource-files#referencies)<br/>
[9] [Project Acknowledgment] (https://github.com/ryanmrestivo/metasploit-resource-files#project-acknowledgment) <br/>
[10] [Project releases description] (https://github.com/ryanmrestivo/metasploit-resource-files/releases) <br/>

---
<br/>

### Project History
Mosquito.sh (**BASH**) script was written for the purpose of automating the resource files (**ERB**) contained in this [repository](https://github.com/ryanmrestivo/metasploit-resource-files). Each resource file is written to allow users to run them in three different ways, from scan the Local Lan, scan user inputs (**RHOSTS/LHOSTS**) or randomly scan the **WAN** network for possible targets to add to Metasploit database.

! metasploit-resource-files-image

**WARNING:** In 'Random search WAN for rhosts' its advice to use default **LIMIT** values (approximately 4 to 5 minutes scan)

---
<br/>

### Framework Description
Mosquito as first step uses Nmap to search-recon hosts information (or possible targets), then adds all the hosts found (with open ports) to Metasploit database to be used in further recon, exploration or brute force jobs carried out later with msf.

! metasploit-resource-files-image
! metasploit-resource-files-image
! metasploit-resource-files-image

Mosquito allow us to scan Local Lan or WAN networks using Nmap (search-recon) and Metasploit (recon-exploration-brute-force), but unlike msf the scans performed by Nmap will use a fake UserAgent (IPhone/Safari) stealth scans (SYN ack) and Cloak scan(s) with decoys (-D decoy_ip,decoy_ip,ME) that makes forensic IDS analysis more difficult to identify the attack.

! metasploit-resource-files-image

**WARNING:** Stealth techniques will not prevent detection.

    stealth techniques used to evade IDS analysis
    -------------------------------------------
    nmap -sS [stealth scan using SYN ack]
    nmap -D 188.234.11.254,167.113.24.80,ME [Cloak a scan with decoys]
    nmap --script-args http.useragent="Apache-HttpClient/4.0.3 (java 1.5)" [spoof your UserAgent]

Mosquito also allows search-scan-exploit-brute-force multiple targets at the same time (multi-tasking).

! metasploit-resource-files-image
! metasploit-resource-files-image

Each valid credentials found (brute-force) will spawn a shell session to remote host.

! metasploit-resource-files-image
! metasploit-resource-files-image

[jump to top](https://github.com/ryanmrestivo/metasploit-resource-files#index)

---
<br/>

### Framework Dictionary files
Initially all resource scripts that this project contains are written to allow is users to input dictionary file absolute path before the scan take place (own dictionary), but mosquito ships with its own set of dictionary files to assist in brute force tasks, and it does not allow is users to input another dictionary file when running mosquito framework.

Mosquito users can still improve the existing dictionary(s) by edit them before executing the framework. <br/>
All dictionary files can be found in project working directory under: 'meatsploit-resource-files/bin/worldlists'.

! metasploit-resource-files-image

[jump to top] (https://github.com/ryanmrestivo/metasploit-resource-files#index)

---
<br/>

### Framework Dependencies
|Dependencie|Function|Install|
|---|---|---|
|zenity|Bash script GUI interfaces|[zenity download](https://help.gnome.org/users/zenity/) * |
|nmap| WAN random search; recon | [nmap download](https://nmap.org/download.html) * |
|metasploit| msf database; recon; exploitation; brute force | [metasploit download](https://www.metasploit.com/download) |
|geoiplookup| hosts geo location | sudo apt-get install geoip-bin * |
|curl| hosts geo location | sudo apt-get install curl * |
|dig| ip address resolver | Linux native installed package ** |
|vulners.nse| CVE recon | mosquito native nse script * |
|freevulnsearch.nse| CVE recon | mosquito native nse script * |
|http-winrm.nse| http winrm recon | mosquito native nse script * |

    * ./mosquito.sh -i = to install all packages/scripts/modules
    ** Linux native installed package = no need to install it

**Hint:** All mosquito dependencies can be easy installed by running: **sudo ./mosquito.sh -i**<br/>
Additionally, to the dependencies described above, different resource scripts require different msf auxiliaries
or Nmap nse additional scripts installed, the -i switch in mosquito allow us to download/install all those extra modules fast and easy.

[jump to top] (https://github.com/ryanmrestivo/metasploit-resource-files#index)

---
<br/>

### Framework Limitations
**a)** mosquito only accepts IP address inputs, not domain names <br/>
**b)** brute forcing takes time, use 'CTRL+C' to skip current task(s)<br/>
**c)** mosquito dictionaries can be found in resource_files/bin/worldlists<br/>
**d)** finding valid credentials sometimes fails to spawn a shell<br/>
**e)** multiple sessions open (msf) might slowdown your pc<br/>

**Hint:** This resource scripts requires that the msf database to be empty of hosts and services data. The main reason why this script creates a new workspace named **'mosquito'** and stores all data inside that workspace while working, then the resource script deletes the **'mosquito'** workspace in the end of execution and leave *default database intact.

[jump to top] (https://github.com/ryanmrestivo/metasploit-resource-files#index)

---
<br/>

### Framework Download
```
[download] git clone https://github.com/ryanmrestivo/metasploit-resource-files.git
[permissions] cd resource_files && find ./ -name "*.sh" -exec chmod +x {} \;
```

### Framework help-update-install-execution

    [help] sudo ./mosquito.sh -h
! metasploit-resource-files-image

    [update] sudo ./mosquito.sh -u
! metasploit-resource-files-image


[jump to top] (https://github.com/ryanmrestivo/metasploit-resource-files#index)

---
<br/>

### References
[1] [Project home page] (https://github.com/ryanmrestivo/metasploit-resource-files) <br/>
[2] [Project wiki - dependencies] (https://github.com/ryanmrestivo/metasploit-resource-files/wiki/Offensive-Resource_Files-%7C-Dependencies) <br/>
[3] [offensive resource script - geo_location.rc] (https://github.com/ryanmrestivo/metasploit-resource-files/wiki/Offensive-Resource_Files--%7C-Geo_Location) <br/>
[4] [offensive resource script - post_exploitation.rc] (https://github.com/ryanmrestivo/metasploit-resource-files/wiki/post_exploitation.rc-%7C-offensive-resource-script) <br/>
[5] [hacking-material-books - metasploit_resource_files] (https://github.com/r00t-3xp10it/hacking-material-books/blob/master/metasploit-RC%5BERB%5D/metasploit_resource_files.md#metasploit-resource-files) <br/>

<br/>