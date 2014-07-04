---
layout: post
title: Installing Puppet Cloud Provisioner
date: '2014-06-12T16:39:00-04:00'
tags:
- puppet aws
tumblr_url: http://peterskim.tumblr.com/post/88600499828/installing-puppet-cloud-provisioner
---
I’m working on a project where we need a way to programmatically provision AWS resources that use Puppet to manage the software and configuration on those resources. Maybe there are some other tools out there to do this but the few I’m considering are: 1) Amazon Cloud Formation, 2) Puppet Cloud Provisioner and 3) Amazon Web Services CLI.

I started trying to evaluate the Puppet Cloud Provisioner but I ran into trouble almost immediately. The Puppet Forge page for the Cloud Provisioner makes it look like all you need to run is the following to install Cloud Provisioner in your Puppet Open Source installation:

>puppet module install puppetlabs-cloud_provisioner

This runs successfully but when I run

    puppet help

an exception is thrown:

    Error: Could not autoload puppet/face/node_aws/list: no such file to load — guid

as described on this blog: [http://jasonoakley.com/puppet/beginning-adventures-with-puppet-from-puppetlabs/](http://jasonoakley.com/puppet/beginning-adventures-with-puppet-from-puppetlabs/)

Unfortunately, there isn’t much help on the Puppet Forge page, the Puppet Open Source documentation, or anywhere else. Fortunately, the fix is easy:

    sudo gem install guid
    sudo gem install fog

For some reason, the Gemfile for Cloud Provisioner states that guid and fog are non-required dependencies but it seems like they obviously are. 

Anyway, after configuring your ~/.fog file with your AWS keys as documented here:
[http://docs.puppetlabs.com/pe/latest/cloudprovisioner_configuring.html](http://docs.puppetlabs.com/pe/latest/cloudprovisioner_configuring.html)
you should be able to run the node_aws commands successfully.

It’s not rocket science but at the same time, if you’re new to Puppet and unfamiliar with Ruby, it’s easy to feel like giving up after getting that initial error.
