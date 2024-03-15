---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: AWS Cli
layout: single
permalink: /aws/aws_cli/
sidebar:
   nav: "aws_nav"
---
## AWS CLI Commands
### S3 Buckets


{% highlight bash linenos %}
# Search S3 bucket for a file containing the string ${i}
$ echo ${i}
        aws s3api list-objects --bucket trust-power-consent-register-bucket-prod --prefix "live/-prod" --query "Contents[?contains(Key, '${i}')]" 

{% endhighlight %}

{% highlight bash linenos %}
# Find all files with a specific modified date.
DATE=$(date +%Y-%m-%d)
bucket=test-bucket-fh
aws s3api list-objects-v2 --bucket "$bucket" \
    --query 'Contents[?contains(LastModified, `'"$DATE"'`)]'

{% endhighlight %}

{% highlight bash linenos %}
# Find files after a certain date
SINCE=`date --date '-2 weeks +2 days' +%F 2>/dev/null || date -v '-2w' -v '+2d' +%F`
#      ^^^^ GNU style                                    ^^^^ BSD style
bucket=test-bucket-fh
aws s3api list-objects-v2 --bucket "$bucket" \
    --query 'Contents[?LastModified > `'"$SINCE"'`]'
{% endhighlight %}


{% highlight bash linenos %}
# Find files from certain date to current date
aws s3api list-objects-v2 --bucket BUCKET_NAME  --query 'Contents[?LastModified>=`YYYY-MM-DD`].Key'
{% endhighlight %}
