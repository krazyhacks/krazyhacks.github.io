---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: AWS Boto
layout: single
permalink: /aws/aws_boto/
sidebar:
   nav: "aws_nav"
---
## AWS Boto/Boto3
### Exceptions 

#### Find available list of exceptions for python
{% highlight python linenos %}
import botocore
[e for e in dir(botocore.exceptions) if e.endswith('Error')]
{% endhighlight %}

{% highlight python linenos %}
import boto3
client = boto3.client('scheduler')
[e for e in dir(client.exceptions) if e.endswith('Exception')]

{% endhighlight %}


#### List exceptions for evert AWS service
{% highlight python linenos %}
import boto3, pprint

region_name = 'eu-west-1'
session = boto3.Session(region_name=region_name)
exceptions = {
  service: list(boto3.client(service).exceptions._code_to_exception)
  for service in session.get_available_services() 
}
pprint.pprint(exceptions, width=20000)
{% endhighlight %}

#### Use specific exception in python
{% highlight python linenos%}
import boto3

self.client = boto3.client("scheduler", region_name="eu-west0-1")

try:
    # do something
except self.client.exceptions.ResourceNotFoundException
    # print something
{% endhighlight %}
