---
layout: post
title: "OpsWorks for App Deployment"
date: 2015-08-20T00:00:00+08:00
tags: opsworks aws devops
---

AWS OpsWorks is based on Chef, a tool for configuring and automating the infrastructure deployments. Since it is based on Chef, it comes with all the benefits of cookbooks and recipes and the community resources. In addition OpsWorks provides AWS specific features like auto scaling, monitoring, access to other aws resources, security and easy management of server nodes.

OpsWorks has the concept of stacks and layers. 
A stack describes all the resources of your entire application. Within a stack are the Layers that allow to group the resources in architecture patters that could define the stack.It could be a database layer, web server layer and middleware layer etc. Each Layer has cookbooks and recipes which provide the scripts to manage the resources. Chef provides abstraction like roles, environments and databags etc which models the development and deployment workflow. OpsWorks builds on top of that by providing concept of Layers and Stack. This way of partitioning things allow better security and allows managing and scaling the components independently. 

To start with OpsWorks, you need create some IAM roles to enable access privileges.

- Service Role: This role the allow OpsWorks to interact with other AWS service. The standard service role has permissions on EC2, CloudWatch, ELB, and RDS. You can create a custom role for access on other services.
- EC2 Role: Another IAM role is to enables the instances in you stack to access various other services like s3 etc.  

From the OpsWorks Dashboard create a new Stack and within that add layers.
Within each layer you can add instances. 
Create an app and provide the location of source code destination. It could be a remote server, github or at S3. 
Within the layers tab, provide the location of cookbooks source and add recipes to be  run at various stages like deployment (or at undeploy , shutdown etc). 
Once these necessary configurations are over, deploy the application.

With these steps it should be able to deploy a simple application. I will be expanding this further with details in adding other elements of stack like ELB and database instance. Also on doing these remotely with SDK.


