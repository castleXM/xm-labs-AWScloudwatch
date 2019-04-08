# AWS CloudWatch
  CloudWatch is an Amazon Web Service for monitoring the AWS resources and the customer applications running on the Amazon infrastructure.
  

---------

<kbd>
  <img src="https://github.com/xmatters/xMatters-Labs/raw/master/media/disclaimer.png">
</kbd>

---------


# Prerequisites 
* xMatters account - If you don't have one, [get one!](https://www.xmatters.com/)
* An AWS Account - If you don't have one, [get one!](https://portal.aws.amazon.com/billing/signup#/start)

# Files
* [AWSCloudWatch.zip](AWSCloudWatch.zip) - The Comm Plan containing the forms and integration scripts


# How it works
When a CloudWatch Alarm condition is met, an Simple Notification Service (SNS) message is published and picked up by the SNS subscription. The subscription is tied to a Topic, which generates a webhook to the xMatters inbound integration. 

# Installation

## xMatters


## Import and Configure the xMatters Communication Plan

The next step is to import the communication plan.

To import the communication plan:

1. Login to xMatters as a Developer and create a new user.
1. Create a new REST user. See details [here](https://help.xmatters.com/integrations/xmatters/configuringxmatters.htm#Create)
1. Import the [AWSCloudWatch.zip](AWSCloudWatch.zip) communications plan.
1. Next to the **AWS - CloudWatch** comm plan, click Edit > Access Permissions and give access to the user created in step 2.
1. Click Edit > Forms and next to the **CloudWatch Alarm** form, click Edit > Sender Permissions and give access to the user created in step 2.
1. Navigate to the Integration Builder tab and expand the Inbound Integrations section. Click on **Inbound from SNS**  and copy the URL at the bottom.


## AWS

### Step 1: Configure an SNS Topic and Subscription

1. Click on the Amazon Simple Notification Service

<kbd>
   <img src="media/awsSNS.png" width="550">
</kbd>

2. Create a topic


<kbd>
   <img src="media/awsTopic.png" width="550">
</kbd>

3.) Create a Subscription 


<kbd>
   <img src="media/snsSubscription.png" width="550">
</kbd>

* Make sure the endpoint protocol is https
* Take the ARN from the topic above and paste it into the ARN field
* In the endpoint field, use the endpoint from the **Inbound from SNS** inbound integration builder integration in your AWS Cloudwatch Communication Plan.

### Step 2: Configure an Alarm in CloudWatch

1. Go back to the CloudWatch Console
2. Click on *Alarm*
3. Click on *Create Alarm*
4. Give the alarm a good name and descriptive description
5. Set the "Whenever" information to the criteria you need to monitor. For example, `CPUUtilization > 0.9 for 4 out of 5 datapoints`. 
7. Whenever this alarm: `State is ALARM`
8. Send notification to `Send_to_XM`

<kbd>
   <img src="media/alarm.png" width="550">
</kbd>


# Testing

Spike the CPU on an ec2 instance, or otherwise trigger the criteria in the `Whenever` section of the alarm. Then, check the Activity Stream in the **Inbound from SNS** inbound integration. There should be a new entry and a resulting event generated, targeting the recipients set in the Form Layout. 

# Troubleshooting

The first place to check is the inbound activity stream. If there is no entry here, the SNS topic failed to fire so investigate the `Whenever` condition and verify the condition was triggered and verify the SNS topic is correctly set up. If there is an entry in the activity stream, but no event, the investigate the activity stream for more details. There will be an error message on why the event wasn't generated. 

If the event was generated but no notification was received, check the recent events report for the event generated to see who was notified. Then verify the correct recipients. 


