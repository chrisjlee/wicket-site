---
layout: default
title: Download Wicket releases
---

Apache Wicket {{site.wicket.version}} is the current stable release.

## Download

New projects should use [Wicket {{site.wicket.version}}](http://www.apache.org/dyn/closer.cgi/wicket/{{site.wicket.version}}) as their base.

* **Latest stable release**: [{{site.wicket.version}}](http://www.apache.org/dyn/closer.cgi/wicket/{{site.wicket.version}})
* **Last 1.4.x release**: [1.4.19](http://archive.apache.org/dist/wicket/1.4.19)
* **Archived releases**: <http://archive.apache.org/dist/wicket>

We recommend you [start](quickstart.html) with our latest 1.5 release. If you
are migrating an existing application from Wicket 1.4 to 1.5, you may find
the [migration guide](http://cwiki.apache.org/WICKET/migration-to-wicket-15.html)
invaluable.

Apache Wicket 1.5.x require JDK 1.5 or newer

You cannot use Wicket without adding an SLF4J logging implementation to
your classpath. Most people use [log4j](http://logging.apache.org/log4j).
If you do, just include **slf4j-log4j12.jar** on your classpath to get Wicket
to use log4j too. If you want to use commons-logging or JDK14 logging or
something else, please see the [SLF4J site](http://www.slf4j.org/faq.html)
for more information.

## Maven 2

Add the following snippet to your Maven project descriptor (`pom.xml`):

{% highlight xml %}
<dependency>
    <groupId>org.apache.wicket</groupId>
    <artifactId>wicket-core</artifactId>
    <version>{{site.wicket.version}}</version>
</dependency>
{% endhighlight %}

For the SLF4J log4j binding:

{% highlight xml %}
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.5.6</version>
</dependency>
{% endhighlight %}

## SNAPSHOTs and latest bleeding-edge code ##

If you wish to build the latest code from scratch, trunk and branches live in
the following places in Subversion:

* **6.0.x**: <http://svn.apache.org/repos/asf/wicket/trunk>
* **1.5.x**: <http://svn.apache.org/repos/asf/wicket/branches/wicket-1.5.x>
* **1.4.x**: <http://svn.apache.org/repos/asf/wicket/branches/wicket-1.4.x>
* **1.3.x**: <http://svn.apache.org/repos/asf/wicket/branches/wicket-1.3.x>
* **1.2.x**: <http://svn.apache.org/repos/asf/wicket/branches/wicket-1.2.x>

There's also a Maven 2 repository providing SNAPSHOTs available here:

{% highlight xml %}
<repositories>
    <repository>
        <releases>
            <enabled>false</enabled>
        </releases>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <id>apache.snapshots</id>
        <name>Apache Snapshot Repository</name>
        <url>https://repository.apache.org/content/groups/snapshots</url>
        <layout>default</layout>
    </repository>
</repositories>
{% endhighlight %}
