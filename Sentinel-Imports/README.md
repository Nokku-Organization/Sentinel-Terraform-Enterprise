## Explanation On Sentinel-Imports:

Sentinel Policies for Terraform are defined using the Sentinel policy language. A policy can include imports which enable a policy to access reusable libraries, external data and functions. Terraform Cloud provides four imports to define policy rules for the plan, configuration, state, and run associated with a policy check.

### tfplan - 
   This provides access to a Terraform plan, the file created as a result of terraform plan. The plan represents the changes that Terraform needs to make to infrastructure to reach the desired state represented by the configuration.
### tfconfig - 
   This provides access to a Terraform configuration, the set of "tf" files that are used to describe the desired infrastructure state.
### tfstate -
   This provides access to the Terraform state, the file used by Terraform to map real world resources to your configuration.
### tfrun - 
   This provides access to data associated with a run in Terraform Cloud, such as the run's workspace.


#### Terraform Cloud allows you to create mocks of these imports from plans for use with the mocking or testing features of the Sentinel CLI. For more information, see Mocking Terraform Sentinel Data in the folder [here](https://github.com/Nokku-Organization/Sentinel-Terraform-Enterprise/tree/master/Sentinel-Imports/mocks).

NOTE: For additional Info , refer [here](https://www.terraform.io/docs/cloud/sentinel/import/index.html)

