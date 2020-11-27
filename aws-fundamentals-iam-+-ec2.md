---
title: "AWS Fundamentals: IAM + EC2"
tags: ""
---

# AWS Regions

-   Regions all around the world
-   Names can be: us-east-1, eu-west-3
-   A region is a cluster of data centers
-   Most AWS services are region-scoped
      

# AWS Availability Zones

-   Each region has many availability zones (usually 3, min is 2, max is 6). Example:
-   AWS Region
-   Sydney: ap-southeast-2
    -   ap-southeast-2a
    -   ap-southeast-2b
    -   ap-southeast-2c
-   Each availability zone (AZ) is one or more discrete data centers with redundant power, networking, and connectivity
-   The're separate from each other, so that they're isolated from disasters
-   They're connected with high bandwidth, ultra-low latency networking
      

# IAM Introduction

-   IAM (Identity and Access Management)
-   Your whole AWS security security is there:
    -   USERS
    -   GROUPS
    -   ROLES
-   Root account should never be used (and shared)
-   Users must be created with proper permissions
-   IAM is at the center of AWS
-   Policies are written in JSON 
-   Has a _global_ view
-   Permissions are governed by Policies (JSON)
-   MFA (Multi Factor Authentication) can be setup
-   IAM has predefined "managed policies"
-   It's best to give users the minimal amount of permissions they need to perform their job (least privilege principles)

# IAM Federation

-   Big enterprises usually integrate their own repository of users with IAM
-   This way, one can login into AWS using their company credentials
-   Identity Federation uses the SAML standard (Active Directory)

# IAM 101 Brain Dump

-   One IAM **User** per PHYSICAL PERSON
-   One IAM **Role** per Application
-   IAM Credentials should NEVER BE SHARED
-   NEVER write IAM Credentials in code. EVER.
-   **Never use the ROOT acount except for initial setup.**
-   **Never use ROOT IAM Credentials.**

# What is EC2?

-   EC2 is one of the most popular of AWS offerings
-   It mainly consists in the capability of:
    -   Renting virtual machines (EC2)
    -   Storing data on virtual drives (EBS)
    -   Distributing load across machines (ELB)
    -   Scaling the services using an auto-scaling group (ASG)
-   Knowing EC2 is fundamental to understand how the Cloud works

# SSH Summary Table

|                 | SSH | PUTTY | EC2 INSTANCE CONNECT |
| --------------- | :-: | :---: | :------------------: |
| Mac             |  X  |       |           X          |
| Linux           |  X  |       |           X          |
| Windows &lt; 10 |     |   X   |           X          |
| Windows >= 10   |  X  |   X   |           X          |

# How to SSH into your EC2 Instance - Linux / Mac OS X

-   SSH is one of the most important functions. It allows you to control a remote machine, all using the command line.
-   chmod 0400 EC2Tutorial.pem
    -   this is the file you saved when you created your ec2
-   ssh -i EC2Tutorial.pem ec2-user@whateveryouripis
    -   run this after and it should work

# Security Groups Deeper Dive

-   Security groups are acting as a "firewall" on EC2 instances
-   They regulate:
    -   Access to Ports
    -   Authorised IP ranges - IPv4 and IPv6
    -   Control of inbound network (from other to the instance)
    -   Control of outbound network (from the instance to other)

# Security Groups Good to know

-   Can be attached to multiple instances
-   Locked down to a region / VPC combination
-   Does live "outside" the EC2 - if traffic is blocked, the EC2 instance won't see it
-   It's good to maintain one separate security group for SSH access
-   If your application is not accessible (time out), then it's a security group issue
-   If your application gives a "connection refused" error, then it's an application error or it's not launched
-   All inbound traffic is **blocked** by default

# Private vs Public IP (IPv4)

-   Networking has two sorts of IPs: IPv4 IPv6
    -   IPv4: 1.160.10.240
    -   IPv6: 3ffe: 1900:4545:3:200:f8ff:fe21:67cf
-   In this course, we will only be using IPv4
    -   Most common format used online
    -   allows for 3.7 billion diff addresses in public space
    -   IPv4: [0-255].[0-255].[0-255].[0-255]
-   IPv6 is newer and solves problems for IoT

# Private vs Public IP (IPv4) Fundamental Differences

-   Public IP:
    -   Public IP means the machine can be identified on the internet (WWW)
    -   Must be unique across the whole web (not two machines can have the same public IP).
    -   Can be geo-located easily
-   Private IP:
    -   Private IP means the machine can only be identified on a private network only
    -   The IP must be unique across the private network
    -   BUT two different private networks (two companies) can have the same IPs
    -   Machines connect to WWW using a NAT+ internet gateway (a proxy)

# Elastic IPs

-   When you stop and start an EC2 instance, it can change its public IP
-   If you need to have a fixed public IP for your instance, you need an Elastic IP
-   An Elastic IP is a public IPv4 IP you own as long as you don't delete it
-   You can atach it to one instance at a time
-   With an Elastic IP address you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account.
-   You can only have 5 Elastic IP in your account ( you can ask AWS to increase that).
-   Overall, **try to avoid using Elastic IP**
    -   They often reflect poor architectural decisions
    -   Instead, use a random public IP and register a DNS name to it
    -   Or, as we'll see later, use a Load Balancer and don't use a public IP

# Private vs Public IP (IPv4) in AWS EC2 - Hands On

-   By default, your EC2 machine comes with:
    -   A private IP for the internal AWS Network
    -   A public IP for the WWW
-   When we are doing SSH into our EC2 machines:
    -   We can't use a private IP, because we are not in the same network
    -   We can only use the public IP
-   If your machine is stopped and then started, **the public IP can change**
