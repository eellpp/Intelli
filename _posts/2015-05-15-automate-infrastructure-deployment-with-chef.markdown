---
layout: post
title: "Automate Infrastructure Deployment With Chef"
date: 2015-05-15T00:00:00+08:00
tags: chef devops
---
Chef can model your infrastructure as source code. This makes the process of infrastructure deployment repeatable and maintainable. It has three essential components 

- Chef Server 
- WorkStation and 
- Nodes 

Workstation is the development machine. Changes are pushed from WorkStation to Chef Server which in turn manages the nodes. 
The source code to model the infrastructure are scripts called as recipes in chef terminology. A collection of this recipe which manages the application could be a cookbook. You can also have a library pattern where a specific piece of infrastructure is modeled as a cookbook. Eg a database cookbook. 

During development, you would need to quick test these recipes. Install Vagrant on you workstation to test these recipes.
For production deployment, you would be using the chef server which stores the cookbook and manages the nodes. You push your changes to chef server which in turn deploys those changes to individual nodes. Communication with the chef server from workstation is done with knife commands. 

To start with, create a chef server account at https://manage.chef.io/login.  Once you have registered with your organization, download the validation keys. 

Chef server supports “multiple organizations” pattern to manage multiple products in an organization where the teams have different access rights to each product infrastructure.  Thus you can keep the infrastructure of these product totally isolated by associating the cookbooks and nodes under different products. 

Create a vagrant box or download the popular available box from vagrant website. Once you have setup your vagrant box, your workflow with vagrant will involve using command like vagrant up, halt and destroy.  During the process of configuring the vagrant env you have to modify the VagrantFile to specify details like memory etc. 

The workflow with chef involves cookbook creation, local testing and eventual deployment over private or public Cloud

Some the common practices while working with chef

To generate the template for Chef Cookbook 

{% highlight bash %}
chef generate cookbooks cookbooks/
{% endhighlight %}

Use Berkshelf for managing the dependencies
{% highlight bash %}
berks install
berks upload <cookbook_name> --no-ssl-verify # to upload the cookbook to hosted server
{% endhighlight %}

Use Chef Test Kitchen for quick testing using test vm. Some of the common commands are
{% highlight bash %}
kitchen create
kitchen converge
kitchen destroy
{% endhighlight %}

For n-tier application it is useful to create roles and assign recipes in each role. Thus a webserver could have a different runlist and configuration from application server and database server.  You can add the role-name.json file to the roles folder and upload the file to chef server
{% highlight bash %}
#This will save the role on server
knife role create test

#Get the role json file from server
knife role show web_server -Fjson > roles/

#To push the changes in roles from file to chef server
knife role from file path/to/role/file
{% endhighlight %}

Chef provides the Environment config to configure the infrastructure as per the development workflow. There could be a development, testing and production environment which could differ by roles and recipes. 

You can provision the infrastructure using the knife command

{% highlight bash %}
 knife bootstrap <Ip-address> --ssh-port <port>--ssh-user <ssh-user> -r 'role[<role-name>]' --sudo --identity-file <path to identity file>--node-name <node-name>
{% endhighlight %}

On the hosted chef server you can view the status of all the nodes that are active and their configuration details. 

Each of your cookbook recipe has a version and chef server saves all the previous versions.
Another point to note is to pin the dependencies to versions to replicate the development stack on production versions.
