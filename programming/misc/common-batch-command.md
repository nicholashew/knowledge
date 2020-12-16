# Common Batch Command

## Useful Commands

### List filenames in directory
```
dir /b /a-d
```

### List directory tree
```
# To display the names of all the subdirectories on the disk in your current drive, type:
tree \

# To display, one screen at a time, the files in all the directories on drive C, type:
tree c:\ /f | more

# To print a list of all the directories on drive C, type:
tree c:\ /f  prn
```

### Command History
```
doskey /history
```

### IP Configuration
```
ipconfig 
```

### IP Configuration utility to remove the DNS cache
```
ipconfig /flushdns
```

### NETSTAT: Network Statistics
```
netstat
```

### PING: Send Test Packets
```
ping google.com
```

### TRACERT: Trace Route
```
tracert google.com
```

### TELNET
```
telnet [domain name or ip] [port]

#example: telnet 192.168.0.10 25
```

### NSLOOKUP
```
nslookup [domain name or ip]

#example: nslookup google.com
```

### SYSTEMINFO: System Information
```
systeminfo
```

## Reference

- [Windows commands](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands)
