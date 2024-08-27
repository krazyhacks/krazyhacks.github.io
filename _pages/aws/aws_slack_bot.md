---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: AWS Slack Bote
layout: single
permalink: /aws_slack_bot/
sidebar:
   nav: "aws_nav"
---

## AWS Slack Bot 
### Setup
- From aws console, navigate to AWS ChatBot
- Its a global service, not region specific.
- Configure new client -> slack
- Redirects you to slack sign-in page.  Enter slack domainname and sign in with email
- Slack will request permission for AWS to access slack channels/contents - accept
- AWS Chatbot should now creates a workspace
- Configure new channel -> 
- Configuration Name = Preview_Slack
- Slack Channel = public
- Public Channel Name (search drop down) = monitoring-preview (need to create in slack beforehand)
- Create Channel IAM role using template
- Role name = Preview-AWS-ChatBot
- Policy Templates = "Notification permissions"
- Guard Rail Policies = AmazonChimeReadOnly
- SNS Topics, Region = eu-west-1
- SNS Topic = "monitoring-alerts"  <<--- Who/What created this?
- Save
- May get warning service is not subscribed to => click subscribe.




{% highlight bash linenos %}$ echo "dummy text" {% endhighlight %}
