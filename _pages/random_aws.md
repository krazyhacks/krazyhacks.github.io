---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: AWS
layout: single
permalink: /random_aws/
sidebar:
   nav: "aws_nav"
---

## AWS 
### AWS section 
See [pip Installation](https://pip.pypa.io/en/stable/installing/).
## AWS Cloudwatch Alarm
1. AWS -> Cloudwatch
2. Select check-box against log group that needs an alarm
3. Actions -> "Create Metric Alarm"
### Define Pattern
1. Filter Pattern = "ERROR"
2. Next
### Assign metric
1. Filter name = "preview/SmartMeter/ERROR" or "preview/smart-meter-api-metric/error"
2. MetricNameSpace = "smartmeter/preview
3. MetricName = "ERROR"
3. Metric Value = 1
4. Default = "BLANK"
5. Unit = "count"

Review -> Create/Assign Pattern

### Create Alarm
1. AWS -> Cloudwatch -> logs
2. Select log group
3. Metric Filter Tab -> Check-box to select -> Create Alarm

### Specify Metirc Conditions
Metric
1. Metric Name = "ERROR"
2. Statistics = "Sum"
3. Period = "5 minutes"
Conditions
1. Threshold = "Static"
2. Whenever = "Greater than or Equal"
3. than = "1"
Additional
Datapoints to Alarm => 1 out of 1
Missing data treatment  => as Good

### Notification
1. Alarm state trigger = "In ALARM"
2. Select an existing SNS topic = "monitoring-alerts"   (SlackCloudWatch)
Next
1. Alarm Name = "API-smart-meter-Errors"
2. Alarm Description = "Manually added"

Create Alarm

{% highlight bash linenos %}$ echo "dummy text" {% endhighlight %}
