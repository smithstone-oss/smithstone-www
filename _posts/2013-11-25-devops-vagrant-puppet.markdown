---
layout: default
title: Devops with Vagrant & Puppet
---

# Devops with Vagrant & Puppet

## Devops
Devops to me is about controlling your deployment environment without distrubing the sysadmin team, as they tend to have a to much to do as is, and you don't want to stack more work on to them just to deploy the new feature you have produced in the last sprint. It brings the whole deployment process into the development cycle, that is now agile with short iterations that sysadmin teams can't really keep up with.

## Vagrant
[Vagrant](http://vagrantup.com) is a VM provisioning tool, it has the ability to create [Virtual Box Instances](http://virtualbox.org), and with the re-architecture of v2, with the use of plugins can spin up instances in [VMWare Fusion/VMWare Player](http://www.vmware.com), and [Amazon AWS](http://aws.amazon.com).
We are going to see how this works first with virtual box, so make sure you have VirtualBox installed.
So how does it work, you download a [binary](http://downloads.vagrantup.com/) for your system, once that has complete and you've installed it, you are going to need a base box to play with. A [base](http://www.vagrantbox.es/) box is a prepackaged system that has been prepared for use with Vagrant. There are several ways to get the box you can run ``` vagrant box add <URL> ``` where the url is one from the previously mentioned site, or you can add it when you run the vagrant init command, which is the way you setup vagrant.

### Setting up
{% highlight bash %}
vagrant init precise64 http://files.vagrantup.com/precise64.box
{% endhighlight %}
This command will make sure that an image is downloaded from the url and named precise64 inside of ~/.vagrant.d/boxes.
If you already have boxes available on your system i.e. you have ran 
{% highlight bash %}
vagrant box add
{% endhighlight %}
then you can simple run
{% highlight bash %}
vagrant init <NAME>
{% endhighlight %}
where name is a directory from ~/.vagrant.d/boxes

Once you have ran that command you will see a Vagrant file this is the configuration for vagrant where we can specifiy options for the virtual machine from hostname, to public / private networks , port forwarding and what type of provisioning we need ie base, puppet single , puppet master , chef solo , chef.

## Up and running
Now we have vagrant initialised we can run 

{% highlight bash %}
vagrant up
{% endhighlight %}
without modifying the configuration file and it will create an instance of the base box and nothing else. Once vagrant has come up we can access the box over ssh using 
{% highlight bash %}
vagrant ssh
{% endhighlight %}
as all vagrant boxes are configured with ssh key access. 
You will also notice that if you startup VirtualBox a new VM has been created. 
To exit the VM just login.

## Halt and Destroy
Once you have finished with the VM you can either preseve the state of the machine and bring down by running 
{% highlight bash %}
vagrant halt
{% endhighlight %}
or destroy the instance using 
{% highlight bash %}
vagrant destroy
{% endhighlight %}

# Puppet
Puppet is a system configuration tool that allows you to specify how your system is setup in terms of resources, and build up a system using roles, there are many types of [resources](http://docs.puppetlabs.com/references/latest/type.html) the main ones we are going to see are package , file, service, class.

### Class resource
This is a resource to collect a set of other resources, you would typically use a class to a role for a Nginx server with the following declaration

{% highlight ruby %}
class nginx {

    package { "nginx": ensure => installed}

    service { "nginx" : ensure => running , require => Package["nginx"] , hasrestart => true  }
}
{% endhighlight %}
What we have done here is ask puppet to install the nging package and ensure the service is up and running, its that easy to get started, underneath what puppet is doing is asking the underlying OS to use its the package manager to install the package and any dependencies that are needed, and then start the service.

### File resource
This is a resouce that can be used to ensure a file / directory / symlink is setup on the system.


{% highlight ruby %}
file { "/etc/motd" :
    ensure => 'present',
    content => "This system is monitored by puppet!"
}
{% endhighlight %}

# Integration Puppet and Vagrant
So we have a collection of puppet resources to apply to out vagrant box how do we go about applying this.

## Configure Vagrant
Inside the Vagrant file there is a block of commented out code that looks like this

{% highlight ruby %}
   config.vm.provision :puppet do |puppet|
     puppet.manifests_path = "manifests"
     puppet.manifest_file  = "site.pp"
     puppet.module_path = "modules"
   end
{% endhighlight %}

What this does it instruct Vagrant on the provision solution for the VM here we are configuring the puppet provisioner,
the block of code is a ruby block, so we can configure the the available puppet options.
Line 2 instructs puppet where the manifest folder is,
Line 3 instructs puppet what the manifest file is called which should be inside the manifest_path folder
Line 4 instructs puppet where the modules are located.
Once we have put the example resources inside of the site.pp file, we can run
{% highlight bash %}
vagrant reload
{% endhighlight %}
if the vagrant box is running, which will stop and start the box, or if the box is not up 

{% highlight bash %}
vagrant up
{% endhighlight %}

If at any point we change the modules, manifests , site.pp file we don't need to restart the VM , we can run


{% highlight bash %}
vagrant provision
{% endhighlight %}
Which will run the provisioners configured inside vagrant.



## Summary
We now have a running vm inside of virtual box configured using vagrant and the system configured using puppet.
Have fun messing around creating classes, resources, packages.

#### Resources
* [Example Setup Code](https://github.com/smithstone-oss/devops-vagrant-puppet)
* [Puppet](http://puppetlabs.com)
* [Vagrant](http://vagrantup.com)
* [PuppetForge](https://forge.puppetlabs.com/)

