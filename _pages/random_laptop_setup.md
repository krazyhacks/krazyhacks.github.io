---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Laptop Setup
layout: single
permalink: /random_laptop_setup/
sidebar:
   nav: "random_nav"
---

## Laptop Setup 
### Reset to recovery mode
* Power off laptop
* Hold down CMD+R and power on, hold until screen
* Select wifi and enter password
* 
See [pip Installation](https://pip.pypa.io/en/stable/installing/).

{% highlight bash linenos %}$ echo "dummy text" {% endhighlight %}

* prepare directories 
{% highlight bash linenos %}
$ for i in deploy_props tf_logs tf_state_files
do
 mkdir -p stack/prv/atul/${i}
done
{% endhighlight %}


* download tf state files from each environment
{% highlight bash linenos %}
$ for i in 0-29-0 staging prod; do
	aws s3 cp s3://trust-power-prod/api/${i}.tfstate ./stack/prd/${i}/tf_state_files/${i}.tfstate.orig
done
{% endhighlight %}
* Store a copy of each state file in S3
{% highlight bash linenos %}
$ for i in 0-29-0 staging prod; do
	aws s3 cp s3://trust-power-prod/api/${i}.tfstate ./stack/prd/${i}/tf_state_files/${i}.tfstate.orig
done
{% endhighlight %}



* Terraform versions use by each stack

| Env |Stack | TF Version |Git Hash |
| ----: | :------- | ------------: | :----: |
|PRV | atul | 0.12.27 | 0f82fcad502fa4d0a2584671151617c562b4b1a0|
|PRV | 0-31-0 | 0.12.26 |0f82fcad502fa4d0a2584671151617c562b4b1a0|
|PRV | qa |  0.12.26| 0f82fcad502fa4d0a2584671151617c562b4b1a0|
|PRD | 0-29-0 | 0.12.27 | 0f82fcad502fa4d0a2584671151617c562b4b1a0|
|PRD | staging |0.12.27  | 0f82fcad502fa4d0a2584671151617c562b4b1a0|
|PRD | prod | 0.12.26 | 0f82fcad502fa4d0a2584671151617c562b4b1a0|


* Use `tfenv` to install and switch between teraform version
ATUL/CITEST
	cleanup
		manually delete s3 buckets, trust-power-user-pool-backup-citest, trust-power-dynamob-backup-citest
		undeploy.sh
		delete tfstate from s3 bucket
		remove local ./terraform/stage/prod/.teraform folder
	
	tfenv use 0.12.27
	deploy stack
		./build.sh
		DISAMBIGUATION_SUFFIX=-citest TF_VAR_user_pool_id=eu-west-1_QUia3jkak TF_VAR_user_pool_arn=arn:aws:cognito-idp:eu-west-1:730825514567:userpool/eu-west-1_QUia3jkak TF_VAR_user_pool_client_id=962pfftp6f4navdknqdjtc9sd TF_VAR_gateway_disambiguation_suffix=-preview ./deploy.sh prod trust-power-preview citest | tee ./stack/prv/citest/tf_logs/stack-citest-rel-0-31-1_TF0.12.27_`git rev-parse --short HEAD`_`date '+%Y-%m-%dT%H:%M:%S'`.log

	cp current-deployment.properties ./stack/prv/citest/tf_logs/current-deployment.properties_TF0.12.27_.`git rev-parse --short HEAD`.log
	aws s3 cp s3://trust-power-preview/api/${i}.tfstate ./stack/prv/${i}/tf_state_files/${i}.tfstate.`git rev-parse --short HEAD`	
	cp current-deployment.properties stack/prv/citest/deploy_props/0.31.x_TF0.12.26_`git rev-parse --short HEAD`.current-deployment.properties
	./plan_summary.sh <log_file>

	tfenv use 0.12.31
	git co -b release/0.32.x/eeeeee
	./build.sh
	deploy.sh



