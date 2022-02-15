---
layout: post
published: false
title: Setting up tflint with IntelliJ
---
## Using tflint with IntelliJ

I've recently been working on a Terraform code base which has grown organically overtime and also been worked on by a number of different people.  This means there are naturally some aspects which can be improved upon.

Some really basic things which could be improved : 

* declare variable types
* give useful descriptions for variables
* remove unused variables (copy/paste from other places)
* basic formatting consistency (terraform fmt)

[tflint](https://github.com/terraform-linters/tflint) - as a tool can detect the first three with the relevant rules enabled.

What I wanted was this functionality available in the IDE I am using to edit the Terraform code as I can fix issues as I work on a specific module/environment.

## Setup instructions

1. Install tflint (I'm working on a Macbook)

```shell
brew install tflint
```

2. Ensure you have tflint setup correctly

I have my `tflint` config file in this location `~/tflint.hcl` and at the time of writing have the following rules enabled :

```shell
config {
  plugin_dir = "~/.tflint.d/plugins"
  disabled_by_default = false
}

rule "terraform_comment_syntax" {
  enabled = true
}

rule "terraform_deprecated_index" {
  enabled = true
}

rule "terraform_documented_outputs" {
   enabled = true
}

rule "terraform_documented_variables" {
  enabled = true
}

rule "terraform_naming_convention" {
  enabled = false
}

rule "terraform_required_providers" {
  enabled = true
}

rule "terraform_required_version" {
  enabled = true
}

rule "terraform_deprecated_interpolation" {
  enabled = true
}

rule "terraform_typed_variables" {
  enabled = true
}

rule "terraform_unused_declarations" {
  enabled = true
}

plugin "aws" {
    enabled = true
    version = "0.12.0"
    source  = "github.com/terraform-linters/tflint-ruleset-aws"
}
```

You can check it is working my manually running it against an example test.tf file with content below : 

```terraform
variable "test" {
  description = "test variable"
}
```

If you run the following command you should expect to see the warnings below if `tflint` is working correctly : `tflint test.tf`

```shell
➜  ~ tflint test.tf
2 issue(s) found:

Warning: `test` variable has no type (terraform_typed_variables)

  on /tmp/test.tf line 1:
   1: variable "test" {

Reference: https://github.com/terraform-linters/tflint/blob/v0.34.1/docs/rules/terraform_typed_variables.md

Warning: variable "test" is declared but not used (terraform_unused_declarations)

  on /tmp/test.tf line 1:
   1: variable "test" {

Reference: https://github.com/terraform-linters/tflint/blob/v0.34.1/docs/rules/terraform_unused_declarations.md
```

4. Install IntelliJ

Grab it from here [IntelliJ Download Page](https://www.jetbrains.com/idea/download/)

3. Install the HCL and LSP IntelliJ Plugins

* Navigate to `Preferences -> Plugins`
* Search for HCL and install the `Terraform and HCL` plugin
* Search for LSP and install the `LSP Support` plugin

Your installed plugins should look similar to the screen grab below.

![Plugins]({{site.baseurl}}/img/intellij-plugins.png)

4. Create an association for the language server

`Open Preferences -> Language & Frameworks -> Language Server Protocol -> Server Definitions`

Create an association as shown in the screenshot below :

![CreateAssociation]({{site.baseurl}}/img/create-association.png)

5. Restart IntelliJ

6. Load the same `test.tf` we used earlier and check we now get code highlighting of our issues without our IDE

![tflintInIntelliJ]({{site.baseurl}}/img/highlighting-tlfint-ide.png)


