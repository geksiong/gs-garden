---
title: Application Logging
description: Notes on logging in software applications
date: 2019-07-29
updated: 2020-04-15

tags:
- logging

---
## Some Best Practices

* DO include a timestamp
* DO format in JSON
* DON’T log insignificant events
* DO log all application errors
* MAYBE log warnings
* DO turn on logging
* DO write messages in a human-readable form
* DON’T log informational data in production
* DON’T log anything a human can’t read or react to

*Source: https://opensource.com/article/18/9/open-source-log-aggregation-tools*

# My Thoughts

### Format

* Make it parseable
  * JSON format is popular because it can be easily consumed by log aggregators. It can also contain more complex data structures, at the expense of human-readability
  * Plain text is also fine, as long as it has a clear structure.
    * Fixed width columnns are good for human-readability. Try to keep the fixed-width (or somewhat regular width) stuff on the left for readability e.g. dates, ip addresses, etc, and the variable length stuff on the right
  * URL-encoding may be useful while keeping things human-readable
* Regardless of the format, use it consistently throughout all your modules


### What to log

* Files
  * Main log
  * Audit log
  * Performance log?
* Log levels
  * DEBUG
  * INFO
  * WARN
  * ERROR
* Log message
  * Always make this a one-liner. Multi-line logs are difficult to parse or manipulate.
  * Stacktraces - these are multi-line, so need to assess.
* Fields
  * Thread id
  * Datetime
    * Use ISO format, with timezone.
    * ISO format is easily sortable, should you need to combine 2 logs.
    * Log in localtime (since you are using ISO format with timezone there is no ambuguity)
  * Thread priority (useful?)
  * Source IP
    * If behind a load-balancer, read the forwarded IP
    * Note that multiple IPs may be forwarded, and may contain both IPv4/IPv6 addresses
  * Host
    * Useful for merging logs, but it does take up space
  * Module / API name
    * Which component is performing the operation?
    * Can include sub-module / action name as well e.g. "User-Reg", "Profile-Update"
    * Hard-code this for performance
    * If we deploy multiple API versions, should include version number in the module name
  * User id (or proxied id)
    * Might as well make this a standard field
  * Session / hashed session id
    * This is useful if a user can have multiple sessions at once (web vs mobile)
  * Transaction id / request id
    * Several log records may reference the same transaction id
  * End time / Time taken of transaction (if available)
    * If this is logged separately after the initial request event, then the user id / transaction id
  * Log message
* If a field is empty, log a '-' so it's clear that there is nothing to log for that field (helps with parsing also)

### What not to log

* Never log passwords, personal identifiers
* User ids should not be logged, but it depends on the client
  * If user ids cannot be logged, some form of proxied id is needed
    * One option is encryption
    * Hashes can be used if we have a mapping table to reverse map hashes to their original ids. We cannot use this hash outside of logging, definitely not as external ids. Feasible only if we control the user database.
      
## Log maintenance

* Online retention
  * These are for immediate troubleshooting. Anything older will be deleted.
* Archival
  * Check with client what facilities are there for long-term storage.
* Monitor the space used by logs and take actions if necessary
  * e.g. increase storage space, change online retention policy, ship logs to other storage
* Gzip the older logs
  * Only for log files which are no longer being appended to.
  * Most linux systems have zless, zgrep (check first)
* (if we manage our own Tomcat) Don't forget catalina.out
* (if we are root) For batch jobs, don't forget root's cron mailbox.

## Servers and Middleware

### Mail Spool

* the mail spool is an often overlooked place where logs can grow
* either disable (if you have other mechanisms for logging) or clean up regularly

### Logrotate

* do not use logrotate if the log file is already being rotated by the app
* use copyandtruncate, otherwise you may find the app "sticking" to the renamed file. but also be aware copying takes time and logs may be lost during this action

### Tomcat Webapps

* catalina.out is produced by the console log handler
* catalina.log is produced by the file log handler
* tomcat rotates logs by default, but doesn't delete them
  * while you can specify tomcat not to rotate logs, and manage them externall (e.g. via logrotate), it is still advisable to let tomcat handle this to avoid potential data loss at the moment of rotation
  * so externally need cronjob to find and delete old logs
* webapps will need to manage their own logs

### Docker

* everything output to console is logged by docker
* docker can limit the size of logs persisted by docker
* a log forwarding mechanism is probably most ideal when using docker to host your apps
