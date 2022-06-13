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

