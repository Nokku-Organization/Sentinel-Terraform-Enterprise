# Sentinel-Terraform-Enterprise
Sentinel-Terraform-Enterprise is for managing terraform-enterprise by making restrictions via policies

Terraform Enterprise is our self-hosted distribution of Terraform Cloud. It offers enterprises a private instance of the Terraform Cloud application, with no resource limits and with additional enterprise-grade architectural features like audit logging and SAML single sign-on.

Sentinel is an embeddable policy as code framework to enable fine-grained, logic-based policy decisions that can be extended to source external information to make decisions

Sentinel gives operations teams the governance capabilities they need to ensure that all infrastructure provisioned with Terraform Enterprise complies with their organization's provisioning rules. 

## How to write Sentinel-policies :
  ### a)Sentinel files/file-structure in Github:
  There are two types of files : ".sentinel" , ".hcl"
  - In <name>.sentinel files, we need to place our policy-code
  - In <name>.hcl file, we need to mention which policy need to be applied and which level.
    There are three type of levels: 
    - hard-mandatory requires that the policy passes. If a policy fails, the run is halted and may not be applied until the failure is resolved.
    - soft-mandatory is much like hard-mandatory, but allows an administrator to override policy failures on a case-by-case basis.
    - advisory will never interrupt the run, and instead will only surface policy failures as informational to the user.
  
  
  ### b)What is sentinel-policy
  ### c)How to write a policy
  ### d)Example explanation of a sentinel-policy code

## How to associate Sentinel-policies with Terraform-Enterprise:

## How to test sentinel-policies locally: 
  ### How to download sentinel-policy-simulator locally:
