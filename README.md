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

### Require libs(deb) ###

* libssl1.0.0 libncurses5 libreadline6 libtinfo5 libyaml-0-2 zlib1g

### recommends deb ###

* supervisor

BOOTUP
------

via supervisord:

    # apt-get install -y supervisor
    # cp /opt/pd/emitter/share/pd-emitter_supervisord.conf /etc/supervisor/cond.d/pd-emitter.conf
    # supervisorctl reload

via CLI:

    # cd /opt/pd/emitter
    # [CONF="/path/of/pd-emitter_bootconf.json"] /opt/pd/bin/rake start

NOTE: _CONF_ enviromnent var is optional file. PD Emitter will work even if this file not present.

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

