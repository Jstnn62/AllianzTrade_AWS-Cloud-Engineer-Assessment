#Question 1
What are the weaknesses of the current GitLab architecture from a resilience perspective?

GitLab is running on one EC2 instance 
Users --> GitLab EC2, If this instance fails CI/CD pipelines stop and developers cannot push or pull code ,GitLab UI becomes unavailable.

EBS Storage Tied to One Instance ,projects and artifacts are stored on EBS volumes which attached to a specific EC2 instance.

Lack of Load Balancing, there is no load balancer in front of GitLab. So no traffic distributio ,no failover routing ,no health check mechanism.

-----------------------------------------------------------------------------------------------

#Question 2
What target architecture would improve resilience?

A more resilient architecture should introduce:

1.Load balancing

2.Multiple GitLab nodes

3.Shared storage

4.Automated scaling

-----------------------------------------------------------------------------------------------

#Question 3
What monitoring practices should be implemented?

1.Infrastructure

2.Application

3.User experience

-----------------------------------------------------------------------------------------------

#Question 4
How would you automate the GitLab runbook (upgrades, configuration, etc.)?
Manual operations should be replaced with Infrastructure as Code and automation.

Infrastructure Automation
Configuration Management
Automated Upgrades
Backup Automation
Self-Healing Mechanisms


