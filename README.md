# Updates
Date of Release: 25th Jan 2019
Release No: V1.1

#### Additions
- Support for Ubuntu
- Checks the OS distributions and installs the Prerequistes packages on the server accrodingly.
- Parameterizes the frequency for exporting metrics. Default is every 5mins
- Disk Usage

# Introduction
This contains ansible playbooks to deploy / install the aws-cloudwatch-agent on VMs and set the cron job to export the metrics to cloudwatch.
The agent is required to capture the memory and disk metrics that are NOT available by default on Cloudwatch.
Minor changes to the perl script ( provided by AWS ) have been done to also export other memory metrics which I found quite useful. The metrics in additions to the base script provided by amazon are:
- Memory Utilized(in %)
- Memory Buffers(in MB)
- Memory Cached(in MB)
- Memory Used(in MB)
- Memory Free(in MB)
- Memory Available(in MB)
- Disk Usage (in GB)

### Terminology Used:
I would be using the following terminologies subsequently in my post.

Host Machine: This is the machine where you would be cloning the Github repository.

Target Machines: These are the machines which you want to monitor.

## Why did I create these playbooks? Why use custom metrics to monitor?

- Memory metrics are NOT provided by AWS CloudWatch by default and require an agent to be installed. I have automated the steps to install the agent and added a few features.

- The base script provided by Amazon didn’t output some metrics to be exported to CloudWatch like buffer memory and cached which didn’t give a clear picture of the about the memory. There were times when the free memory would indicate 1-2GB but the cached/buffer would be consuming that memory and NOT releasing it, thereby depriving your applications of memory.

- Installing the agent on each server and adding to the cron was a pain. Especially if you frequently create and destroy VMS. Why not just use Ansible to install it in one go to multiple servers?

## Supported OS Versions
- Amazon Linux 2
- Ubuntu

## Prerequistes
1. Ansible (I have used Ansible Version 2.7) to be installed on the Host Machine to deploy the scripts on the target machines/servers.

- To install ansible on Ubuntu you can run the following commands or follow this (https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-18-04)

```

$ sudo apt update

$ sudo apt install software-properties-common

$ sudo apt-add-repository ppa:ansible/ansible

$ sudo apt update

$ sudo apt install ansible
```

- On Amazon Linux 2 you just need to run the following commands - obviously there is no Digital Ocean Guide to follow

```

$ sudo yum-config-manager --enable epel

$ yum repolist ( you should see epel)

$ yum install ansible
```

2. Cloudwatch access to Amazon EC2. The ec2 instaces need to have access to push metrics to Cloudwatch. So you need to attach an IAM role ‘EC2AccessToCloudwatch’ to the target hosts you want to monitor. The other alternative is to export the keys to the servers. (Playbooks not updated for this option yet). I have used the IAM option which avoids the need to export keys to the server which can be a security concern. It is also difficult to rotate the credentials subsequently. To attach a role go to the Instance section of  the AWS Console. Select the instance > Click on Actions > Instance Settings > Attach Replace IAM Role.
3. SSH access to the target hosts i.e. the hosts where you want the agent installed, since ansible uses SSH to connect to managed hosts.

## How to run the playbooks ?
- Populate the hosts file with the hosts / IP's. A sample host file is present in the repo
- Run the main playbbok with inturn calls the role cloudwatch-agent
```
$ ansible-playbook -i hosts installer.yaml -vv
```

## What do the playbooks exactly do?
- Identifies the Distribution
- Installs the pre-requisites as per th OS flavour
- Installs the prerequisite packages based on the distribution
- Sets the cron job to fetch and export the metrics

## Packages installed on the target machines prior to running the scripts.
The following packages are installed by the playbooks as per the distribution.

### For Amazon Linux and Amazon Linux 2
The following packages are installed on Amazon Linux 2 server
- perl-Switch
- perl-DateTime
- perl-Sys-Syslog
- perl-LWP-Protocol-https
- perl-Digest-SHA.x86_64

### For Ubuntu
The following packages are installed on Ubuntu Server
- libwww-perl
- libdatetime-perl

## To manually verify the script is working

On the managed instance which you want to monitor, you can verify by manually navigating to the folder '/home/<user>/AWS_Scripts' and executing the perl script with the parameters as follows

```
$ ./mon-put-instance-data.pl --mem-util --mem-buffer --mem-cached --mem-used --mem-free --mem-avail  --disk-space-util --disk-path=/ --verbose --verify
```

It will fetch the following metrics
Memory Utilized(in %)
Memory Buffers(in MB)
Memory Cached(in MB)
Memory Used(in MB)
Memory Free(in MB)
Memory Available(in MB)
Disk Usage (in GB)
