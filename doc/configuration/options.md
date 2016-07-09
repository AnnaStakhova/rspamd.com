---
layout: doc_conf
title: Rspamd Configuration
---
# Rspamd options settings

## Introduction

The options section defines basic Rspamd behaviour. Options are global for all types of workers. The default options are shown in the following example snippet:

~~~ucl
filters = "chartable,dkim,spf,surbl,regexp,fuzzy_check";
raw_mode = false;
one_shot = false;
cache_file = "$DBDIR/symbols.cache";
map_watch_interval = 1min;
dynamic_conf = "$DBDIR/rspamd_dynamic";
history_file = "$DBDIR/rspamd.history";
check_all_filters = false;
dns {
    timeout = 1s;
    sockets = 16;
    retransmits = 5;
}
tempdir = "/tmp";
url_tld = "${PLUGINSDIR}/effective_tld_names.dat";
classify_headers = [
	"User-Agent",
	"X-Mailer",
	"Content-Type",
	"X-MimeOLE",
];

control_socket = "$DBDIR/rspamd.sock mode=0600";
~~~

## Global options

* `filters`: comma separated string that defines enabled **internal** Rspamd filters; for a list of the internal filters please check the [modules page](../modules/)
* `one_shot`: if this flag is set to `true` then multiple rule triggers do not increase the total score of messages (however, this option can also be individually configured in the `metric` section for each symbol)
* `cache_file`: used to store information about rules and their statistics; this file is automatically generated if Rspamd detects that a symbol's list has been changed.
* `map_watch_interval`: interval between map scanning; the actual check interval is jittered to avoid simultaneous checking, so the real interval is from this value up to 2x this value
* `check_all_filters`: turns off optimizations when a message gains an overall score more than the `reject` score for the default metric; this optimization can also be turned off for each request individually
* `history_file`: this file is automatically created and refreshed on shutdown to preserve the rolling history of operations displayed by the WebUI across restarts
* `temp_dir`: a directory for temporary files (can also be set via the environment variable `TMPDIR`).
* `url_tld`: path to file with top level domain suffixes used by Rspamd to find URLs in messages; by default this file is shipped with Rspamd and should not be touched manually
* `pid_file`: file used to store PID of the Rspamd main process (not used with syst.html)
* `min_word_len`: minimum size in letters (valid for utf-8 as well) for a sequence of characters to be treated as a word; normally Rspamd skips sequences if they are shorter or equal to three symbols
* `control_socket`: path/bind for the control socket
* `classify_headers`: list of headers that are processed by statistics
* `history_rows`: number of rows in the recent history table
* `explicit_modules`: always load modules from the list even if they have no configuration section in the file
* `disable_hyperscan`: disable Hyperscan optimizations (if enabled at compile time)
* `cores_dir`: directory where Rspamd should drop core files
* `max_cores_size`: maximum total size of core files that are placed in `cores_dir`
* `max_cores_count`: maximum number of files in `cores_dir`
* `local_addrs` or `local_networks`: map or list of IP networks used as local, so certain checks are skipped for them (e.g. SPF checks)

## DNS options

These options are in a separate subsection named `dns` and specify the behaviour of Rspamd name resolution. Here is a list of available tunables:

* `nameserver`: list (or array) of DNS servers to be used (if this option is skipped, then `/etc/resolv.conf` is parsed instead). It is also possible to specify weights of DNS servers to balance the payload, e.g.

~~~ucl
options {
	dns {
		# 9/10 on 127.0.0.1 and 1/10 to 8.8.8.8
		nameserver = ["127.0.0.1:10", "8.8.8.8:1"];
		# or
		# nameserver = "127.0.0.1:10";
		# nameserver = "8.8.8.8:1";
	}
}
~~~

* `timeout`: timeout for each DNS request
* `retransmits`: how many times each request is retransmitted before it is treated as failed (the overall timeout for each request is thus `timeout * retransmits`)
* `sockets`: how many sockets are opened to a remote DNS resolver; can be tuned if you have tens of thousands of requests per second).

## Upstream options

**TODO**