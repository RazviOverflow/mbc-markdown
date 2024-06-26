<table>
<tr>
<td><b>ID</b></td>
<td><b>F0012</b></td>
</tr>
<tr>
<td><b>Objective(s)</b></td>
<td><b><a href="../persistence">Persistence</a></b></td>
</tr>
<tr>
<td><b>Related ATT&CK Techniques</b></td>
<td><b>Boot or Logon Autostart Execution: Registry Run Keys / Startup Folder (<a href="https://attack.mitre.org/techniques/T1547/001/">T1547.001</a>)</b></td>
</tr>
<tr>
<td><b>Version</b></td>
<td><b>2.3</b></td>
</tr>
<tr>
<td><b>Created</b></td>
<td><b>2 August 2022</b></td>
</tr>
<tr>
<td><b>Last Modified</b></td>
<td><b>30 April 2024</b></td>
</tr>
</table>


# Registry Run Keys / Startup Folder

Malware may add an entry to the Windows Registry run keys or startup folder to enable persistence. [[1]](#1)

See ATT&CK: **Boot or Logon Autostart Execution: Registry Run Keys / Startup Folder ([T1547.001](https://attack.mitre.org/techniques/T1547/001/))**. 

## Use in Malware

|Name|Date|Method|Description|
|---|---|---|---|
|[**TrickBot**](../xample-malware/trickbot.md)|2016|--|The malware has an auto-start service that allows it to run whenever the machine boots. [[16]](#16)|
|[**Poison Ivy**](../xample-malware/poison-ivy.md)|2005|--|To start itself at system boot, Poison Ivy adds registry entries. [[2]](#2)|
|[**Hupigon**](../xample-malware/hupigon.md)|2013|--|Hupigon drops the file "Systen.dll" and adds the registry entry: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify\BITS DllName = "%System%\Systen.dll". [[3]](#3)|
|[**Hupigon**](../xample-malware/hupigon.md)|2013|--|Hupigon persists via Run registry key. [[3]](#3)|
|[**Terminator**](../xample-malware/terminator.md)|2013|--|The Terminator RAT sets "2019" as Windows' startup folder by modifying a registry value. [[4]](#4)|
|[**CryptoLocker**](../xample-malware/cryptolocker.md)|2013|--|The malware creates an "autorun" registry key. [[5]](#5)|
|[**GoBotKR**](../xample-malware/gobotkr.md)|2019|--|GoBotKR installs itself under registry run keys to establish persistence. [[6]](#6)|
|[**Kovter**](../xample-malware/kovter.md)|2016|--|The malware writes an autorun registry entry. [[7]](#7)|
|[**Rombertik**](../xample-malware/rombertik.md)|2015|--|The malware will proceed to install itself in order to ensure persistence across system reboots before continuing on to execute the payload. To install itself, Rombertik first creates a VBS script named “fgf.vbs”, which is used to kick off Rombertik every time the user logs in, and places the script into the user’s Startup folder. [[8]](#8)|
|[**Ursnif**](../xample-malware/ursnif.md)|2016|--|The malware adds registry entries to ensure automatic execution at system startup. [[9]](#9)|
|[**BlackEnergy**](../xample-malware/blackenergy.md)|2007|--|BlackEnergy 3 variant drops its main DLL component and then creates a .lnk shortcut to that file in the startup folder, allowing it to persist via a Run registry key. [[10]](#10) [[17]](#17)|
|[**Conficker**](../xample-malware/conficker.md)|2008|--|To start itself at system boot, the virus saves a copy of its DLL form to a random filename in the Windows system folder, then adds registry keys to have svchost.exe invoke that DLL as an invisible network service. [[11]](#11)|
|[**DarkComet**](../xample-malware/dark-comet.md)|2008|--|DarkComet adds several registry entries to enable automatic execution at startup. [[12]](#12)|
|[**Emotet**](../xample-malware/emotet.md)|2018|--|To start itself at system boot, Emotet adds the downloaded payload to the registry to maintain persistence. [[13]](#13)|
|[**Bagle**](../xample-malware/bagle.md)|2004|--|Bagle adds registry keys to enable its automatic execution at every system startup. [[14]](#14)|
|[**Vobfus**](../xample-malware/vobfus.md)|2016|--|Malware adds registry keys to enable startup after reboot. [[15]](#15)|
|[**Redhip**](../xample-malware/redhip.md)|2011|--|Redhip persists via a Run registry key. [[17]](#17)|
|[**WannaCry**](../xample-malware/wannacry.md)|2017|--|WannaCry creates two registry run keys to ensure persistence. [[18]](#18)|
|[**CryptoWall**](../xample-malware/cryptowall.md)|A copy of Crytowall is placed in the startup folder and a directory at the root of the system drive. Also adds multiple "autostart" registry keys. [[19]](#19)|

## Detection

|Tool: capa|Mapping|APIs|
|---|---|---|
|[persist via Run registry key](https://github.com/mandiant/capa-rules/blob/master/persistence/registry/run/persist-via-run-registry-key.yml)|Registry Run Keys / Startup Folder (F0012)|--|

|Tool: CAPE|Mapping|APIs|
|---|---|---|
|[persistence_bootexecute](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/persistence_bootexecute.py)|Registry Run Keys / Startup Folder (F0012)|RegSetValueExA, RegSetValueExW, NtSetValueKey|
|[geodo_banking_trojan](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/banker_geodo.py)|Registry Run Keys / Startup Folder (F0012)|--|
|[persistence_autorun](https://github.com/CAPESandbox/community/tree/master/modules/signatures/windows/persistence_autorun.py)|Registry Run Keys / Startup Folder (F0012)|NtSetValueKey, RegSetValueExA, RegSetValueExW, CreateServiceW, CreateServiceA|

### F0012 Snippet
<details>
<summary> Persistence::Registry Run Keys/Startup Folder </summary>
SHA256: 0b8e662e7e595ef56396a298c367b74721d66591d856e8a8241fcdd60d08373c
Location: 0x402994
<pre>
push    eax     ; where to store handle to created/opened registry key
push    u_SOFTWARE\Microsoft\Windows\Curre_00429bb8     ; subkey to create -- in this case SOFTWARE\Microsoft\Windows\Current\Version\Run
push    0x80000001      ; predefined registry key HKEY_CURRENT_USER
call    dword ptr [->ADVAPI32.DLL::RegCreateKeyW]       ; call to Windows API function to create the registry key HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\Current\Version\Run
lea     ecx, [esp + 0x70]
lea     edx, [ecx + 0x2]
nop     dword ptr [eax]
mov     ax, word ptr [ecx]
add     ecx, 0x2
test    ax, ax
jnz     lab_004029b0
sub     ecx, edx
sar     ecx, 1
lea     eax, [ecx * 0x2 + 0x2]
push    eax     ; size of data to write to registry key
lea     eax, [esp + 0x74]
push    eax     ; data to write to registry key
push    0x1     ; indicates that the type of value to be written to registry key is a string
push    0x0     ; reserved parameter, must be NULL
push    u_WinHoster_00429c14    ; name of the value to add to the key -- in this case, WinHoster
push    dword ptr [esp + local_264]     ; handle to open registry key
call    dword ptr [->ADVAPI32.DLL::RegSetValueExW]      ; API call to set registry value
</pre>
</details>

## References

<a name="1">[1]</a> https://threatvector.cylance.com/en_us/home/windows-registry-persistence-part-2-the-run-keys-and-search-order.html

<a name="2">[2]</a> https://www.cyber.nj.gov/threat-center/threat-profiles/trojan-variants/poison-ivy

<a name="3">[3]</a> https://www.trendmicro.com/vinfo/us/threat-encyclopedia/malware/HUPIGON

<a name="4">[4]</a> https://www.fireeye.com/content/dam/fireeye-www/current-threats/pdfs/pf/file/fireeye-hot-knives-through-butter.pdf

<a name="5">[5]</a> https://www.secureworks.com/research/cryptolocker-ransomware

<a name="6">[6]</a> https://www.welivesecurity.com/2019/07/08/south-korean-users-backdoor-torrents/

<a name="7">[7]</a> https://blog.malwarebytes.com/threat-analysis/2016/07/untangling-kovter/

<a name="8">[8]</a> https://blogs.cisco.com/security/talos/rombertik

<a name="9">[9]</a> https://www.trendmicro.com/vinfo/us/threat-encyclopedia/malware/PE_URSNIF.A2?_ga=2.131425807.1462021705.1559742358-1202584019.1549394279

<a name="10">[10]</a> https://blog-assets.f-secure.com/wp-content/uploads/2019/10/15163408/BlackEnergy_Quedagh.pdf

<a name="11">[11]</a> https://en.wikipedia.org/wiki/Conficker

<a name="12">[12]</a> https://blog.malwarebytes.com/threat-analysis/2012/06/you-dirty-rat-part-1-darkcomet/

<a name="13">[13]</a> https://cofense.com/blog/recent-geodo-malware-campaigns-feature-heavily-obfuscated-macros/

<a name="14">[14]</a> https://www.trendmicro.com/vinfo/us/threat-encyclopedia/malware/WORM_BAGLE.U/

<a name="15">[15]</a> https://securitynews.sonicwall.com/xmlpost/revisiting-vobfus-worm-mar-8-2013/

<a name="16">[16]</a> https://www.trendmicro.com/en_us/research/18/k/trickbot-shows-off-new-trick-password-grabber-module.html

<a name="17">[17]</a> capa v4.0, analyzed at MITRE on 10/12/2022

<a name="18">[18]</a> https://www.mandiant.com/resources/blog/wannacry-malware-profile

<a name="19">[19]</a> https://www.secureworks.com/research/cryptowall-ransomware
