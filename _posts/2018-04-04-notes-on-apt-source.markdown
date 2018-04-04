---
layout: post
title:  "Get package source with apt-get/apt"
categories: Admin Linux Tool 
---

To download source code of a package, first add or uncomment the `deb-src` lines in `/etc/apt/sources.list`. The run `sudo apt-get update` to make the package manager obtain available package sources. Use `apt-get source <package_name>` to download the source, bu be noticed this will extract the code for tarballs.

{% highlight bash %}
vi /etc/apt/sources/lis # add what need
sudo apt-get update
apt-get source python #example
{% endhighlight %}

Use `=` to specify a version: `apt-get source cmake=3.5`, or use `--comiple` to compile the package to a binary .deb: `apt-get source python --compile`.

For `apt` all work the same way:

{% highlight bash %}
apt source python
{% endhighlight %}

But some packages source could not be found with `apt-get` e.g. bison. Try to use `apt source bison` to ge the source code works in my case.(why?)