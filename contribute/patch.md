---
layout: default
title: Submit a patch
---

TODO: this is just a set of basic instructions.  We need to add more information here.

## High level: ##
The steps to submitting a patch for Wicket are:

* Identify an issue that you want to fix or improve
* Search JIRA and the mailing list to see if it's already been discussed
* If it's a bug or a feature request, open a [JIRA issue](https://issues.apache.org/jira/browse/WICKET)
* [Create a quickstart](../start/quickstart.html) that you can use for prototyping the feature or demonstrating the bug
** Attach this quickstart to the JIRA issue if it's representing a bug report.
* Get Subversion running
* [Checkout out the code](../start/download.html#snapshots_and_latest_bleedingedge_code)
* Make your changes
* Create the patch:

{% highlight console %}
svn add any_files_you_created
svn diff > /tmp/fix-WICKET-NNNN.patch
{% endhighlight %}

* Attach that file (/tmp/fix-WICKET-NNNN.patch) to the JIRA
