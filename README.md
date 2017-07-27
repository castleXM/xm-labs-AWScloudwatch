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

# Step 2: Install and Configure CloudWatch Logs on an Existing Amazon EC2 Instance

The process for installing the CloudWatch Logs agent differs depending on whether your Amazon EC2 instance is running Amazon Linux, Ubuntu, CentOS, or Red Hat. Use the steps appropriate for the version of Linux on your instance. However, remember, in this example we are using a **Amazon Linux instance**

To install and configure CloudWatch Logs on an existing Amazon Linux instance

Starting with Amazon Linux AMI 2014.09, the CloudWatch Logs agent is available as an RPM installation with the awslogs package. Earlier versions of Amazon Linux can access the awslogs package by updating their instance with the ```sudo yum update -y``` command. By installing the awslogs package as an RPM instead of the using the CloudWatch Logs installer, your instance receives regular package updates and patches from AWS without having to manually reinstall the CloudWatch Logs agent.

## Warning
Do not update the CloudWatch Logs agent using the RPM installation method if you previously used the Python script to install the agent. Doing so may cause configuration issues that prevent the CloudWatch Logs agent from sending your logs to CloudWatch.
1. Connect to your Amazon Linux instance. For more information, see Connect to Your Instance in the [Amazon EC2 User Guide for Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-connect-to-instance-linux).

If you have trouble connecting, see [Troubleshooting Connecting to Your Instance in the Amazon EC2 User Guide for Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html).

1. Update your Amazon Linux instance to pick up the latest changes in the package repositories.
* `sudo yum update -y`
* `sudo yum install -y awslogs`
* ```region = us-east-1
aws_access_key_id = <YOUR ACCESS KEY>
aws_secret_access_key = <YOUR SECRET KEY>```
