---
layout: post
title: Building CouchDB on OpenIndiana
date: 2014-07-21 20:27:03
tags:
- solaris
- openindiana
- couchdb
comments: true
---

[CouchDB](http://couchdb.apache.org) rocks! I really like their data model, especially with respect to data integrity (i.e. never overwriting live data). That being said, Solaris plays second fiddle to Linux for many open source applications, and CouchDB is no different. Getting it working was a multiple day challenge.

## Installing Dependencies

### Erlang/OTP

See my earlier blog post on building Erlang on OpenIndiana. It's not much of a challenge, but does involve a few steps.

### ICU

This one is easy, `pkg search icu` and install the matching package.

```
$ pfexec pkg install developer/icu
```

One small problem with this package is that it seems to be incompatible with the GCC 4.4.x package that is recommended for compiling "modern" software on OpenIndiana. As such, I ran into a linker error when building CouchDB and had to hack `/usr/bin/icu-config`: I removed the `-lCrun` argument from the `LDFLAGS` line. If you have already tried to build CouchDB by this time, run `make distclean` and `configure` again to regenerate the numerous makefiles.

### SpiderMonkey

Mozilla's [SpiderMonkey](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey) provides the JavaScript engine for CouchDB to process map/reduce functions and execute view queries. Installing from source on OpenIndiana is non-trivial, so use the [SFE](http://wiki.openindiana.org/oi/Spec+Files+Extra+Repository) package instead.

```
$ pfexec pkg set-publisher -p http://pkg.openindiana.org/sfe
$ pfexec pkg install runtime/javascript/spidermonkey
```

### Netscape Portable Runtime headers

This one was tricky to discover. It is not mentioned anywhere obvious, and it took some detective work to figure it out. The clue was to look in the `config.log` after CouchDB `configure` failed to find `JS_NewContext` in the `mozjs185` library. From the log, it becomes clear that the problem has nothing at all to do with SpiderMonkey and instead it's missing a file named `nspr.pc`. After some searching and package querying, I came to find that the OpenIndiana package `library/nspr/header-nspr` fits the bill.

```
$ pfexec pkg install library/nspr/header-nspr
```

## Compiling and Installing CouchDB

Once all of the dependencies have been installed, and the `icu-config` has been hacked, compiling and installing CouchDB is _easier_. I found that I had to add a couple of directories to the `LDFLAGS` in order for `configure` to find the NSPR library. The `crle` command at the end is so that `couchjs` can find `libplds4.so`, otherwise the database verification fails.

```
$ LDFLAGS='-L/usr/lib -L/usr/lib/mps' ./configure
$ make
$ pfexec make install
$ pfexec crle -u -l /usr/lib/mps
```

## Fire it up!

Before starting CouchDB, be sure to add the group and user, and possibly make some configuration changes.

```
$ pfexec groupadd couchdb
$ pfexec useradd -c 'CouchDB User' -d /usr/local/var/lib/couchdb -g couchdb -s /usr/bin/false couchdb
$ pfexec chown -R couchdb:couchdb /usr/local/var/lib/couchdb
$ pfexec chown -R couchdb:couchdb /usr/local/var/log/couchdb
$ pfexec chown -R couchdb:couchdb /usr/local/var/run/couchdb
```

You may want to configure CouchDB to bind to all addresses on the server, in which case edit `/usr/local/etc/couchdb/local.ini` and change `bind_address` to `0.0.0.0`.

I highly recommend using the Service Management Framework (SMF) for managing the CouchDB process. To do so, copy the following SMF service manifest to `/var/svc/manifest/application/database` (you may need to create this directory first):

```
<?xml version="1.0"?>
<!DOCTYPE service_bundle SYSTEM "/usr/share/lib/xml/dtd/service_bundle.dtd.1">
<service_bundle type="manifest" name="couchdb">
  <service name="application/database/couch" type="service" version="1">
    <create_default_instance enabled="false"/>
    <single_instance/>
    <dependency name="network" grouping="require_all" restart_on="none" type="service">
      <service_fmri value="svc:/milestone/network:default"/>
    </dependency>
    <dependency name="filesystem-local" grouping="require_all" restart_on="none" type="service">
      <service_fmri value="svc:/system/filesystem/local:default"/>
    </dependency>
    <exec_method name="start" type="method" exec="/usr/local/bin/couchdb -b" timeout_seconds="300">
      <method_context>
        <method_credential user="couchdb" group="couchdb"/>
        <method_environment>
          <envvar name="HOME" value="/usr/local/var/lib/couchdb" />
        </method_environment>
      </method_context>
    </exec_method>
    <exec_method name="stop" type="method" exec="/usr/local/bin/couchdb -d" timeout_seconds="300">
      <method_context>
        <method_credential user="couchdb" group="couchdb"/>
        <method_environment>
          <envvar name="HOME" value="/usr/local/var/lib/couchdb" />
        </method_environment>
      </method_context>
    </exec_method>
    <stability value="Evolving"/>
    <template>
      <common_name>
        <loctext xml:lang="C">Apache CouchDB</loctext>
      </common_name>
      <documentation>
        <manpage title="couchdb" section="1M"/>
        <doc_link name="couchdb.apache.org" uri="http://docs.couchdb.org/"/>
      </documentation>
    </template>
  </service>
</service_bundle>
```

And now install the service and start it up.

```
$ pfexec chown root:sys /var/svc/manifest/application/database/couchdb.xml
$ pfexec svccfg -v import /var/svc/manifest/application/database/couchdb.xml
$ pfexec svcadm enable couch
```

## Verification

Visit the Futon web administrative interface by visiting http://yourhost:5984/\_utils with your browser, then click the __Verify Installation__ link on the sidebar. If that does not pass, it could be that the `couchjs` binary is unable to find a library. See the note about `crle` above.

## What about the build-CouchDB project?

Another way to build CouchDB is to make use of the [build-CouchDB](https://github.com/jhs/build-couchdb) build system. I have to admit, the idea is very appealing, but by the time I discovered it, I had already resolved most of the issues. That, and I didn't want to install an entirely new set of (duplicate) packages ([OpenCSW](http://www.opencsw.org)) when my only remaining problem was the `-lCrun` linker error.
