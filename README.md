# Read Me

This contains ansible playbooks to deploy / install the aws-cloudwatch-agent on VMs and set the cron job to export the metrics to cloudwatch.
The agent is required to capture the memory and disk metrics that are NOT available by default on Cloudwatch.
Minor changes to the perl script ( provided by AWS ) have been done to also export other memory metrics which I found quite useful. The metrics in additions to the base script provided by amazon are:
- Memory Utilized(in %)
- Memory Buffers(in MB)
- Memory Cached(in MB)
- Memory Used(in MB)
- Memory Free(in MB)
- Memory Available(in MB)

# Supported OS Versions
- Amazon Linux 2
- Ubuntu 18 (Currently Unavailable)
- Ubuntu 16 (Currently Unavailable)

# Prerequistes
To export the meterics to Cloudwatch from EC2, AWS EC2 would require to access to export metrics to AWS Cloudwatch.
This can be given in 2 ways:
1. Attach a role to the Instances allowing EC2 Service access to Cloudwatch Service (Reccomended).
2. The other alternative is to export the keys to the servers. (Playbooks not updated for this option yet)  

I have used Option 1 which avoids the need to export keys to the server which can be a security concern. It is also difficult to rotate the credentials subsequently.

# Why are these metrics required?
Although 'Used Memory' maybe just 30-40%, there are times when the buffered/ cached memory is held back and not released, hence depriving your applications of memory.

# What do the playbooks exactly do?
- Identifies the OS
- Installs the pre-requisites as per th OS flavour
- Clones the repo which is using the aws-cloud-agent script to fetch metrics from the servers.
- Sets the cron in root user

# Pre-requisite installations done on the instances / servers
The following packages are installed by the playbooks as per the OS flavour.

For Amazon Linux and Amazon Linux 2

$ sudo yum install -y perl-Switch perl-DateTime perl-Sys-Syslog perl-LWP-Protocol-https perl-Digest-SHA.x86_64

For Ubuntu 18 (Currently Unavailable)


# To manually verify the script is working
It will fetch the following metrics
Memory Utilized(in %)
Memory Buffers(in MB)
Memory Cached(in MB)
Memory Used(in MB)
Memory Free(in MB)
Memory Available(in MB)
On the managed instance which you want to monitor, you can verify by manually installing the 
$ ./mon-put-instance-data.pl --mem-util --mem-buffer --mem-cached --mem-used --mem-free --mem-avail --verbose --verify
