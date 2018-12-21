# aws-cloudwatch
This contains ansible playbooks to deploy / install the aws-cloudwatch-agent on Amazon VMs.

Also additions to the base script provided by amazon
- Cached Memory
- Buffer Memory
Although used memory maybe just 60%, there are times when the buffered/ cached memory is held back and not released, hence depriving your applications of memory.


This is using the aws-cloud-agent script  to fetch metrics from the servers.
