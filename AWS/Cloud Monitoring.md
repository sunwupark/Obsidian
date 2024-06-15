## CloudWatch 
- EC2 instances: CPU Utilization, Status Checks, Network
- EBS volumes: Disk Read/Writes
- S3 buckets: BucketSizeBytes, NumberofObjects, AllRequests
- Biling
- Service Limits

![[스크린샷 2024-06-15 오후 5.10.36.png]]

## Amazon CloudWatch Alarams
- Alarms are used to trigger notifications for any metric

## Amazon CloudWatch Logs
- ECS, Lambda, CloudWatch Log agaents on EC2
- Route53
- real-time monitoring of logs


## Amazon EventBridge
- Schedule: Cron jobs(scheduled scripts)
	- ![[Pasted image 20240615171410.png]]
- Event Pattern: Event rules to react to a service doing something
	- ![[Pasted image 20240615171422.png]]

## Aws CloudTrail
- Provides governance, compilance and audit for your AWS Account
- get history of events / API calls made within your AWS Account by
	- Console
	- SDK
	- CLI
	- AWS Services

![[Pasted image 20240615171909.png]]

## AWS X-Ray
- Test locally
- log statements everywhere
- Re-deploy in production
![[Pasted image 20240615172014.png]]

- Advantages
	- Troubleshooting Performance
	- Understand depedencies in a msa
	- pinpoint service issue
	- review request behavior

## Amazon CodeGuru
- ML-powered service for automated code reviews and application performance recommendations

### Provides two functionalities
• CodeGuru Reviewer: automated code reviews for static code analysis (development)
- Identify critical issues, security vulnerabilities, and hard-to-find bugs
- Example: common coding best practices, resource leaks, security detection, input validation

• CodeGuru Profiler: visibility/recommendations about application performance during runtime (production)
- Helps understand the runtime behavior of your application
- Example: identify if your application is consuming excessive CPU capacity on a logging routine

![[Pasted image 20240615172150.png]]

## AWS Health Dashboard
- Shows all regions, all services health
- Shows historical information for each day

