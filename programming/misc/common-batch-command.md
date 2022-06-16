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

### EXECUTE COMMAND WITH INPUT

Sometimes we need to request new AWS session token with MFA enabled in order to update the session token credentials when it is expired.

For example:
`aws sts get-session-token --serial-number arn:aws:iam::123456789012:mfa/yourawsuser --token-code %otp% --profile yourprofile`

If you do this manually and very frequent, you may consider to create a simple Batch Script file to prompt you to enter the MFA OTP.

```
@ECHO off
cls

GOTO start

REM <The BEGIN Label>
:start
ECHO.
ECHO --------------------------------------------------------
ECHO *** Request AWS Session Token ***
ECHO --------------------------------------------------------

REM - prompt input for one time passcode
SET /p otp="Enter OTP: "

ECHO OTP is %otp%

REM - prepare command
SET command="aws sts get-session-token --serial-number arn:aws:iam::{REPLACE_YOUR_AWS_ID_HERE}:mfa/{YOUR_AWS_USER} --token-code %otp% --profile {YOUR_PROFILE}"

REM - execute the command in a seperate shell console
start cmd /k %command%

REM - prompt do you wish to request a new one or exit.
GOTO retry_or_exit
REM </The BEGIN Label>

REM <RETRY or EXIT Label>
:retry_or_exit
@ECHO off
cls
ECHO.
ECHO --------------------------------------------------------
ECHO  Y - Retry
ECHO  n - Exit
ECHO --------------------------------------------------------

REM - prompt input for choice
SET /p choice="Do you want to retry or exit? [Y/n]"

REM - handle choice
IF '%choice%'=='Y' GOTO start
IF '%choice%'=='y' GOTO start
IF '%choice%'=='N' GOTO end
IF '%choice%'=='n' GOTO end
IF '%choice%'=='Y' GOTO start

REM - prompt for input again if invalid choice is provided   
ECHO "%choice%" is not valid, try again
GOTO retry_or_exit

PAUSE
REM </RETRY or EXIT Label>

REM <THE END Label>
:end 
REM </THE END Label>
```

## Reference

- [Windows commands](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands)
