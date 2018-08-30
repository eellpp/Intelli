---
layout: post
title: "Continuous Integrations With Jenkins and BitBucket"
date: 2015-03-01T00:00:00+08:00
tags: ci tools devops
---
Two essential components required for continuous integration are:  a central place to hold the repository and automated build tool. With these in place, at any time, anyone can checkout a working version of the code which could be deployed or further enhanced with additional features. 

We are using BitBucket to hold our Repo and Jenkins as our Continuous Integration build tool. The article details steps for setting this up. The Integration server was running on Ubuntu OS.

While setting up jenkins with bitbucket are trying to accomplish two things here

- Connect Jenkins to the repo at BitBucket. 
- Allow BitBucket to notify jenkins when there is new push

### Connect Jenkins to the repo at BitBucket ###

The repo at bitbucket is a private repo and we will use the public/private key for authentication

Generate a Deployment Key for BitBucket read only access 
{% highlight bash %}
ssh-keygen -f ~/.ssh/deployment_key.rsa -t rsa -b 4096
{% endhighlight %}
Ignore the passphrase (which may be good for additional hardened security)

This will create two files

- deployment_key.rsa (private)
- deployment_key.rsa.pub (public)

#### Bitbucket configuration ####
In the settings/Deployment Keys  section of your repo, add a new entry and copy the public key from above (deployment_key.rsa.pub)

#### Jenkins Configuration ####
Jenkins runs under the system user ‘jenkins’ with the home directory set as /var/lib/jenkins (ubuntu). Under this user’s home dir, you have to create a .ssh folder and copy the private key deployment_key.rsa into it. Also create a config file with following content
{% highlight bash %}
Host bitbucket.org
    IdentityFile ~/.ssh/deployment_key.rsa
{% endhighlight %}

Make sure that the jenkins user has read access to .ssh folder and files in that
{% highlight bash %}
su jenkins
sudo chown -R jenkins ~/.ssh
{% endhighlight %}
(If you don’t have the jenkins user password you can change it with sudo passwd command )

In the Jenkins webapp, in your projects configuration, in the configure section, under the "Source Code Management" select the option for Git. Under this section,
for the URL enter the ssh url of your repo from  BitBucket in the form
 git@bitbucket.org:<username>/<project>.git 
select the credentials as none

Save the Configuration

You can test whether jenkins is able to access the bitbucket repo using the access keys by testing with this command at the command line (while logged in as jenkins user)
{% highlight bash %}
git -c core.askpass=true ls-remote -h git@bitbucket.org:<username>/<project>.git  HEAD
{% endhighlight %}

### Allow BitBucket to notify jenkins when there is new push ###

install the jenkins bitbucket plugin

#### BitBucket Config ####
In the settings/Webhooks  section of your repo, add a new webhook with the URL as
http://<jenkins domain>:<port>/bitbucket-hook/
Set the trigger to Repository Push

#### Jenkins Config ####
In the Jenkins webapp, in your projects configuration, in the  “Build Triggers” section, enable Build when a change is pushed to BitBucket
Now whenever the build is triggered at bitbucket, jenkins will do a build


