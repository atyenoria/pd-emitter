PD Emitter
==========

[PD Emitter Introdution(ja)](https://pd.plathome.com/emitter)

It is a feature of the IoT network "IoT devices are many, the line is unstable, and requires two-way communication" is also resolved to addition to these issues of "difficult system changes after the start of operation", IoT safety and I will surely realize the device and application data exchange.

INSTALL
-------

Deployed to `/opt/pd/emitter`

chdir & bundle install

### Requirements ###

* Ruby 1.9 or higher

### Require libs and commands(deb) ###

* libssl1.0.0 libncurses5 libreadline6 libtinfo5 libyaml-0-2 zlib1g
* build-essential git

### recommends deb ###

* supervisor

### Example and BOOTUP ###

In case of under debian 7.0 (wheezy) and using deb's ruby.

```
$ sudo apt-get install -y ruby1.9.1-dev libssl1.0.0 libncurses5 libreadline6 libtinfo5 libyaml-0-2 zlib1g build-essential git
$ sudo gem install bundle --no-rdoc --no-ri
$ git clone https://github.com/plathome/pd-emitter.git /pd-emitter/deploy/dir
$ cd /pd-emitter/deploy/dir
$ bundle install --without development --path vendor/bundle
$ bin/rake start
## Stop is Ctrl+C
```

```
## In other terminal ...
$ sudo apt-get install -y socat
$ echo -n "MessageText" | socat stdin abstract-connect:/pd/stdout.sock
## See PD Emitter startup terminal. Maybe display below...
2015-05-10 10:44:52 +0900 pd.stdout.debug: {"data":"MessageText"}
```

BOOTUP via supervisord
----------------------

```
$ sudo apt-get install -y supervisor
$ sudo cp /pd-emitter/deploy/dir/share/pd-emitter_supervisord.conf /etc/supervisor/cond.d/pd-emitter.conf
## Adjust pd-emitter.conf (*)
$ sudo supervisorctl reload
```

NOTE: pd-emitter.conf: Adjust `directory` and `command` directive in accordance with the your environment.

CONFIG
------

see `conf/`. And [Fluentd configuration](http://docs.fluentd.org/articles/config-file).

LOG
---

via supervisord:

    # supervisorctl tail -f main:pd-emitter

via CLI:

// display on CLI //

DEBUG
-----

    # apt-get install -y socat
    # echo "hoge" | socat stdin abstract-connect:/pd-emitter/v1/localname/dev_le001.sock

==> watch up log

e.g.)

```
2015-05-12 11:48:53 +0900 [info]: adding source type="unix_unimsg"
2015-05-12 11:48:53 +0900 [info]: listening dRuby uri="druby://127.0.0.1:24230" object="Engine"
2015-05-12 11:54:19 +0900 pd.skel.ab.1234567.a5dec5aa: {"data":"hoge\n"}
```

TIPS
----

### Listup of listening Unix Domain Socket ###

    # /opt/pd/emitter/bin/fluentctl | grep "path /pd-emitter"

e.g.)

```
# /opt/pd/emitter/bin/fluentctl | grep "path /pd-emitter"
    path /pd-emitter/v1/localname/devle_001.sock
    path /pd-emitter/v1/localname/devle_002.sock
```

### execute path of using out\_exec ###

```
<match **>
  type exec
  ...
  path bin/exec_script.rb
</match>
```

[EoT]

