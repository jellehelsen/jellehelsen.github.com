---
layout: post
title: SSH password guessing (part 2)
comments: true
category: Security
---
After disabling password login for ssh I was still seeing password guessing. I'm confident disabling password login should be sufficient, but I was still not happy with the situation. I'm not really happy that the firewall built into Mac OS X has no option to deal with this situation.

### Presenting SshGuard
<!--more-->
Since Mac OS X has no solution for this out of the box, I created one. SshGuard is a script that monitors the log file /var/log/secure.log for ssh errors containing "Invalid user" or "authentication error". When a host has too many failed authentications, his ip address gets blocked by the firewall.

SshGuard is written in ruby and distributed as a gem. Installation is simple:

``` bash
    sudo gem install ssh_guard
    sudo ssh_guard -i #to install and load the launchd plist file
```

After this launchd will keep SshGuard running at all times. It will keep monitoring the log files for authentication errors and block hosts that are trying to guess passwords. Host blocking rules will be added with rule number 100 to keep it among the first rules to be applied.

At the moment it only works on Mac OS X. It's only been tested on Mac OS X Server 10.6, but it should work on any Mac OS X system. It could probably be ported to other Unix system without much trouble. The source code is available [here](http://github.com/jellehelsen/ssh_guard). Any bugs or feature request can be reported [here](http://github.com/jellehelsen/ssh_guard/issues). 

