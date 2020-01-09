# Python-based Checks for Icinga

This git repo provides various Python-based check plugins for Nagios compatible monitoring systems like Nagios and Icinga. All checks are written and tested on Fedora >= 30 and CentOS 7.

All checks are written in Python 2, because...

* In a datacenter environment (where these checks are mainly used) the `python == python2` side is still more popular.
* In CentOS 7, Python 2.7 is the default.
* In CentOS 8, there is no default. You just need to specify whether you want Python 3 or 2.
* Support for Python 2 will end, but not in CentOS 8 (Python 2 remains available in CentOS 8 until at least 2029).

Our checks run Python 2 by using `#! /usr/bin/env python2`.

For further details have a look at https://developers.redhat.com/blog/2018/11/14/python-in-rhel-8/.


## Hints

To run a check make sure that the symbolic link `lib` points to `lib-linux`, which you have to clone from [lib-linux](https://gitlab.com/linuxfabrik-icinga-plugins/lib-linux).


# Icinga Check Plugin Developer Guidelines

## Deliverables

* The check itself.
* A nice 16x16 transparent PNG icon, for example based on font-awesome.
* optional: Grafana panel
* optional: Icinga Director Basket Config
* optional: Icingaweb2 Grafana Module .ini file
* optional: sudoers file


## Rules of Thumb

* Develop with CentOS 7/8 Minimal in mind.
* Develop with Icinga2 in mind.
* Avoid complicated or fancy (and therefore unreadable) Python statements.
* Comments and output should be in English only.
* If possible avoid libraries that have to be installed.
* Validate input.
* Check for system commands, libraries and their versions.
* It is not needed to execute system commands by specifying their full path.
* Use temp files if needed.
* Or better: use a local SQLite database if you want to use a temp file.
* Great, if the plugin executes fast and uses less ressources (cpu time, memory etc.).
* Keep in mind: Plugins have a limited runtime - typically 10 seconds max.
* Timeout gracefully on errors (for example `df` on a failed network drive) and return WARN.
* Return UNKNOWN on missing dependencies or wrong parameters.
* Mainly return WARN. Only return CRIT if the operators have to wake up at night.


## Names, Naming Conventions

define_args     > get_options
parsed          > options
unpack_perfdata > format_perfdata
set_thresholds
get_status
stats (instead of "statistics")
msgs (abbreviation for "messages")
get_greater_state > get_most_significant_state


Parameters:

* ignore-...
* disable-...
* noproxy
* insecure

Libraries:

* utils.py


## Parameters, Option Processing

There are a few reserved options that should not be used for other purposes:

	-a, --authentication    authentication password
	-C, --community         SNMP community
	-c, --critical          critical threshold
    -f, --filename          filename or directory name
	-h, --help              help
	-H, --hostname          hostname
	-l, --logname           login name
	-p, --password          password
	-p, --port              network port
	-t, --timeout           timeout
	-u, --url               URL
	-u, --username          username
	-V, --version           version
	-v, --verbose           verbose
	-w, --warning           warning threshold

* The check should be Self Configuring and/or using best practise defaults.
* Complex parameter tupels are separated by comma, or by semicolon and comma.
  "Name,Value,Warn,Crit;Name,Value,Warn,Crit"


## Help

README.md
gute Hilfetexte im Check (das "warum" erklären)


## Plugin Output

Sample Output:

```
There are warnings.
* WARN Item 1: 123M (100/200)
* Item 2: 37M
```

* Print a short concise message in the first line within the first 80 chars.
* Use multi-line output for details
* Don't print OK, WARN, CRIT
* Give a help text to solve the problem
* Multiple items checked, and ...
  - everything ok? Print "Everything is ok." in the first line, and optional the items and their data attached in multiple lines.
  - 
  - 

* bei der Ausgabe am aktuellsten `glances` unter Fedora orientieren
* Angaben von Einheiten so "schmal" wie möglich (ohne Leerzeichen und Ballast)
  * Percent: %
  * Bytes: B, K, M, G, T
  * Temperatures: C, F
  * Network: "Rx/s", "Tx/s", Mbps (Megabit per Second)
  * I/O: MB/s (Megabyte per Second)
  * Read/Write: R/s, W/s
* Used, Total
* File Sys: "/boot (sda2)"
* Tendenz-Anzeige mit "\ / -", Beispiel: "CPU / 5.0%" 
* Use ISO format for datetimestamps ("yyyy-mm-dd hh:mm:ss")
* Human readable datetimes ("Up 3d 4h", "2019-12-31 23:59:59", "1.5s")


# UOM (Unit of Measurement)

Perfdata value-suffix:

    no unit specified - assume a number (int or float) of things (eg, users, processes, load averages)
    s - seconds (also us, ms)
    % - percentage
    B - bytes (also KB, MB, TB)
    c - a continous counter (such as bytes transmitted on an interface)

