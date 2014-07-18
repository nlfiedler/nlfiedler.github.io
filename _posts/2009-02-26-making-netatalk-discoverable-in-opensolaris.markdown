---
author: nlfiedler
comments: true
date: 2009-02-26 16:00:15+00:00
layout: post
slug: making-netatalk-discoverable-in-opensolaris
title: Making netatalk discoverable in OpenSolaris
wordpress_id: 83
tags:
- mac
- netatalk
- opensolaris
---

Previously I described how I had set up [netatalk](http://netatalk.sourceforge.net/) on my [OpenSolaris](http://www.opensolaris.com/) storage server. That step went a long way to making it easy to use Time Machine to backup my Mac to the server. But having read [kremalicious](http://www.kremalicious.com/2008/06/ubuntu-as-mac-file-server-and-time-machine-volume/), I wanted to find a way to make the netatalk daemon [discoverable](http://opensolaris.org/os/project/nwam/service-discovery/) by the Macs on my network. The same technique that Matthias used on Linux was not going to work on OpenSolaris. For starters, OpenSolaris doesn't use [avahi](http://avahi.org/), it has it's own solution in the form of the dns/multicast service. In place of creating a static configuration file, you use the `dns-sd` command line client on OpenSolaris. While this tool is not meant to be used to register long running services, it's the only [feasible solution](http://www.opensolaris.org/jive/thread.jspa?threadID=93112) at the moment. But just running that command in the background and leaving its fate to the gods is not good enough for me. It should be monitored using the Service Management Framework. This turns out to be surprisingly easy once you've read the [SMF guide](http://www.sun.com/bigadmin/content/selfheal/sdev_intro.jsp) on [BigAdmin](http://www.sun.com/bigadmin/).

Start by installing netatalk, if you haven't already, as described in an earlier [post](http://cafenate.wordpress.com/2009/02/08/building-netatalk-on-opensolaris-200811/). Next, create the SMF manifest file that will register the AFP daemon as a discoverable service. Name the file `dnssd-afp.xml` and place it in the `/var/svc/manifest/site` directory.

    
    <?xml version="1.0"?>
    <!DOCTYPE service_bundle SYSTEM "/usr/share/lib/xml/dtd/service_bundle.dtd.1">
    <service_bundle type="manifest" name="dnssd-afp">
      <service
         name="site/dnssd-afp"
         type="service"
         version="1">
    
        <single_instance/>
    
        <dependency
           name="filesystem-local"
           grouping="require_all"
           restart_on="none"
           type="service">
          <service_fmri value="svc:/system/filesystem/local:default"/>
        </dependency>
    
       <dependency
           name="dns-multicast"
           grouping="require_all"
           restart_on="none"
           type="service">
          <service_fmri value="svc:/network/dns/multicast:default"/>
        </dependency>
    
        <exec_method
           type="method"
           name="start"
           exec="/lib/svc/method/dnssd-afp"
           timeout_seconds="60">
          <method_context>
            <method_credential user="root" group="root"/>
          </method_context>
        </exec_method>
    
        <exec_method
           type="method"
           name="stop"
           exec=":kill"
           timeout_seconds="60">
        </exec_method>
    
        <instance name="default" enabled="false" />
    
        <stability value="Unstable" />
    
        <template>
          <common_name>
            <loctext xml:lang="C">
              dns-sd registration of afp daemon
            </loctext>
          </common_name>
          <documentation>
            <manpage title="dns-sd" section="1M" manpath="/usr/man"/>
          </documentation>
        </template>
      </service>
    </service_bundle>


Change the ownership of the manifest to `root:sys` and make it read-only by all but the root user. For this service, we'll need to write a shell script that spawns `dns-sd` as a background process, otherwise SMF will timeout waiting for the service to start (the SMF documentation is better at explaining this than I am). _Note that `chihiro` in the script is the hostname that will be advertised; you may want to change this to suit your needs._

    
    #!/sbin/sh
    #
    # Registers the AFP daemon with dns-sd.
    #
    /usr/bin/dns-sd -R chihiro _afpovertcp._tcp local 548 &
    /usr/bin/dns-sd -R chihiro _device-info._tcp. local 548 model=Xserve &
    # Sleep to ensure service has enough time to start up,
    # otherwise SMF will timeout waiting for it to be ready.
    sleep 5


Place the shell script in the `/lib/svc/method` directory, give it the name `dnssd-afp`, change the ownership to `root:bin`, and make the file executable by all and writable only by root. Now we're ready to import the service configuration and start the service. Import it using the command `pfexec svccfg -v import /var/svc/manifest/site/dnssd-afp.xml`, then start the service using `pfexec svcadm enable dnssd-afp`, and finally check that the service is running with `svcs dnssd-afp`. At this point, if the `dns-sd` process were to unexpectedly die, SMF will immediately restart it. That's one of the many advantages of SMF over initd, which does not monitor the processes that it initiates. With the AFP service now registered, any Mac on your network should see your storage server as a Mac-compatible file share, which will appear automatically in the Finder sidebar. If you've added a shared volume to your Login Items previously, you can remove it, you won't need it any more.
