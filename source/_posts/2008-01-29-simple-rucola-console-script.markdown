---
layout: post
title: Simple Rucola console script
comments: true
category: Rucola
---
I made a small script to help in debugging rucola projects. It will start an interactive irb session and boot your rucola app in the test environment.I saved it in ‘script/console’. You can use it to experiment with your classes.

``` ruby
    #!/usr/bin/env ruby
    ENV['RUBYCOCOA_ROOT'] = File.join(File.dirname(__FILE__), '..')
    ENV['RUBYCOCOA_ENV'] = 'test'
    libs = "-r rubygems"
    libs << " -r rucola"
    libs << " -r #{ENV['RUBYCOCOA_ROOT']}/config/boot"
    exec "irb #{libs}"
```
