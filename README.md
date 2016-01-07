What is uNote
=============

`uNote` is very simple and flexible standalone GUI notification utility.

It is not affiliated with any of window managers.
It provides an network interface to accept messages.
This makes it possible to receive messages from different scripts,
even from other hosts. And it makes very easy to raise
notifications from routines like `cron` and `at` without
care about `$DISPLAY` and `Xauth`.

`uNote` writen on `Tcl/Tk` and requires no additional libraries.

`uNote` proveides multiwindows and can display two types of messages:
*text* and *progress bar*.

How to run
==========

`uNote` is one file (script). Just run it.

To run it automaticaly add it to `~/.xinitrc`.

How to send message
===================

Simples

    echo '|Hello, world!' | nc localhost 7779

Big red label at specified place, displayd 10 seconds

    echo 'geometry=+10+10,fg=f00,bg=ff0,size=24,padx=120,duration=10|Hello, world!' | nc localhost 7779

Green 80% bar

    echo 'type=bar,geometry=+10+10,percent=80,width=200,height=10,duration=10,fg=0f0,bg=000,bd=090|' | nc localhost 7779

Message structure and params
============================

Message structure
-----------------

Message consists of two parts, separated by pipe ("|"). First
(left) part conteins options, second (right) part is a message itself.

So, simplest text message is "`|OK`":

    echo '|OK' | nc localhost 7779

Params overview
---------------

Params are:

* `type`: `text` or `bar`
* `geometry`: position in X-format like `+0+0`, `-10-0` etc.
* `duration`: seconds
* `service`: name of queues (see below)
* `fg`: foreground color
* `bg`: background color
* `bd`: border color
* `padx`: horisontal padding
* `pady`: vertical padding
* `size`: font size
* `family`: font family like `Droid Serif` (command `fc-list : family` show all font families on your X)
* `bold`: bold flag
* `percent`: percents (for progress bar)

Windows and queues (`service`)
------------------------------

For every unquie `geometry` value raise its own window.

Every window has its own message queue and displays
only one message form top of queue.

New message appended to queue, and replace exitance
with same value of `service`. If `service` not cpecified, it
will be generated by random number generator.

Code snippets
=============

Call from Perl
--------------

    #!/usr/bin/perl

    use strict;
    use warnings;
    use Socket;

    my $message = 'DONE!';

    my $host = 'localhost';
    my $port = 7779;
    socket(SOCK, PF_INET, SOCK_STREAM, (getprotobyname('tcp'))[2]);
    connect(SOCK, sockaddr_in($port, inet_aton($host)));
    send(
        SOCK,
        'geometry=+0+10,bg=99f,fg=000,size=24,padx=12,duration=10,family=Ubuntu Condensed|' .
        $message,
        0
    );

Call from Python (v2 and v3)
----------------------------

    #!/usr/bin/python

    import socket

    HOST = 'localhost'
    PORT = 7779

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((HOST, PORT))
    s.sendall(b'|Hello, world')
    s.close()

Create ssh tunnel
-----------------

You can add to your `~/.ssh/config`:

    RemoteForward 7779 localhost:7779
