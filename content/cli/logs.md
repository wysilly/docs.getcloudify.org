---
layout: bt_wiki
title: logs
category: Docs
draft: false
abstract: Cloudify's Command-Line Interface
weight: 110
---

The `cfy logs` command is used to manage log files on a Cloudify Manager.

You can use the command to download, backup and purge Cloudify Manager service logs.

To use the command you must have the credentials (user and key) set in the local context and must `cfy use -t MANAGEMENT_IP` prior to running the command.

#### Optional flags

These will work on each command:

* `-v, --verbose` - Show verbose output. You can supply this up to three times (i.e. -vvv)
* `-h, --help` - Show this message and exit.

## Commands

### backup

#### Usage 
`cfy logs backup [OPTIONS]`

Create a backup of all logs under a single archive and save it on the Cloudify Manager under /var/log.

&nbsp;
#### Example

```markdown
$ cfy logs backup
...

Creating logs archive in manager: /tmp/cloudify-manager-logs\_20170330T122201\_10.239.0.208.tar.gz
Backing up manager logs to /var/log/cloudify-manager-logs\_20170330T122201\_10.239.0.208.tar.gz

...
```

### download

#### Usage 
`cfy logs download [OPTIONS]`

Download an archive containing all of the Cloudify Manager service logs.

#### Optional flags

* `-o, --output-path TEXT` - 
						The local path to which to save the download.

&nbsp;
#### Example

```markdown
$ cfy logs download
...

Creating logs archive in manager: /tmp/cloudify-manager-logs\_20160623T070559\_10.10.1.10.tar.gz
Downloading archive to: /home/nir0s/work/local-bootstrap-env
Removing archive from manager...

...
```

### purge

#### Usage 
`cfy logs purge [OPTIONS]`

Purge all log files on the manager.

Truncate all logs files under /var/log/cloudify.

This enables you to take extreme measures to clean up data from Cloudify Manager. For example, you might choose to do this when the disk is full due to some bug that has caused the logs to bloat.

The `-f, --force` flag is mandatory as a safety measure.

#### Optional flags

* `--backup-first` - 	Creates a backup before purging.

{{% gsWarning title="Forced Prerequisites Installation" %}}
USE WITH CARE!<br>
Log files in Cloudify Manager are rotated. `cfy purge` is a safety measure in case disk space on Cloudify Manager runs out, and  should only be used in extreme situations.
{{% /gsWarning %}}


&nbsp;
#### Example

```markdown
$ cfy logs purge -f
...

Purging manager logs...

...
```