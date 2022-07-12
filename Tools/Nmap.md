# Ping sweep

```nmap -sn -oG $pathtofile $IPRange```

# Further system enumeration

```nmap -sV -sC -oG $pathtofile $IP1 $IP2 $IP3 $IP4```

```sudo nmap -sU -sV -oG $pathtofile $IP1```

## Common UDP ports:

|Port|Service
|---|---|
|123|NTP|
|53 |DNS|
|161|SNMP|
|69|TFTP|

### Note: Typically only interested in 53 and 161 but NTP can play a role in evasion techniques.

# Common scripts

ftp-anon

tftp-enum

smb-enum-domains

smb-enum-groups

smb-enum-shares

smb-enum-users

smb-vuln-ms17-010

smb-os-discovery

smb2-capabilities

smb2-security-mode

http-cookie-flags

http-wordpress-enum

http-shellshock

http-server-header

http-wordpress-enum

dns-check-zone

dns-srv-enum

dns-zone-transfer

snmp-info

snmp-processes
