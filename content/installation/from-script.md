---
layout: bt_wiki
title: Installing with get-cloudify.py
category: Installation
draft: false
weight: 300

---

[get-cloudify.py](http://gigaspaces-repository-eu.s3.amazonaws.com/org/cloudify3/get-cloudify.py) is a Python script that is dedicated to installing Cloudify on different platforms.

{{% gsWarning title="Prerequisites Installation" %}}
By default, this script does not install any prerequisites. You can add a `--force` flag, which will install all prerequisites, without prompting you for anything other than a sudoer password (if required).
{{% /gsWarning %}}

## Installation Prerequisites
For all users the following components are required:

* [Python 2.7.X](https://www.python.org/downloads/)
* [pip 6.0+](https://pip.pypa.io/en/stable/installing/)
* [virtualenv 12.0+](https://virtualenv.readthedocs.org/en/latest/installation.html)

* **Windows Users**  
  PyCrypto ([32bit](http://repository.cloudifysource.org/org/cloudify3/components/pycrypto-2.6.win32-py2.7.exe) / [64bit](http://repository.cloudifysource.org/org/cloudify3/components/pycrypto-2.6.win-amd64-py2.7.exe))

* **Linux Users**  
  * Python development headers (`python-dev` in Ubuntu/Debian or `python-devel` in CentOS/RHEL)
  * C compiler (`gcc`)

* **(OS X Users)**  
  [Xcode Command Line Tools](https://developer.apple.com/library/ios/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-DOWNLOADING_COMMAND_LINE_TOOLS_IS_NOT_AVAILABLE_IN_XCODE_FOR_OS_X_10_9__HOW_CAN_I_INSTALL_THEM_ON_MY_MACHINE_)

## Downloading and Running the Script
1. [Download the script](http://gigaspaces-repository-eu.s3.amazonaws.com/org/cloudify3/get-cloudify.py) by right-clicking the link and selecting **Save**.
2. Run `python get-cloudify.py -h` to access the Help documentation.

### Installing the Latest Stable Release in a new virtualenv
Run the following command to create new a virtualenv (`my_virtualenv`) in which to install Cloudify.

```{{< gsHighlight  bash  >}}
$ python get-cloudify.py -e my_virtualenv --installvirtualenv
...

20:21:40 [INFO] [get-cloudify.py] virtualenv is already installed in the path.
20:21:40 [INFO] [get-cloudify.py] Dropping root permissions...
20:21:40 [INFO] [get-cloudify.py] Creating Virtualenv my_virtualenv...
20:21:41 [INFO] [get-cloudify.py] Installing cloudify...
20:21:48 [INFO] [get-cloudify.py] You can now run: "source my_virtualenv/bin/activate" to activate the Virtualenv.
...

$ source my_virtualenv/bin/activate

{{< /gsHighlight >}}```

{{% gsNote title="Installing within a virtualenv" %}}
If you are already within a virtualenv when running the script and have not supplied the `--virtualenv` flag, the script installs Cloudify within the currently-active virtualenv.
{{% /gsNote %}}

### Installing the Most Recent Milestone Release
Run the following command to install the latest stable release.

```{{< gsHighlight  bash  >}}
$ python get-cloudify.py --pre
{{< /gsHighlight >}}```

### Installing a specific version
Run the following command to install a specific version of Cloudify:

```{{< gsHighlight  bash  >}}
$ python get-cloudify.py --version 3.2a4
{{< /gsHighlight >}}```

The full list of PyPi versions is [available here](https://pypi.python.org/pypi/cloudify/json).
