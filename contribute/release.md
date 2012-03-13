---
layout: default
title: Build a release
---

## Contents ##

* [Releasing Apache Wicket 1.4.x](#release-14)
* [Releasing Apache Wicket 1.3.x](#release-13)
* [Staging Releases using the Apache Nexus Repository](#nexus)
* [Announcing the release](#announcing)
* [Additional tasks](#additional)

## Releasing Apache Wicket 1.4.x ## {#release-14}

### Building the Release ###

#### Preparing #####

* Ask the dev@ mailing list if there are any issues that still need to be in the release (looking at JIRA is a good start)
* Release the version in JIRA
* Assuming there are none, and you are now in build mode, update the CHANGELOG-1.4 file 
(you can use JIRA for this list: go to "releases", and under "unreleased", next to your version, there's a release notes link)

Make sure your ```~/.m2/settings.xml``` contains the following definition

{% highlight console %}
<?xml version="1.0"?>
<settings>
<servers>
	<server>
	    <id>apache.releases.https</id>
	    <username>$your_apache_username</username>
	    <password>$your_apache_password</password>
	</server>
</servers>
</settings>
{% endhighlight %}

* create a release branch to do your release work in (modifying poms, etc)
* then checkout that release branch and cd into it
{% highlight console %}
$ svn cp https://svn.apache.org/repos/asf/wicket/branches/wicket-1.4.x \
https://svn.apache.org/repos/asf/wicket/branches/wicket-1.4.13 \
-m "creating 1.4.13 release branch"
Committed revision 1027818.
$ cd ..
$ svn co https://svn.apache.org/repos/asf/wicket/branches/wicket-1.4.13
... lots of output ...
Checked out revision 1027818.
$ cd wicket-1.4.13
{% endhighlight %}
* Set correct version in the pom files using the [maven-versions-plugin](http://mojo.codehaus.org/versions-maven-plugin):
{% highlight console %}
mvn versions:set -DnewVersion=1.4.13
... list of all changed artifacts changed.
mvn versions:commit 
removes all of the pom.xml.versionsBackup files.
{% endhighlight %}
* Call release.sh (still from the checkout out release branch code) **NOTE** If you get an error while generating javadocs like "`java.lang.ClassCastException: com.sun.tools.javadoc.ClassDocImpl`", run a `mvn clean install -Dmaven.test.skip=true` followed by a `mvn javadoc:jar`, then re-run `release.sh`.
* Login to the Apache Nexus Repository on http://repository.apache.org; See [Releasing with Apache Nexus](#nexus) below.  
* Select **Staging Repositories** from the left hand side, then select the repository and click **Close**.
* Test that the distribution (examples, rat, etc) is accessible from the staging repository url.
* upload non-maven artifacts to pao:
{% highlight console %}
export wicketversion=<version>
svn export http://svn.apache.org/repos/asf/wicket/common/KEYS target/dist/KEYS
ssh people.apache.org mkdir public_html/wicket-$wicketversion
scp -r target/dist target/m2-repo people.apache.org:public_html/wicket-$wicketversion
{% endhighlight %}
* Commit these modified pom files to the release branch.
* Send vote message to dev@ mailing list.  Include link to the closed repository for testing against.
* Wait requisite 72 hours for the vote to pass (we hope)
* Copy release to apache mirrors
{% highlight console %}
ssh people.apache.org
mkdir /www/www.apache.org/dist/wicket/1.4.0
mv ~/public_html/wicket-1.4.0/dist/* /www/www.apache.org/dist/wicket/1.4.0
chown -R :wicket /www/www.apache.org/dist/wicket/1.4.0
{% endhighlight %}
* Login to repository.apache.org, select the closed repository and click **Release**.  It will be accessible immediately through the apache release repo and then within 1-2 hours through maven central. 
* If the release is voted down you can **Drop** the staged release and then restage later after incorporating the necessary changes.
* Tag the release in Git:
{% highlight console %}
git checkout build/wicket-1.5.4
git tag release/wicket-1.5.4
git push --tags
{% endhighlight %}
* Wait until repo1.maven.org has picked up the release artifacts.
* Wait 24 hours until mirrors picked up release artifacts
* Announce (see [below](#announcing))

## Staging Releases using the Apache Nexus Repository ## {#nexus}

The key to stage and manage repositories on https://repository.apache.org are your Apache Commiter credentials.

### Staging Artifacts ###

When you run **mvn -P release deploy** the generated artifacts will be uploaded into a Nexus staging repository.

Maven sends a username/password when attempting to upload the artifacts.  

The ```<id>``` of the staging repository is: **apache.releases.https**.

Place a ```<server>``` definition in your ```~/.m2/settings.xml``` file like this:

{% highlight console %}
<?xml version="1.0"?>
<settings>
<servers>
	<server>
	    <id>apache.releases.https</id>
	    <username>$username</username>
	    <password>$password</password>
	</server>
</servers>
</settings>
{% endhighlight %}

This works if you can deploy artifacts into the repository.apache.org properly (i.e. you don't see failures mentioning 401 errors).

### Managing Staged Artifacts ###

By logging in with your Apache committer credentials to https://repository.apache.org you can access the management interface for the staged artifacts.

The interface will allow you to:
* View the staging repositories.
* Close staging repositories.
* Release closed repository artifacts (into the release repo and after 1-2 hours into central as well).
* Drop open and closed staging repositories.

More information is available at: [Publishing Maven Artifacts](http://www.apache.org/dev/publishing-maven-artifacts.html#staging-maven)

## Announcing the release ## {#announcing}

Edit the \_config.yaml file. This file contains a site wide configuration
section specific to Wicket:

{% highlight yaml %}
wicket:
    version:    1.4.10
    released:   2010-08-11
    versions:   [1.3.7, 1.3-SNAPSHOT, 1.4.9, 1.4.10, 1.4-SNAPSHOT, 1.5-M1, 1.5-SNAPSHOT]
{% endhighlight %}

You'll need to edit this part: modify the version, update the released date,
and add the new version to the versions list (remove any stale releases,
typically just leave the previous release as well).

* Write an announcement in the \_posts directory
* Restart jekyll to regenerate the pages: it will automatically generate the
  correct links in the navigation menu, quickstart and downloads page. It will
  update the doap.rdf file, atom.xml rss feed and index.html file so that it 
  contains your announcement.
* Send email to: users@wicket.apache.org, dev@wicket.apache.org, announce@wicket.apache.org, announce@apache.org
* Misc. websites like javalobby, serverside, onjava etc.

## Additional tasks ## { #additional }

* Commit the JavaDocs to
  [https://svn.apache.org/repos/asf/wicket/common/site/apidocs/1.4](https://svn.apache.org/repos/asf/wicket/common/site/apidocs/1.4)
  svnpubsub will push this directly to our website. (example script below)

{% highlight console %}
# do this in your locally-checked-out release branch (i.e. svn.../releases/wicket-1.4.13)
mvn javadoc:aggregate
# note that you may need to tweak these paths to fit your configuration:
cp -r target/site/apidocs/* ../wicket-common/site/apidocs/1.4/
cd ../wicket-common/
# call "svn add" for files that are new (there's probably an easier way to do this)
svn stat | grep -e '^?' | sed 's/\?\W*//' | xargs svn add
svn commit -m "changes to update javadocs to latest release"
{% endhighlight %}
