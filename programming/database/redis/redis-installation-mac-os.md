# Redis Installation on Mac OS

Refer to Redis docs, https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/

First, make sure you have Homebrew installed. From the terminal, run:

```
brew --version
```

If this command fails, you will need to install the brew from https://brew.sh/

From the terminal, run:
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

From the terminal, run:

```
nano .zshrc
```

This will edit the .zshrc file

Add the path in the nano editor screen, and then press Control + X to save

```
export PATH=/opt/homebrew/bin:$PATH
```

From the terminal, run:

```
source ~/.zshrc
```

The brew command should be available now, and we can start with the redis installation

```
brew --version
```

From the terminal, run:

```
brew install redis
```

This will install Redis on your system.

### Redis Command

- Test redis installation

```
redis-server

# If successful, you'll see the startup logs for Redis, and Redis will be running in the foreground.
# To stop Redis, enter Ctrl-C.

47132:M 04 Nov 2022 12:14:54.316 * monotonic clock: POSIX clock_gettime
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 7.0.5 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                  
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 47132
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           https://redis.io       
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                  
```

- Start redis service

```
brew services start redis
```

-Check redis info

```
brew services info redis

# If the service is running, you'll see output like the following:
redis (homebrew.mxcl.redis)
Running: ✔
Loaded: ✔
User: xxx
PID: 67975
```

- Stop redis service

```
brew services stop redis
```

### Redis CLI

- Connect to Redis

```
redis-cli
```

- SET key value

```
SET mykey1 "hello"
```

- GET key value

```
GET mykey1
```

- List all keys

```
KEYS *
```

- Delete key

```
DEL mykey1
```

Example usage:

```
some@body-dq05n ~ % redis-cli
127.0.0.1:6379> SET mykey1 "hello"
OK
127.0.0.1:6379> SET mykey2 "hello 2"
OK
127.0.0.1:6379> SET mykey3 "hello 3"
OK
127.0.0.1:6379> GET mykey1
"hello"
127.0.0.1:6379> GET mykey2
"hello 2"
127.0.0.1:6379> GET mykey3
"hello 3"
127.0.0.1:6379> KEYS *
1) "mykey3"
2) "mykey1"
3) "mykey2"
127.0.0.1:6379> DEL mykey1
(integer) 1
127.0.0.1:6379> KEYS *
1) "mykey3"
2) "mykey2"
127.0.0.1:6379> DEL mykey2 mykey3
(integer) 2
127.0.0.1:6379> KEYS *
(empty array)
127.0.0.1:6379> 
```
