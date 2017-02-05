---
layout: bt_wiki
title: Installing using packages for common OS distributions
category: Installation
draft: false
weight: 200

---

Cloudify packages significantly reduce the initial complexity of the installation process and are the recommended way of installing for new users.

This installation method doesn't require an active internet connection during
the installation process.

##Installation Workflow

### Linux

### Centos/RHEL

1. [Download the installer](http://getcloudify.org/downloads/get_cloudify_3x.html)
2. If running in graphical environment, proceed with graphical package installer
of your environment by double clicking the file you just downloaded.
3. Alternatively, in your terminal run the following command (replacing `<pkg.rpm>` with
the path of the file you just downloaded):

{{< gsHighlight bash>}}
$ sudo rpm -i <pkg.rpm>
{{< /gsHighlight >}}

#### Debian/Ubuntu

1. [Download the installer](http://getcloudify.org/downloads/get_cloudify_3x.html)
2. If running in graphical environment, proceed with graphical package installer
of your environment by double clicking the file you just downloaded.
3. Alternatively, in your terminal run the following command (replacing `<pkg.deb>` with
the path of the file you just downloaded):

{{< gsHighlight bash>}}
$ sudo dpkg -i <pkg.deb>
{{< /gsHighlight >}}

### Windows

The Windows installer is a single executable which installs the following:

* Python 2.7.x
* Pip
* Virtualenv
* Cloudify

{{% gsNote title="Installing Prerequisites" %}}
Python's installation requires [Microsoft Visual C++ 2008 Redistributable](https://www.microsoft.com/en-us/download/details.aspx?id=29).
Install it if you stumble upon an error during the Python installation.
{{% /gsNote %}}

{{% gsNote title="Installing Python and Pip" %}}
During Python's setup wizard, you will be able to choose whether to install pip or not.
Regardless of what you choose, pip will be installed in the next steps since it is
required by Cloudify.
{{% /gsNote %}}

##### To install on Windows

1. [Download the installer](http://getcloudify.org/downloads/get_cloudify_3x.html).
2. Run the installer and follow the installation instructions.
3. When the installation is finished, double click the new Cloudify icon on your desktop.

