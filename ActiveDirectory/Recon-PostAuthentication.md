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

# Addtl. Notes
- you can use logoncount, pwdlastset, and badpwdcount properties to extrapolate data to determine if your target account(s) are honey users or not
