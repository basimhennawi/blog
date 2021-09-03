In this article I'm going to share with you how I dealt with a use-case where I wanted to have a big screen displaying a dashboard **24/7** from **CloudWatch** showing couple of widgets (i.e. EC2 CPU, Mongo CPU, ECS requests, ...) nothing more!

I created a new IAM user with only `CloudWatchReadOnlyAccess` policy attached directly to it, then bought me a Raspberry Pi, connected it to the screen, had OS installed, opened a browser, logged in to the **AWS Console** with this newly IAM user created, then opened **CloudWatch** service then hit on **Dashboards** from the side-menu, then choose the dashboard I want (`MainDashboard` as shown in the screenshot below), made the browser window full screen... that's it!

![MainDashboard.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1629574576874/0V7-Ah_2d.png)

I wrongly thought that's it, as ONE hour later I found myself logged out and the screen looks like this:

![AWSConsoleLoginScreen.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1629574960947/OGtWrqPfK.png)

Okay, seems to be session is timed out! Few minutes later after looking for some settings that could prolong this period, found out the following: 

> 
For security purpose, a login session will expire in 12 hours when you sign into the AWS Management Console with your AWS or IAM account credentials. To resume your work after the session expires, we ask you to click the "Click login to continue" button and login again. The duration of federated sessions varies depending on the federation API (GetFederationToken or AssumeRole) and the administrator’s preference.

More about it here in [AWS FAQ](https://aws.amazon.com/console/faq-console/#session_expire).

Of course if I just wanted to extend the maximum session duration for an IAM role to up to 12 hours, I could do it easily using the IAM console or CLI as explained here: https://aws.amazon.com/blogs/security/enable-federated-api-access-to-your-aws-resources-for-up-to-12-hours-using-iam-roles/

But I wanted "continuously" session for AWS console...

So here is my "over-engineered" solution:

I wrote a Node.js script; scraping the AWS Console page (using `puppeteer` npm package) to login and navigate to the desired CloudWatch dashboard and made the browser window full screen along with a cronjob (using `cron` npm package) runs every specific time (for example 11 hours) and run this on Pi.

Check it out here on Github: https://github.com/basimhennawi/aws-prolong-session

Also, make sure this generally speaking against securities best practices, so if you are planning to do so make sure the IAM User got ONLY read-only permissions.

UPDATE:

There seems to be the feature for that since September 2020:

https://aws.amazon.com/about-aws/whats-new/2020/09/amazon-cloudwatch-dashboards-supports-sharing/

So you can share CloudWatch Dashboards in **three ways**:

- Share a single dashboard and designate specific email addresses and passwords of the people who can view the dashboard.

- Share a single dashboard publicly, so that anyone who has the link can view the dashboard.

- Share all the CloudWatch dashboards in your account and specify a third-party single sign-on (SSO) provider for dashboard access. All users who are members of this SSO provider's list can access the dashboards in the account. To enable this, you integrate the SSO provider with Amazon Cognito.   

For the first **two ways**, there is no limited session expiry time. 

https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-dashboard-sharing.html

That's it! and if you have a feedback for me, please drop it below in the comments!
