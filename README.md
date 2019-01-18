# aws-cloudwatch
# Read Me

This contains ansible playbooks to deploy / install the aws-cloudwatch-agent on Amazon VMs.

Also additions to the base script provided by amazon
- Memory Utilized(in %)
- Memory Buffers(in MB)
- Memory Cached(in MB)
- Memory Used(in MB)
- Memory Free(in MB)
- Memory Available(in MB)
Although used memory maybe just 30-40%, there are times when the buffered/ cached memory is held back and not released, hence depriving your applications of memory.


This is using the aws-cloud-agent script  to fetch metrics from the servers.

Pre-requisites

For Amazon Linux and Amazon Linux 2
# Install the following packages
$ sudo yum install -y perl-Switch perl-DateTime perl-Sys-Syslog perl-LWP-Protocol-https perl-Digest-SHA.x86_64

# Verify the script is working
# It will fetch the following metrics
# Memory Utilized(in %)
# Memory Buffers(in MB)
# Memory Cached(in MB)
# Memory Used(in MB)
# Memory Free(in MB)
# Memory Available(in MB)
$ ./mon-put-instance-data.pl --mem-util --mem-buffer --mem-cached --mem-used --mem-free --mem-avail --verbose --verify
