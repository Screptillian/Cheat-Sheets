# SMBClient

## Null Authentication - List Shares

```smbclient -L -N \\\\$IP\\```

```smbclient -L -N ////$IP//```

## Null Authentication - Connecting to Specified Share

```smbclient -N ////$IP//$ShareName```

```smbclient -N \\\\$IP\\$ShareName```

## Username Authentication with blank password - List Shares

```smbclient -L -U $username -P '' \\\\$IP\\```

```smbclient -L -U $username -P '' ////$IP//```

## Username Authentication - Connecting to Specified Share

```smbclient -U $username -P '' \\\\$IP\\$ShareName```

```smbclient -U $username -P '' ////$IP//$ShareName```

## Username + Password Authentication - List Shares

```smbclient -L -U $username -P 'PasswordHere' \\\\$IP\\```

```smbclient -L -U $username -P 'PasswordHere' ////$IP//```

## Username + Password Authentication - Connecting to Specified Share

```smbclient -L -U $username -P 'PasswordHere' \\\\$IP\\$ShareName```

```smbclient -L -U $username -P 'PasswordHere' ////$IP//$ShareName```

## Username + NT Hash - List Shares

```smbclient -L --pw-nt-hash -U $username -P 'NTHash' \\\\$IP\\```

```smbclient -L --pw-nt-hash -U $username -P 'NTHash' ////$IP//```

## Username + NT Hash - Connecting to Specified Share

```smbclient --pw-nt-hash -U $username -P 'NTHash' \\\\$IP\\```

```smbclient --pw-nt-hash -U $username -P 'NTHash' ////$IP//```
