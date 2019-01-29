# Updates
Date of Release: 25th Jan 2019
Release No: V1.1
Additions
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

## Supported OS Versions
- Amazon Linux 2
- Ubuntu

## Prerequistes
1. Ansible (Ansible Version = 2.7) to be installed on the Host Machine to deploy the scripts on the target machines/servers.
2. Cloudwatch access to Amazon EC2. The ec2 instaces need to have access to push metrics to Cloudwatch. So an IAM role is created and attached to the instances.
3. SSH access to the tacget hosts ie the hosts where you want the agent installed, since ansible uses SSH to connect to managed hosts.

To export the meterics to Cloudwatch from EC2, AWS EC2 would require to access to export metrics to AWS Cloudwatch.
This can be done in 2 ways:

####Option 1:

Attach a role to the Instances allowing EC2 Service access to Cloudwatch Service (Reccomended).

####Option 2: 

The other alternative is to export the keys to the servers. (Playbooks not updated for this option yet)  

I have used Option 1 which avoids the need to export keys to the server which can be a security concern. It is also difficult to rotate the credentials subsequently.

## Why are these metrics required?
Although 'Used Memory' maybe just 30-40%, there are times when the buffered/ cached memory is held back and not released, hence depriving your applications of memory.

## What do the playbooks exactly do?
- Identifies the Distribution
- Installs the pre-requisites as per th OS flavour
- Clones the repo which is using the aws-cloud-agent script to fetch metrics from the servers.
- Sets the cron in root user

## Pre-requisite installations done on the instances / servers
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

## How to run the playbooks ?
- Populate the hosts file with the hosts / IP's. A sample host file is provided in the
file roles/cloudwatch-agent/tests/inventory


## To manually verify the script is working

On the managed instance which you want to monitor, you can verify by manually running the following command

`$ ./mon-put-instance-data.pl --mem-util --mem-buffer --mem-cached --mem-used --mem-free --mem-avail  --disk-space-util --disk-path=/ --verbose --verify`

It will fetch the following metrics
Memory Utilized(in %)
Memory Buffers(in MB)
Memory Cached(in MB)
Memory Used(in MB)
Memory Free(in MB)
Memory Available(in MB)
Disk Usage (in GB)
