---
layout: post
title: "Managing AWS Access With IAM"
date: 2015-03-12T00:00:00+08:00
tags: cloud aws
---
When working with AWS the first thing to look into is how to secure your access to aws resources. Basically authentication, access policies and restrictions. Since this is a cloud environment, you have to be circumspect of everything from security perspective and double check each of them. Anything that is not secured will be broken into. 

The good thing is that AWS provides easy and comprehensive security service with IAM. It comes at no additional cost and should be used to secure your resources at AWS. 

### IAM Features
Some of the major features of IAM are
- Centralised control of your AWS environment
- Shared access to AWS account
- Granular permissions
- Multifactor authentication
- Identify Federation (including Active Directory, Facebook, Linkedin etc)
- Allows to setup up own password rotation policy

### Key Concepts
Some key concepts to understand while working with IAM are Users, Groups, Roles and Policies.

#### Users 
Users are the people to whom you want to provide secured access to AWS resources. May be developers or admin users.

#### Groups
Groups are collection of users under one set of permission. Like Admin group, developer group etc where developers may have rights to administer over RDS,EC2 etc but not IAM.

#### Roles
Roles are access rights you assign to aws resources in order for them to act on behalf of you. Eg the ec2 instance that you created could be given a role for s3 access inorder to read and write to your s3 directories without explicity providing passwords. The other option is store the access credentials on ec2 machine and each time it could authenticate with it. This is unsafe and not recommended. Instead use roles for this purpose. Roles can be assigned to users as well.

#### Policies
Policies are JSON documents your write to described the permissions. You apply policies to users, groups and role. Policy documents can be shared between resources. Using policies you can specify several layers of granularity. First, you can define specific AWS service actions you wish to allow or explicitly deny access to. Second, depending on the action, you can define specific AWS resources the actions can be performed on. Third, you can define conditions to specify when the policy is in effect. For example, if MFA is enabled or not. 


### Root account
When you create an AWS account, the email account with which you have created the account is the root account. It has all the privileged rights and is the most powerful account. You should enable MFA( multifactor authentication) on this account to make it very secure.
Also this root account should not be used for routine administration. Instead, you should create a IAM user for yourself with admin rights and then in future use that account for administrating AWS resources and users.

 
When you create a new user under IAM, by default he has no permissions. You have to grant them the permission by assigning them to a group. You can create groups and attach suitable policies to the group and then assign user to the group.

IAM is global access under your account. Means that users cannot be restricted based on region or you cannot define separate quotas of resource consumption at user level. 

### Federated Access
You can allow users to access your AWS resources using their existing credentials from active directory, linkedin etc. Federated users have full access to your AWS console and can also have API access to connect programatically. You can set restrictions on session durations etc to control the access. 

### Workflow
To keep a tab on overall resource consumption, you can create different development and production accounts. This way there would total isolation between each.

### Some useful FAQ

Q: Can a Role be added to running instance?
A role is assigned to an EC2 instance when you launch it. It cannot be assigned to an instance that is already running. If you need to add a role to an instance that is already running, you can create an image of the instance, and then launch a new instance from the image with the desired role assigned.

Q: Can I associate more than one IAM role with an EC2 instance?
No. You can only associate one IAM role with an EC2 instance at this time.

Q: What happens if I delete an IAM role that is associated with a running EC2 instance?
Any application running on the instance that is using the role will be denied access immediately.

Q: Can I control which IAM roles an IAM user can associate with an EC2 instance?
Yes
You can use the PassRole permission to restrict which role a user can pass to an EC2 instance when the user launches the instance. This helps prevent the user from running applications that have more permissions than the user has been granted—that is, from being able to obtain elevated privileges. 

Q: Can I grant permissions to access AWS resources owned by another AWS account?
Yes. Using IAM roles, IAM users and federated users can access resources in another AWS account via the AWS Management Console, the AWS CLI, or the APIs. 



