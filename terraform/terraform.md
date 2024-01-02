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
  * Cloud providers will typically publish modules to capture best practices in using their platforms.