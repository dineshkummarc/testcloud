# Testcloud

Testcloud allows for the distributed execution of a collection of commands, and aggregates the
results on the calling client.

## Install

Before you install testcloud, install zeromq and the php zeromq binding:

```bash
$ aptitude install libzmq-dev
$ pear channel-discover pear.zero.mq
$ pecl install zero.mq/zmq-beta
$ echo "extension=zmq.so" >> /etc/php.ini
```

The recommended way to install testcloud is [through composer](http://getcomposer.org).

```bash
$ php composer.phar create-project 99designs/testcloud /usr/local/testcloud
```

## Example

Here is an example of a distributing the calculation of PI to several decimal places:

```bash
$ testbroker &
$ testworker &
$ seq 5 | awk '{print "bin/pi " $1}' | ./testclient
```

The results are:

```bash
Connecting to tcp://localhost:2224
Releasing the hounds: 5 commands to execute
OK! bin/pi 1 ⌚8.14ms
2.8

OK! bin/pi 2 ⌚7.58ms
3.12

OK! bin/pi 3 ⌚7.74ms
3.140

OK! bin/pi 4 ⌚7.73ms
3.1412

OK! bin/pi 5 ⌚9.72ms
3.14156

All commands successfully executed ☃
Ran 5 commands in ⌚0.05s, result hash is 24aa911ac6e5f453c2ca1fa3cd8fe3ad2d6b1f43
```

## License

MIT, see LICENSE.
