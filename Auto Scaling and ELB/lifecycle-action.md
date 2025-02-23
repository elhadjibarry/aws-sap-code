
1. Create an SNS Topic

aws sns create-topic --name my-lifecycle-topic

2. Create a lifecycle hook

aws autoscaling put-lifecycle-hook --auto-scaling-group-name ASG1 --lifecycle-hook-name my-lifecycle-hook --lifecycle-transition autoscaling:EC2_INSTANCE_TERMINATING --notification-target-arn arn:aws:sns:us-east-1:291934546285:my-lifecycle-topic --role-arn arn:aws:iam::291934546285:role/aws-service-role/autoscaling.amazonaws.com/AWSServiceRoleForAutoScaling --heartbeat-timeout 300

3. Create a role that can be assumed by Lambda with the permissions in the iam-permissions-policy-lambda.json file

4. Create a Lambda function with the code in the lambda-lifecycle-action.py file

5. Subscribe the function to the topic

aws sns subscribe --protocol lambda --topic-arn arn:aws:sns:us-east-1:291934546285:my-lifecycle-topic --notification-endpoint arn:aws:lambda:us-east-1:291934546285:function:lambda-lifecycle

6. Give the SNS topic the permission to invoke the Lambda
aws lambda add-permission --function-name lambda-lifecycle --source-arn arn:aws:sns:us-east-1:291934546285:my-lifecycle-topic --statement-id function-with-sns --action "lambda:InvokeFunction" --principal sns.amazonaws.com

7. Test the lifecycle hook by terminating an EC2 instance (change the desired capacity to 0)

