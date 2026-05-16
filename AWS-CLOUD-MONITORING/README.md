
AWS Security Monitoring Architecture
-----------------------------------------------------------------------------------------------------------------------------------------------------------

This architecture demonstrates a real-time cloud security monitoring and alerting pipeline built using Amazon Web Services services integrated with Splunk SIEM. AWS services such as Secrets Manager, S3, EC2, and IAM generate API activity logs that are captured by CloudTrail. The logs are stored in an S3 bucket and simultaneously forwarded to CloudWatch Logs for monitoring and detection. CloudWatch Metric Filters and Alarms are configured to detect suspicious or security-relevant activities. When a detection is triggered, SNS sends real-time email notifications and forwards messages to an SQS queue. Splunk consumes events from SQS using the Splunk Add-on for AWS, enabling centralized log analysis, alerting, and SOC monitoring workflows.


This project implements a real-time AWS security monitoring architecture using CloudTrail, CloudWatch, SNS, SQS, and Splunk SIEM integration. Security events from AWS services such as EC2, S3, IAM, and Secrets Manager are monitored using CloudWatch Metric Filters and Alarms. Alerts are delivered through SNS email notifications and forwarded to Splunk via SQS for centralized security monitoring and detection engineering.


## Architecture Overview

<div align="center">
  <img src="/assets/architecture.png" width="900">
</div>



The monitoring pipeline captures AWS API activities using CloudTrail and forwards logs to CloudWatch Logs and S3. CloudWatch Metric Filters detect specific security events and trigger alarms when suspicious activity occurs. SNS distributes notifications through email and SQS, while Splunk ingests events from SQS for centralized SIEM analysis and alerting.

### Services Used
- AWS CloudTrail
- AWS CloudWatch Logs
- AWS CloudWatch Alarms
- AWS SNS
- AWS SQS
- AWS S3
- Splunk SIEM

### Detection Workflow

AWS Service Activity → CloudTrail → CloudWatch Logs → Metric Filter → CloudWatch Alarm → SNS → Email & SQS → Splunk


