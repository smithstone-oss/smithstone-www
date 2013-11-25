# Devops with Vagrant & Puppet

## Devops
Devops to me is about controlling your deployment environment without distrubing the sysadmin team, as they tend to have a to much to do as is, and you don't want to stack more work on to them just to deploy the new feature you have produced in the last sprint. It brings the whole deployment process into the development cycle, that is now agile with short iterations that sysadmin teams can't really keep up with.

## Vagrant
[Vagrant](http://vagrantup.com) is a VM provisioning tool, it has the ability to create [Virtual Box Instances](http://virtualbox.org), and with the re-architecture of v2, with the use of plugins can spin up instances in [VMWare Fusion/VMWare Player](http://www.vmware.com), and [Amazon AWS](http://aws.amazon.com).
We are going to see how this works first with virtual box, so make sure you have VirtualBox installed.
So how does it work, you download a [binary](http://downloads.vagrantup.com/) for your system, once that has complete and you've installed it, you are going to need a base box to play with. A [base](http://www.vagrantbox.es/) box is a prepackaged system that has been prepared for use with Vagrant. There are several ways to get the box you can run ``` vagrant box add <URL> ``` where the url is one from the previously mentioned site, or you can add it when you run the vagrant init command, which is the way you setup vagrant.

### Setting up
`` 
vagrant init precise64 http://files.vagrantup.com/precise64.box
`` 

This command will make sure that an image is downloaded from the url and named precise64 inside of ~/.vagrant.d/boxes.
If you already have boxes available on your system i.e. you have ran 

``
vagrant box add
``

then you can simple run

``
vagrant init <NAME>
`` 

where name is a directory from ~/.vagrant.d/boxes

Once you have ran that command you will see a Vagrant file this is the configuration for vagrant where we can specifiy options for the virtual machine from hostname, to public / private networks , port forwarding and what type of provisioning we need ie base, puppet single , puppet master , chef solo , chef.

## Up and running
Now we have vagrant initialised we can run 

``
vagrant up
`` 

without modifying the configuration file and it will create an instance of the base box and nothing else. Once vagrant has come up we can access the box over ssh using 

`` 
vagrant ssh
`` 

as all vagrant boxes are configured with ssh key access. 
You will also notice that if you startup VirtualBox a new VM has been created. 
To exit the VM just login.

## Halt and Destroy
Once you have finished with the VM you can either preseve the state of the machine and bring down by running 

``
vagrant halt
`` 

or destroy the instance using 

``
vagrant destroy
`` 


