---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: AWS Cloudwatch
layout: single
permalink: /aws/aws_cloudwatch_insights/
sidebar:
   nav: "aws_nav"
---
## AWS Cloudwwatch Insights 
### Insights query with statistics 


{% highlight bash linenos %}$ echo "dummy text" {% endhighlight %}


{% highlight bash linenos %}

fields @timestamp, @message
| filter @message like /(?i)(Exception|error|fail|Timed out)/
# | filter @message not like "Location over the sea"
# | filter @message not like "solarGenerationPowerAtEnd"
# | filter @message not like "SearchParameterSource.getParameters"
# | filter @message not like "must be after the value given for"
# | filter @message not like "Connection reset by peer"
# | filter @message not like "Connect to api.worldweatheronline.com:443"
# | filter @message not like "There are overlapping usage intervals"
# | filter @message not like "There is a negative 'energy' in 'usage'"
# | filter @message not like "at least one month I need without coefficients for all weather codes"
# | filter @message not like "There is more than one point with"

# ignore because there are multiple output lines for the same error
| filter @message not like "UserPostcodeMessageLambda - java.lang.NullPointerException"
| filter @message not like "SocketTimeoutException"
| filter @message not like "ERROR UserPostcodeMessageLambda - com.google.gson.JsonSyntaxException"
| filter @message not like "ERROR UserPostcodeMessageLambda - com.fasterxml.jackson.databind.exc.MismatchedInputException"
| filter @message not like "PVGISResponseParser.parseResponse"


| stats 
sum(strcontains(@message, "There is more than one point with"))/count(*) * 100 as DuplicateWWOData, 
sum(strcontains(@message, "Location over the sea"))/count(*) * 100 as OverTheSea, 
sum(strcontains(@message, "There are overlapping usage intervals"))/count(*) * 100 as OverlappingUsage,
sum(strcontains(@message, "There is a negative 'energy' in 'usage'"))/count(*) * 100 as NegativeEnergy,
sum(strcontains(@message, "at least one month I need without coefficients for all weather codes"))/count(*) * 100 as MissingModelData,
sum(strcontains(@message, "solarGenerationPowerAtEnd is null"))/count(*) * 100 as SolarPowerAtEndNull,
sum(strcontains(@message, "SearchParameterSource.getParameters"))/count(*) * 100 as NullPointerInSearchParameterSource,
sum(strcontains(@message, "must be after the value given for"))/count(*) * 100 as ValueGivenForEndMustBeAfterStart,
sum(strcontains(@message, "Connection reset by peer"))/count(*) * 100 as WWOConnectionReset,
sum(strcontains(@message, "Connect to api.worldweatheronline.com:443"))/count(*) * 100 as WWOTimeout

{% endhighlight %}

### Cloudwatch Metrics
Display DynamoDB consumed Writes, hence are we being throttled
Just need to easily identify the name of metrics [..See here for DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/metrics-dimensions.html)
Also checkout Lambda, [AWS Batch, Step Function](https://docs.aws.amazon.com/step-functions/latest/dg/procedure-cw-metrics.html)
{% highlight bash linenos  %}
{
    "metrics": [
        [ "AWS/DynamoDB", "ConsumedWriteCapacityUnits", "TableName", "TableA-prod", { "id": "m1", "yAxis": "left" } ],
        [ "...", "TableA-staging", { "id": "m2" } ],
        [ "...", "TableA-qa", { "id": "m3" } ]
    ],
    "title": "Write usage (average units/second)",
    "view": "timeSeries",
    "stacked": false,
    "region": "eu-west-1",
    "stat": "Sum",
    "period": 900,
    "yAxis": {
        "left": {
            "showUnits": false
        }
    }
}
{% endhighlight %}
{% highlight bash linenos  %}
{
    "metrics": [
        [ "AWS/DynamoDB", "SuccessfulRequestLatency", "TableName", "TableA-qa", "Operation", "DeleteItem", { "yAxis": "right", "period": 900 } ],
        [ "...", "TableA-prod", ".", ".", { "yAxis": "right" } ],
        [ "...", "TableA-staging", ".", ".", { "yAxis": "right" } ],
        [ ".", "ReturnedItemCount", ".", "TableA-0-29-0", ".", "Query" ],
        [ "...", "TableA-prod", ".", "." ],
        [ "...", "TableA-staging", ".", "." ]
    ],
    "view": "timeSeries",
    "stacked": false,
    "region": "eu-west-1",
    "stat": "Average",
    "period": 300
}

{% endhighlight %}
