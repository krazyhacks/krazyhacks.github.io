---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Postman
layout: single
permalink: /random_postman/
sidebar:
   nav: "random_nav"
---

## Postman 
### Postman Limitations 

Currently Postman has no easy way of writing a reusable library of tests which makes maintenance a pain.
Requests are best organised as folders, in order to use the `postman.setNextRequest(<req_name>)` function, the target `req_name` has to be in the same folder as the folder being run under the postman runner.  You can not (yet) reference a sub-folder of tests to be run as unit. 
If another test B is called from test A, execution will not resume from the caller A, you'd have to duplicate lots of requests/code/tests to create different scenarios.
Test collections can be data driven using external csv or json data files.

### Postman section 
See [Configure Postman for Github Actions](https://learning.postman.com/docs/integrations/available-integrations/ci-integrations/github-actions/#configuring-the-postman-cli-for-github-actions).

* Form postman UI, create collections, tests under a folder structure
  * One collection per micro-service
  * One collection per system level testsuite
  * Each collection to use top level folders, `Sanity/Smoke` ,`Integration`,  `Functional`
  * Different test stages can call tests at a folder level
* Export collection as a json file
* Export environment as a json file
* Export globals as a json file
* Run collection using newman running in a docker container (postman/newman)
* Customize docker image to be built with htmlextra plugin for newman

Run Collection in GitHub
* create repo for testing
* create Dockerfile, github actions file
	* trigger, build, test, notify
	* Get terraform to generate an plan output file
	* Review output file - commiting file to trigger deployment
	* Figure out how to get github actions to send notify to slack channel
* Install github plugin in slack
	* create CICD channel
	* Subscribe to repo

* Run test using local docker, using;
```
docker run -v ${PWD}/collections/:/etc/newman -t microservices/api-tests run "Loop_with_bearer.postman_collection.json" --environment "prv-qa.postman_environment.json" --globals "Loop.postman_globals.json" --folder "(5) LoadDashboard - After Onboarded" -r cli,htmlextra --reporter-htmlextra-export ./foo.html --reporter-htmlextra-title "Foo"
```	

Notify Slack channel
{% highlight bash linenos %}
{code name=.codecov.yml}
coverage:
  status:
    project:
      default:
        target: 75%
        threshold: 2%
  notify:
    slack:
      default:
        url: "https://hooks.slack.com/services/T9xxxxQKE/Bxxxxx1B8F/nw7ICsuZyj7hxxxxxlzgjNea"

{code}
{% endhighlight %}

{% highlight bash linenos %}$ echo "dummy text" {% endhighlight %}

#### TODO
* Slack /command to trigger github jobs
* Report results to slack channel
* bash script to drive docker container runs
* manual trigger - inputs to drive workflows with overrides
* caching docker container to speed up tests
* Clear cache, [see](https://stackoverflow.com/questions/63521430/clear-cache-in-github-actions)
* Share data between [workflows and jobs](https://servreality.com/news/how-to-share-data-between-tasks-in-github-actions-examples/)
* See [here](https://blog.postman.com/automation-tricks-for-newman/) for tips and tricks - wish i'd found it earlier


#### CI/CD

Run tests on a micro-service(s) tests updated, PR
Run tests on a micro-service manually, i.e. invoke via github console
Run tests at system level when code merged into environment branch
Run tests on a cron job

* youtube [video](https://www.youtube.com/watch?v=OPwU3UWCxhw)
* how to trigger workflow using [external call](https://www.youtube.com/watch?v=84kUf9ycr9A&t=616s)
* Trigger circleCI job from [github](https://circleci.com/blog/trigger-circleci-pipeline-github-action/)
* One workflow yml file per micro-service, system test

* Understand if/when to use workflow_dispatch & workflow_call

#### Tests
* Smoke tests per microservice
	Check the service is present, accessible, auth, expected endpoints present
* Integration tests to exercise across microservices
	Check microservice endpoints,
** Thirdparty APIs
* System test to exercise end to end user journeys

#### Reports

* csv
* allure
* html
* junit
* progress
* htmlextra (via plugin)

##### HTML Reports
When using htmlextra, sometimes you just want to get the summary on the command line without
loading the html (they can be big), hence only open if you have errors.

{% highlight bash linenos %}
cat htmlextra-report.html | grep -A12  "Click to view the Summary" | grep "<a " | sed 's/^.*false">//; s/<\/span.*$//; s/<.*>//'
{% endhighlight %}

##### Allure Reports

Within each Postman request add comments under the `tests` to serve as cucumber tags, as follows;
{% highlight linenos %}

// Id
// @allure.id=228

// Label
// @allure.label.{{labelName}}={{labelValue}}

// Story
// @allure.label.story=storyName

// Suite
// @allure.label.suite=suiteName

// Owner
// @allure.label.owner=ownerName

// Tag
// @allure.label.tag=tagName
// Followed by the javascript test.

pm.test("Status code is 200", function () {
  pm.response.to.be.ok;
});

{% endhighlight %}

* Run newman with the allure report enabled, this will output txt/json files, by including the arguments;
{% highlight linenos %}
 -r allure --reporter-allure-export <allure-results-out-dir>
{% endhighlight %}

* Generate an Allure Report, this will take the txt/json files as an input
{% highlight linenos %}
allure serve <allure-results-out-dir> # this will generate and serve up a report in a temp directory which gets deleted. 
allure generate --clean <allure-results-out-dir> # Creates and generates/serves a report from a folder named `allure-report`  
{% endhighlight %}

* To get Allure to generate/report a Trend, subsequent report generations must first copy the history into the next test run.
* Run newman with allure enabled to generate initial test results
* Generate the initial report, hence,  under `allure-report` a `history` folder is created.
* Run newman again
* Copy the contents of `allure-report/history` to `<allure-results-out-dir>/history/`
* Generate report for second run, this will amalgamate the new test results with the previous history, rinse and repeat to build up a trend.

Documentation is a bit wooly/vague, the above steps appear to work. Script it and/or add it to github actions.

TODO: Add it to github actions. Add the `passing|failing` tag message in github, publish it as a webpage in github
Also take a look at [Mocha](https://qaplusone.hashnode.dev/generating-test-reports-with-history-using-allure-and-mocha)
{% highlight linenos %}

{% endhighlight %}

* [See](https://github.com/allure-framework/allure-js/blob/master/packages/newman-reporter-allure/README.md)
* [See it in github actions](https://github.com/marketplace/actions/allure-report-with-history)
#### Using Newman with the Postman Pro API:

* Generate an API key
* Fetch a list of your collections from:
```
https://api.getpostman.com/collections?apikey=$apiKey
```
* Get the collection link via its UID:
``` 
https://api.getpostman.com/collections/$uid?apikey=$apiKey
```
* Obtain the environment URI from:
```
https://api.getpostman.com/environments?apikey=$apiKey
```
* Using the collection and environment URIs acquired in steps 3 and 4, run the collection as follows:
```
newman run "https://api.getpostman.com/collections/$uid?apikey=$apiKey" \
    --environment "https://api.getpostman.com/environments/$uid?apikey=$apiKey"
```


#### Run newman in AWS EC2

* From AWS Console, Launch an EC2 instance using ubuntu AMI
* enable ssh port 22
* allow traffic from your IP address only to port 22
* Create ssh key/pem file
* Login to instance
  * update pkg list, will fail to find usefull stuff without it
```
sudo apt update
```
  * Install docker 
```
sudo apt install docker.io
```
* check docker is working `sudo docker run hello-world`
* mkdir -p prv-qa-cicd/collections/results/prv-qa/
* copy over files to EC2 instance; Dockerfile, collection.json, env.json, globals.json, run.sh
```
scp -i ~/.ssh/ssh-key.pem <local_file> ubuntu@ec2-3-252-223-217.eu-west-1.compute.amazonaws.com:/home/ubuntu/prv-qa-cicd/collections/
```
* Build docker images;
```
sudo docker build -t newman/bulk_account_creation .
```
* download results file;
```
scp -i ~/.ssh/ssh-key.pem ubuntu@ec2-3-252-223-217.eu-west-1.compute.amazonaws.com:/home/ubuntu/prv-qa-cicd/collections/<remote_file> .
```

##### Run multiple runs of newman using screen
* Create a new screen session
```
$ screen -S NEWMAN
```
* Use `CTRL-a + c` to create a window, one per newman run, up to 10? named 0..9
* Common screen commands;

```
Ctrl+? help.
Ctrl+a c Create a new window (with shell).
Ctrl+a " List all windows.
Ctrl+a 0 Switch to window 0 (by number).
Ctrl+a A Rename the current window.
Ctrl+a S Split current region horizontally into two regions.
Ctrl+a | Split current region vertically into two regions.
Ctrl+a tab Switch the input focus to the next region.
Ctrl+a Ctrl+a Toggle between the current and previous windows
Ctrl+a Q Close all regions but the current one.
Ctrl+a X Close the current region.

Ctrl+a d detach from session
screen -ls  list sessions
screen -r re-attach to running session, assumes only one currently running
```

[How to use postman for AWS](https://www.postman.com/api-evangelist/workspace/amazon-web-services-aws/request/35240-62d3eeb0-a95d-4472-a705-cd03e1b005be)
