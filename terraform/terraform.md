# Terraform notes

## Overview

3 Main commands:

* Refresh - Reconciles internal view of the infra with what's actually there.
* Plan - Figures out how to bring internal view in sync with real world
* Apply - Executes the plan


Also

* Destroy - decommission everything

## Terraform components

* Core
  * Monolith component
  * Takes the user defined TF and the actual system state and figures out the component graph and lifecycle management
  * Manages a single global **statefile** (global to ensure consistency)

* Providers
  * How Terraform talks out to the real world
  * Low level stuff like how to talk to Azure, GCP, AWS, or OpenStack
  * Also higher level stuff like intefacing with Kubernetes and Heroku, or Fastly or Datadog

These components also come into play in a team environment.

* Git server
  * Changes are pushed to git repo.

* Terraform Enterprise
  * Runs when changes are pushed to the git repo.
  * It's like the role Github plays in a team of developers.
  * Includes a private terraform Registry

* Modules
  * Reusable TF config that are parameterised and encapsulates some config.
  * System administrators will typically produce a catalogue of modules for use by internal development teams (here's how we do a java app, or c# app, etc)

* Terraform Registry
  * Hashicorp managed public registry
  * Cloud providers will typically publish modules and providers to capture best practices in using their platforms.
  
  
## Terraform Registry

location: registry.terraform.io

Seeing a provider source like this:

```
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0.2"
    }
```

`hashicorp/azurerm` translates to `registry.terraform.io/hashicorp/azurerm`
via Web it's https://registry.terraform.io/providers/hashicorp/azurerm/3.85.0


## Terraform DSL 


Example conf:

```
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0.2"
    }
  }

  required_version = ">= 1.1.0"
}
```
The `terraform` block contains general config for Terraform,
including defining the provider dependencies, whose `source`
identifies their path on the hashicorp registry (or other registries).


```
provider "azurerm" {
  features {}
}
```
Configures the provider. Can have multiple providers.


```
resource "azurerm_resource_group" "rg" {
  name     = "myTFResourceGroup"
  location = "australiaeast"
}
```

Configures the components of the infrastructure.

The string after resource is the id and has the syntax

`"{PROVIDER}_{TYPE}" "{NAME"`

The id of the resource block in this example is `azurerm_resource_group.rg`

The rest of the block contains arguments as defined by the provider.
The documentation can be looked up on the registry.



## Typical TF editing loop

```
terraform init     # do this once

terraform fmt      # style formatting + syntax validation

# alternatively
terraform validate

# deploy

terraform apply
```

`apply` will produce a local state file with the ids of the created resources.
You can view the state with `terraform state list`






  
## Terraform on Azure

IF not done already, create a Service Principal for the cli:

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<SUBSCRIPTION_ID>"
```

From the output, create the following file and call it creds.ps1. Note: do not give it extension .env

```
$Env:ARM_CLIENT_ID = "<APPID_VALUE>"
$Env:ARM_CLIENT_SECRET = "<PASSWORD_VALUE>"
$Env:ARM_SUBSCRIPTION_ID = "<SUBSCRIPTION_ID>"
$Env:ARM_TENANT_ID = "<TENANT_VALUE>"
```

