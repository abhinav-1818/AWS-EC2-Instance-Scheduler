## AWS EC2 Instance Scheduler

Automated EC2 Start/Stop Using AWS Lambda, Amazon EventBridge Scheduler, IAM, and CloudWatch Logs.

## Project Overview

The AWS EC2 Instance Scheduler is a cloud automation project that starts and stops Amazon EC2 instances on a predefined schedule.

The project uses AWS Lambda with Python and Boto3 to manage EC2 instances tagged with `AutoSchedule=true`. Amazon EventBridge Scheduler invokes the Lambda function with a `start` or `stop` action, while IAM permissions control access to EC2 resources.

## Architecture

![AWS EC2 Instance Scheduler Architecture](architecture/ec2-scheduler-architecture.png)

### Workflow

1. EventBridge Scheduler triggers the Lambda function at the configured time.
2. Lambda receives an event containing the `start` or `stop` action.
3. Lambda finds EC2 instances tagged `AutoSchedule=true`.
4. The IAM execution role authorizes the permitted EC2 operations.
5. Lambda starts or stops the selected instances.
6. Lambda execution logs are available in Amazon CloudWatch Logs.

## AWS Services Used

| Service | Purpose |
|---|---|
| Amazon EC2 | Compute instances managed by the scheduler |
| AWS Lambda | Executes the automation using Python and Boto3 |
| Amazon EventBridge Scheduler | Triggers Lambda at scheduled times |
| AWS IAM | Controls Lambda's permissions to manage EC2 |
| Amazon CloudWatch Logs | Stores Lambda execution logs for monitoring and troubleshooting |

## Project Configuration

### EC2 Instance

- Instance name: `scheduled-ec2-server`
- Tag key: `AutoSchedule`
- Tag value: `true`

Only instances matching the configured tag are intended to be managed by the automation.

### Lambda Function

- Function name: `ec2-instance-scheduler`
- Runtime: Python (use the actual runtime configured in AWS)
- SDK: Boto3

The function reads the incoming action, identifies tagged EC2 instances, and calls the appropriate EC2 API.

### IAM Role and Policy

- Execution role: `ec2-scheduler-lambda-role`
- Policy: `EC2InstanceSchedulerPolicy`

Permissions used:

- `ec2:DescribeInstances`
- `ec2:StartInstances`
- `ec2:StopInstances`

The start/stop permissions are restricted using the `AutoSchedule=true` resource-tag condition. The execution role also needs permissions to write Lambda logs to CloudWatch.

### EventBridge Schedules

**Start schedule:** `ec2-start-schedule`

Input:

```json
{
  "action": "start"
}
```

**Stop schedule:** `ec2-stop-schedule`

Input:

```json
{
  "action": "stop"
}
```

Configure the desired schedule times and timezone in EventBridge Scheduler.

## Testing and Validation

The project was tested using start and stop actions.

- Start test: EC2 state changed from Stopped to Running.
- Stop test: EC2 state changed from Running to Stopped.
- CloudWatch Logs: Used to inspect Lambda execution details and troubleshoot errors.

Screenshots of the actual test results are included in the `screenshots/` directory.

## Repository Structure

aws-ec2-instance-scheduler/
│
├── README.md
├── lambda/
│   └── ec2_instance_scheduler.py
│
├── iam/
│   └── ec2-instance-scheduler-policy.json
│
├── architecture/
│   └── ec2-scheduler-architecture.png
│
├── screenshots/
│   ├── 01-ec2-instance.png
│   ├── 02-autoschedule-tag.png
│   ├── 03-lambda-function.png
│   ├── 04-iam-policy.png
│   ├── 05-start-schedule.png
│   ├── 06-stop-schedule.png
│   ├── 07-start-test.png
│   ├── 08-stop-test.png
│   └── 09-cloudwatch-logs.png
│
└── docs/
    └── project-documentation.pdf

## Key Learnings

- Automating EC2 lifecycle operations with Lambda and Boto3
- Creating scheduled triggers with EventBridge Scheduler
- Using EC2 tags to select resources
- Applying IAM permissions and resource-tag conditions
- Reviewing Lambda execution logs in CloudWatch
- Reducing unnecessary EC2 running time through scheduling

## Cost Management

Scheduled stopping can reduce EC2 compute charges when instances are not needed. Other resources and storage may continue to incur charges.

After testing, unnecessary resources were stopped or deleted to control costs. The project is documented so the architecture and configuration can be recreated.

## Future Improvements

- Add structured logging and better error handling.
- Add handling for paginated EC2 `DescribeInstances` responses.
- Add notifications for failed executions using CloudWatch alarms and Amazon SNS.
- Recreate the infrastructure using Terraform.
- Improve scheduling configuration for multiple environments.

## Author

Created as a hands-on AWS learning project to practise cloud automation, IAM, serverless computing, monitoring, and cost optimization.
