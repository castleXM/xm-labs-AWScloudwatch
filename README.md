# AWS Cloudwatch
  Cloudwatch is an Amazon Web Service that is available when you spin up an EC2 instance. In order to configure this integration, you must sign up for an EC2 instance by going [Here](https://www.amazon.com/ap/signin?openid.assoc_handle=aws&openid.return_to=https%3A%2F%2Fsignin.aws.amazon.com%2Foauth%3Fresponse_type%3Dcode%26client_id%3Darn%253Aaws%253Aiam%253A%253A015428540659%253Auser%252Fiam%26redirect_uri%3Dhttps%253A%252F%252Fconsole.aws.amazon.com%252Fiam%252Fhome%253Fstate%253DhashArgs%252523%25252Fhome%2526isauthcode%253Dtrue%26noAuthCookie%3Dtrue&openid.mode=checkid_setup&openid.ns=http%3A%2F%2Fspecs.openid.net%2Fauth%2F2.0&openid.identity=http%3A%2F%2Fspecs.openid.net%2Fauth%2F2.0%2Fidentifier_select&openid.claimed_id=http%3A%2F%2Fspecs.openid.net%2Fauth%2F2.0%2Fidentifier_select&action=&disableCorpSignUp=&clientContext=&marketPlaceId=&poolName=&authCookies=&pageId=aws.ssop&siteState=registered%2Cen_US&accountStatusPolicy=P1&sso=&openid.pape.preferred_auth_policies=MultifactorPhysical&openid.pape.max_auth_age=120&openid.ns.pape=http%3A%2F%2Fspecs.openid.net%2Fextensions%2Fpape%2F1.0&server=%2Fap%2Fsignin%3Fie%3DUTF8&accountPoolAlias=&forceMobileApp=0&language=en_US&forceMobileLayout=0).
  
In this lab, I am using a **Amazon Linux AMI**


# Step 1: Configure Your IAM Role or User for CloudWatch Logs

The CloudWatch Logs agent supports IAM roles and users. If your instance already has an IAM role associated with it, make sure that you include the IAM policy below. If you don't already have an IAM role assigned to your instance, you can use your IAM credentials for the next steps or you can assign an IAM role to that instance. For more information, [see Attaching an IAM Role to an Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#attach-iam-role).

* To configure your IAM role or user for CloudWatch Logs

1. Open the IAM console at https://console.aws.amazon.com/iam/.

1. In the navigation pane, choose Roles.

1. Choose the role by selecting the role name (do not select the check box next to the name).

1. On the Permissions tab, expand Inline Policies and choose the link to create an inline policy.

1. On the Set Permissions page, choose Custom Policy, Select.

1. For more information about creating custom policies, see IAM Policies for Amazon EC2 in the Amazon EC2 User Guide for Linux Instances.

1. On the Review Policy page, for Policy Name, type a name for the policy.

1. For Policy Document, paste in the following policy:

``` {
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "logs:DescribeLogStreams"
    ],
      "Resource": [
        "arn:aws:logs:*:*:*"
    ]
  }
 ]
}
```


# Step 2: Configure a SNS Topic and Subscription

1. Click on the Amazon Simple Notification Service

![AWS SNS Logo](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/awsSNS.png?raw=true)

2. Create a topic


![AWS SNS Topic Logo](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/awsTopic.png?raw=true)

3.) Create a Subscription 


![AWS Subscription Logo](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/ansSubscription.png?raw=true)

* Make sure the end point is protocol is https
* Take the ARN from your previous topic and paste it into the ARN field
* In the endpoint field, grab the endpoint from the integration builder in "Inbound from SNS" in your AWS Cloudwatch Communication Plan.

![XM Comm Plan Outbound Logo](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/outboundXM.png?raw=true)




# Step 3: Install and Configure CloudWatch Logs on an Existing Amazon EC2 Instance

The process for installing the CloudWatch Logs agent differs depending on whether your Amazon EC2 instance is running Amazon Linux, Ubuntu, CentOS, or Red Hat. Use the steps appropriate for the version of Linux on your instance. However, remember, in this example we are using a **Amazon Linux instance**

To install and configure CloudWatch Logs on an existing Amazon Linux instance

Starting with Amazon Linux AMI 2014.09, the CloudWatch Logs agent is available as an RPM installation with the awslogs package. Earlier versions of Amazon Linux can access the awslogs package by updating their instance with the ```sudo yum update -y``` command. By installing the awslogs package as an RPM instead of the using the CloudWatch Logs installer, your instance receives regular package updates and patches from AWS without having to manually reinstall the CloudWatch Logs agent.

## Warning
Do not update the CloudWatch Logs agent using the RPM installation method if you previously used the Python script to install the agent. Doing so may cause configuration issues that prevent the CloudWatch Logs agent from sending your logs to CloudWatch.
1. Connect to your Amazon Linux instance. For more information, see Connect to Your Instance in the [Amazon EC2 User Guide for Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-connect-to-instance-linux).

If you have trouble connecting, see [Troubleshooting Connecting to Your Instance in the Amazon EC2 User Guide for Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html).

2. Update your Amazon Linux instance to pick up the latest changes in the package repositories.
* `sudo yum update -y`
* `sudo yum install -y awslogs`
* Edit the `/etc/awslogs/awscli.conf` file and in the [default] section, specify the region in which to view log data and add your credentials.
  ```
     region = us-east-1
     aws_access_key_id = <YOUR ACCESS KEY>
     aws_secret_access_key = <YOUR SECRET KEY> 
     ```
3. Install apache web service by running ` sudo yum install httpd`. One Apache is installed, start the service by running `service httpd start`
4. Next, we will need to configure which logs we want to track
* We will do this by downloading the following script `wget https://s3.amazonaws.com/aws-cloudwatch/downloads/awslogs-agent-setup-v1.0.py`
* And then running the following `sudo python ./awslogs-agent-setup-v1.0.py --region us-east-1`
* When prompt, input your AWS Access Key ID
* When prompt, input your AWS Secret Access Key
* You can leave the default region name blank
* You can leave the default output format blank
* When prompt for the Log stream name, enter option 3 and type "Apache Error Logs"
* Chose a timestamp for your output
* One the configuration is complete you should run `sudo service awslogs restart`

![Script logo](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/pythonScript.png?raw=true)

5. Head over to AWS Cloudwatch
![AWS CloudWatch Logo](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/cloudWatchAWS.png?raw=true)

6 *Click on Logs*
![AWS Logs logo](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/awsLogsImage.png?raw=true)

7.) You should see your apache logs 
![AWS Apache Logs Logo](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/logImage.png?raw=true)

# Step 4: Configure a Metric in the Logs
1. After your logs are being sent to CloudWatch, In the CloudWatch console click on *Logs*
2. Find your Apache logs
3. Click on *Create Metric Filter*
4. In the Filter Pattern type `shutting down`
5. Define the Metric

![Define Metric](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/defineMetric.png?raw=true)


6. Name the filter `shutting-down`
7. Name the Metric `apacheShutDown`

![Assign Metric](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/assignMetric.png?raw=true)



# Step 5: Configure a Alarm in CloudWatch

1. Go back to the CloudWatch Console
2. Click on *Alarm*
3. Click on *Create Alarm*
4. Name the alarm `apache is down`
5. Description `Apache Web Service has stopped`
6. Whenever: apacheShutDown is ` >=1 ` for ` 1 ` consecutive period
7. Whenever this alarm: `State is ALARM`
8. Send notification to `Send_to_XM`

![AWS Alarm](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/alarm.png?raw=true)


# Step 6: Stop Apache

1. Log into your EC2 instance
2. Type the following command ` sudo service httpd stop`
3. Your Alarm should be triggered in Cloudwatch

![Alarm State](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/alarmState.png?raw=true)

4. You should also get an alert from xMatters telling you Apache has stopped

![xM Email](https://github.com/mpeters413/AWScloudwatch-docker/blob/master/e-mail%20alert.png?raw=true)



