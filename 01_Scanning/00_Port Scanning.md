Initial scan to know open ports.
```
nmap -p- --open -T5 -v  ipHost -n
```

Enumeration of the services and versions.
```
nmap -p22,80 -sC -sV ipHost
```

In case of detect an HTTP service running we run a script that enums directories and files of the server.
```
nmap --script=http-enum.nse -p80,443,8080 ipHost 
```

For other services we use scripts that discover vulnerabilities and are safe to use (They don't break the service).
```
nmap -p21,445 --script="vuln and safe" ipHost 
```
For more script usage check this link https://nmap.org/book/nse-usage.html