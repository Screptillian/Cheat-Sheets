# Guest Authentication

```ldapsearch -x -h $ADServer -s base namingcontexts```

```ldapsearch -x -h $ADServer -b "DC=$DomainParam1,DC=$DomainParam2" > $OutFile```

|String|Description|
|---|---|
|memberof|Enumerates Groups|

# LDAPS

```ldapsearch -ZZ -H ldap://$ADSERVER -D $DOMAIN -b "DC=$DomainParam1,DC=$DomainParam2"```

## Note: You will have to modify your ldap.conf (/etc/openldap|ldap/ldap.conf) file to look like (below) to disable certificate verification (host based)

```
HOST my.server.com
PORT
TLS_REQCERT ALLOW
```

# Domain Enumeration

## Active Directory PowerShell Module
```Import-Module ActiveDirectory```

You can also import DLLs, so if you have the Active Directory DLLs you can import those directly with:
```Import-Module Microsoft.ActiveDirectory.Management.dll```

|Command|Description|
|---|---|
|Get-ADDomain|Get Current Domain|
|Get-ADDomain -Identity $domainname|Get Domain of specified Object|
|(Get-ADDomain).DomainSID|Get Domain SID for current Domain|
|Get-ADDomainController|Get Domain Controllers for current Domain|
|Get-ADDomainController -Domain $domainname -Discover|Get Domain Controllers for another domain|

## PowerView

|Command|Description|
|---|---|
|Get-NetDomain|Get Current Domain|
|Get-NetDomain -Domain $domainname|Get object of another Domain|
|Get-DomainSID|Get DomainSID for current domain|
|Get-NetDomainController|Get Domain Controllers for current domain and information about that domain controller|
|Get-NetDomainController -Domain $DomainName | Get Domain Controllers and info about domain controllers of another domain|
|Get-DomainPolicy| Get Domain information about various policies on the domain|
|(Get-DomainPolicy)."Kerberos Policy"|Get Domain information regarding kerberos configuration|
|(Get-DomainPolicy)."system access"|Get Domain information regarding password requirements|

# Domain Trust Enumeration

## Active Directory PowerShell Module
|Command|Description|
|---|---|
|Get-ADTrust|List of all Domain Trusts for the current domain|
|Get-ADTrust -Identity $domainFQDN|List of Domain Trusts for specified domain|
|Get-ADForest|Get Details about the current forest|
|Get-ADForest -Identity $forest|Get details about the specified forest|
|(Get-ADForest).Domains|Get all domains in the current forest|
|Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null"'|Get trusts of a specified forest|

## PowerView
|Command|Description|
|---|---|
|Get-NetDomainTrust|Get list of all domain trusts for the current domain|
|Get-NetDomainTrust -Domain $DomainFQDN | Get list of domain trusts for the specified domain|
|Get-NetForest| Get details about the current forest|
|Get-NetForest -Forest $forest|Get Details about the specified forest|
|Get-NetForestDomain | Get all domains in the current forest|
|Get-NetForestDomain -Forest $forest | Get all domains in the specified forest|
|Get-NetForestCatalog|Get all global catalogs for the current forest|
|Get-NetForestCatalog -Forest $forest | Get all global catalogs for the specified forest|
|Get-NetForestTrust | Get trusts of the current forest|
|Get-NetForestTrust -Forest $forest|Get trusts of the specified forest|

# Domain User Enumeration

## Active Directory PowerShell Module

|Command|Description|
|---|---|
|Get-ADUser -Filter *|Get list of users in current domain and default properties|
|Get-ADUser -Filter * -Properties *| Get list of users and all properties of users in current domain|
|Get-ADUser -Identity $username -Properties *|Get all properties of specified username|
|Get-ADUser -Filter * \| select Name | Get list of usernames|
|Get-ADUser -Filter * -Properties \* \| select name, @{expression={[datetime]::fromFileTime($\_.pwdlastset)}}||
|Get-ADUser -Filter 'Descript -like "\built\"' -Properties Description \| select name, Description|Search user descriptions in current domain|
|Get-ADUser -Identity \$Username -Properties ServicePrincipalName \| select ServicePrincipalName|See if \$Username has a SPN|

## PowerView
|Command|Description|
|---|---|
|Get-NetUser|Get a list of users and all default properties in the current domain|
|Get-NetUser -Username $username|Get list of all default properties for the specified username|
|Get-NetUser \| Select cn | Get list of Common Names|
|Get-UserProperty -Properties $PropertyName|Look at property details for all users in current domain|
|Find-UserField -SearchField Description -SearchTerm "built" | Search User Descriptions in current domain|
|Find-LocalAdminAccess -Verbose|Find all machines on the current domain where the current user has local admin access|
|Invoke-EnumerateLocalAdmin -Verbose|Find all machines on current domain where current user has local admin access (needs admin privs on non-dc machine)|
|Invoke-UserHunter|Find computers where a domain admin has sessions|
|Invoke-UserHunter -GroupName "$GroupName"|Find computers where a member of the specified group has sessions|
|Get-DomainUser -Identity \$Username | Select serviceprincipalname | See if the user has a SPN|

# Domain Group Enumeration

## Active Directory PowerShell Module

|Command|Description|
|---|---|
|Get-ADGroup -Filter \* \| Select Name| Extract all group names for the current domain|
|Get-ADGroup -Filter \* -Properties \* | Extract all group information for the current domain|
|Get-ADGroup -Filter 'Name -like "\admin\"' \| Select Name | Extract all group information with group names like "admin"|

## PowerView
|Command|Description|
|---|---|
|Get-NetGroup|Extract group information for the current domain|
|Get-NetGroup -Domain|Extract group information for a specified domain|
|Get-NetGroup -FullData|Extract all group information for the current domain|
|Get-NetGroup -GroupName $groupname |Extract group information for a specified group|

# Domain Computer Enumeration

## Active Directory PowerShell Module
|Command|Description|
|---|---|
|Get-ADComputer -Filter \* \| Select Name||
|Get-ADComputer -Filter 'OperatingSystem -like "\Server 2016\"' -Properties OperatingSystem \| select Name,OperatingSystem||
|Get-ADComputer -Filter \* -Properties DNSHostName \| %{Test-Connection -Count 1 -ComputerName $\_.DNSHostName} | |
|Get-ADComputer -Filter \* -Properties \* | Retrieve all properties for all computers in the current domain|

## PowerView
|Command|Description|
|---|---|
|Get-NetComputer| Retrieve all computers in the current domain|
|Get-NetComputer -Ping| Retrieve all computers in the current domain and test access to them|
|Get-NetComputer -FullData| Retrieve all properties for all computers in the current domain|
|Get-NetFileServer|Looks for targets that have the file server role installed on them|

# Group Policy Object Enumeration

## Active Directory PowerShell Module
|Command|Description|
|---|---|
|Get-GPO -All|Get All Group Policy Objects|
|Get-GPO -Guid $GUID | Get GPOs applied on a GUID|
|Get-GPResultantSetOfPolicy -ReportType Html -Path $Path/To/Export/To||
|Get-ADOrganizationalUnit -Filter * -Properties * | Get OUs in a domain|

## PowerView
|Command|Description|
|---|---|
|Get-NetGPO|Get all group policy objects in the domain|
|Get-NetGPO \| Select displayname | Gathers all displaynames of all group policy objects|
|Get-NetGPO -ComputerName $ComputerFQDN | Gathers all grou policies applied to a specific ComputerName |
|Find-GPOComputerAdmin -ComputerName $ComputerFQDN | Get a users which are in a local group of a machine using GPO|
|Find-GPOLocation -UserName $username|Get machines there the given user is a member of a specific group|
|Get-NetOU -FullData | Get OUs in a domain|
|Get-NetGPO -GPOname "{$GUID}"|Get GPO applied to an OU|

## Misc.
|Command|Description|
|---|---|
|gpresult /R|Shows applied group policy objects for the local machine|

# Group Policy Object Access Control Lists

## Active Directory PowerShell Module
|Command|Description|
|---|---|
|(Get-ACL 'AD:\CN=Administrator,CN=Users,DC=\$domain,DC=\$domain').Access|Enumerate ACLs without resolving GUIDs|

## PowerView
|Command|Description|
|---|---|
|Get-ObjectAcl -ADSprefix 'CN=Administrator,CN=Users' -Verbose|Get ACLs associated with the specified prefix|
|Get-ObjectAcl -SamAccountName $username -ResolveGUIDs | Get ACLs associated with the object|
|Get-ObjectAcl -ADSPath "LDAP://CN=Domain Admins, CN=Users, DC=\$domain,DC=\$domain" -ResolveGUIDs -Verbose | Get ACLs associated with the specified LDAP path|
|Invoke-ACLScanner -ResolveGUIDs | Search for interesting ACEs (any permission aside from Read)|
|Get-PathAcl -Path "\\\\\$machinename\\\$share"|Get ACLs associated with specified path|

# Addtl. Notes
- you can use logoncount, pwdlastset, and badpwdcount properties to extrapolate data to determine if your target account(s) are honey users or not
- Find-LocalAdminAccess is very noisy, it queries the DC of the current or provided domain for a list of computers and uses invoke-checklocaladminaccess on each of those machines (leaves 4624 and 4634 (login/logoff) event on each machine)
- Invoke-EnumerateLocalAdmin is very noisy, it queries the DC of the current or provided domain for a list of computers and uses Get-NetLocalGroup on each machine
- Invoke-UserHunter is very noisy, queries the domain controller of the current or provided domain for members of a given group and uses get-netgroupmember and lists sessions/logged on users on each machine. (leaves 4624 and 4634 (login/logoff) event on each machine)
- The event 4656 indicates that specific access was requested for an object. Object could be a file, system, kernel, registry, or file system object on either removable storage or a device.
- The event that shows if the operation was performed, check event ID 4663


