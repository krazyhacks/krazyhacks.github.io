---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Terraform
layout: single
permalink: /random_terraform/
sidebar:
   nav: "terraform_nav"
---

## Terraform 
### Terraform section 
See [pip Installation](https://pip.pypa.io/en/stable/installing/).


### AWS profiles in terraform
Instead of hardcoding AWS credentials in terraform try this to reference
locally stored credentials;
{% highlight bash linenos %}
provider "aws" {
  region = "region"
  shared_credentials_file = "$HOME/.aws/credentials # default
  profile = "default" # you may change to desired profile
}

terraform {
  backend "s3" {
    profile = "default" # change to desired profile
    # Replace this with your bucket name!
    bucket         = "great-name-terraform-state-2"
    key            = "global/s3/terraform.tfstate"
    region         = "eu-central-1"
    # Replace this with your DynamoDB table name!
    dynamodb_table = "great-name-locks-2"
    encrypt        = true
  }
}
{% endhighlight %}

Notes: terraform error
{% highlight bash linenos %}
Error: Provider configuration not present

To work with
module.main.module.lambdas.module.data-transformation-lambda.aws_s3_bucket.junk_bucket
(orphan) its original provider configuration at
module.main.module.lambdas.module.data-transformation-lambda.provider["registry.terraform.io/hashicorp/aws"]
is required, but it has been removed. This occurs when a provider
configuration is removed while objects created by that provider still exist in
the state. Re-add the provider configuration to destroy
module.main.module.lambdas.module.data-transformation-lambda.aws_s3_bucket.junk_bucket
(orphan), after which you can remove the provider configuration again.

{% endhighlight %}
Remove specific resource (this is NOT a recommended best practice), sometimes required as a get out of jail.
{% highlight bash linenos %}
terraform destroy -target module.main.module.lambdas.module.data-transformation-lambda.aws_s3_bucket.junk_bucket 
{% endhighlight %}
[How to evolve terraform setup](https://www.hashicorp.com/resources/evolving-infrastructure-terraform-opencredo)
[Testing Terraform](https://medium.com/contino-engineering/terraform-infrastructure-as-code-testing-best-practice-unit-tests-bdd-end-to-end-scenario-c30d5a6921d)
[How to test IaC](https://winder.ai/how-to-test-terraform-infrastructure-code/)
[TerraTest](https://octopus.com/blog/testing-terraform-code)
[Gruntworks](https://terratest.gruntwork.io/)
[How to clean up TF state](https://faun.pub/cleaning-up-a-terraform-state-file-the-right-way-ab509f6e47f3)
[How to deploy multiple stack github actions](https://acidtango.com/thelemoncrunch/how-to-deploy-multiple-branches-with-terraform-and-github-actions/)
