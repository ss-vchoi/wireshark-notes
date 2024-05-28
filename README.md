#How to use
From https://github.com/scrapy/scrapy/issues/4368
This is done by LD_PRELOAD hooking openssl.

So we need to have our SSL library's header files:

sudo apt install libssl-dev
(use libssl1.0-dev, if your ssl programs are still built against libssl.so.1.0*, check this with e.g.
ldd `python3 -c "import _ssl; print(_ssl.__file__)"` | grep ssl)

... and necessary build tools:

sudo apt install git make gcc
Fetch and build the awesomesauce

git clone --depth=1 https://git.lekensteyn.nl/peter/wireshark-notes
cd wireshark-notes/src
make
This will build libsslkeylog.so
(There is also a Python script in this dir, which might be of interest. I couldn't find a license, though.)

Optionally, copy it to a convenient location elsewhere in $PATH:

sudo install libsslkeylog.so /usr/local/lib/
Now, on Linux, just do this:

export LD_PRELOAD=/path/to/libsslkeylog.so
export SSLKEYLOGFILE=/path/to/SSLKEYLOGFILE
...or use the provided ./sslkeylog.sh script in the src dir, which also handles MacOS and has some convenience functions for gdb debugging.

A word of warning: Your browser (and curl) will likely obey this variable, too, when it's set in the browser environment (on it's own, without the LD_PRELOAD hook!). Be aware of that, if you consider to export SSLKEYLOGFILE in your ~/.profile!
This file will contain all the secrets to decrypt your comms.
Make sure it's written to a safe place (not your cloud-synced home dir ;), readable by only you and trash it when done.

Open Wireshark, visit (Edit>)Preferences, and under the Protocols drop-down find TLS (or, if missing, SSL) and enter your SSLKEYLOGFILE location in "(Pre)-Master-Secret log filename".

Let's, test it: ./sslkeylog.sh curl https://example.com -sI
and you should see output written to the SSLKEYLOGFILE at this point.
In wireshark, it should now be possible to inspect the captured SSL stream and have the connection displayed in plain text.

And finally, to inspect scrapy's SSL connections, run the crawler as

./sslkeylog.sh scrapy shell|fetch https://example.com
And no more guessing if these requests look as they were meant to, on the wire, or if any headers or parameters don't match your expectations in the code.
Heck, yarrrr!

