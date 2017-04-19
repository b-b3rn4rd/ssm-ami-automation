# ssm-ami-automation
Automated AMI creation using SSM

Recently AWS announced the induction of SNS topic to keep the community informed about Amazon AMI releases. Following solution integrates this feature with Amazon EC2 Systems Manager to provide Amazon native solution to build AMIs.

Workflow
---------

![ssm automation](http://bigm-and-pie.com/wp-content/uploads/2017/04/ssm.png)

1. The  lambda function is subscribed to the official SNS topic
`arn:aws:sns:us-east-1:137112412989:amazon-linux-ami-updates`
2. The SSM automation document is triggered with the latest AMI  when a new SNS notification is received
3. Automation document launches a new EC2 instance and runs required configuration commands.
4. Once the configuration process has completed, SSM stops the instance and creates a new AMI. When the AMI is ready,  automation terminates the stopped instance.
5. CloudWatch Event gets notified when the SSM has finished or failed the execution. It sends a message to the SNS topic which is subscribed by the notification email.

Creating stack
-------------

1. Upload all cloudformation templates except `main.yml` and `sns-subscription.yml` to the s3 bucket
2. Run `main.yml` in the desired region and provide s3 bucket name where cloudformations were uploaded
3. Run `sns-subscription` in the us-east-1 region and specify lambda ARN  from `SSMLambdaARN` output created in step 2
