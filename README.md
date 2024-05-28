# Description
From https://github.com/scrapy/scrapy/issues/4368

This is done by LD_PRELOAD hooking openssl.

## Install required library
```
sudo apt install libssl-dev
sudo apt install git make gcc
```

## Build
```
cd wireshark-notes/src
make
```

This will build libsslkeylog.so

## How to use
```
export LD_PRELOAD=/path/to/libsslkeylog.so
export SSLKEYLOGFILE=/path/to/SSLKEYLOGFILE
./sslkeylog.sh curl https://example.com -sI
```
You should observe /path/to/SSLKEYLOGFILE being populated. Load the log file in wireshark to decrypt TLS traffic
