# notify

notify is a simple wrapper script intended to capture errors in a cron job and send a notification via SNS.

# Setup

## Create SNS Topic

```
aws sns create-topic --name notify
```

## Create SNS Subscription

```
aws sns subscribe --topic-arn ${topic-arn} --protocol email --notification-endpoint ${email-address}
```

Check email and click verification link.

## IAM User

### Create User

```
aws iam create-user --user-name notify
```

### Generate AWS Access Key

```
aws iam create-access-key --user-name notify 
```

### Create IAM Managed Policy

```
aws iam create-policy --policy-name notify --description "SNS Publish access for notify" \
--policy-document '{
   "Version": "2012-10-17",
   "Statement": [
       {
           "Sid": "SNSPublishAccess",
           "Effect": "Allow",
           "Action": "sns:Publish",
           "Resource": "arn:aws:sns:us-east-1:349342608844:notify"
       }
   ]
}'
```

### Attach IAM Managed Policy to User

```
aws iam attach-user-policy --user-name notify --policy-arn ${iam-managed-policy-ARN}
```

# Usage

The following is an example cron job where the AWS region and key pairs have been defined via environment variables in the crontab.
This example will not result in an SNS notification.

```
* * * * * export REGION="us-east-1"; export AWS_ACCESS_KEY_ID="AKIDQ"; export AWS_SECRET_ACCESS_KEY="c3cm"; /home/aboutte/notify echo "hello world"
```

The following example will result in a failure and an SNS notification will be generated:

```
* * * * * export REGION="us-east-1"; export AWS_ACCESS_KEY_ID="AKIDQ"; export AWS_SECRET_ACCESS_KEY="c3cm"; /home/aboutte/notify non_existent_command
```

The following is an example email:

![email](https://github.com/aboutte/notify/blob/master/assests/email.png "email")
