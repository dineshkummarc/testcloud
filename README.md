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
ln -s /usr/local/testcloud/bin/* /usr/local/bin
```

## Example

Here is an example of a distributing the calculation of PI to several decimal places:

```bash
$ testbroker &
$ testworker &
$ seq 5 | awk '{print "bin/pi " $1}' | ./testcloud
```

The results are:

```bash
Connecting to tcp://localhost:2224
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

## Protocol

Testcloud is made up of workers, brokers and clients. Workers take jobs and synchronously execute them,
returning results in JSON. Brokers bind on two points, one for downstream workers (or other brokers) and one for upstream workers.

A conversation below shows a broker and worker starting up:

```
worker -> broker: RDY
client -> broker: {"cmd":"pi 1"}
worker -> broker -> client: QUE 9f84903
worker -> broker -> client: WRK 9f84903
worker -> broker -> client: WRK 9f84903
worker -> broker -> client: RES {"hash":"9f84903","exit":0,"stdout":"3.12\n","time":13.95}
worker -> broker: RDY
```

A worker sends READY when it needs jobs, WORKING whilst it's doing them and RESULT when it's done. When a worker
isn't processing work, it should periodically send new READY commands.

## License

MIT, see LICENSE.
