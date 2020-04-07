# Sentinel-Terraform-Enterprise
Sentinel-Terraform-Enterprise is for managing terraform-enterprise by making restrictions via policies

Terraform Enterprise is our self-hosted distribution of Terraform Cloud. It offers enterprises a private instance of the Terraform Cloud application, with no resource limits and with additional enterprise-grade architectural features like audit logging and SAML single sign-on.

Sentinel is an embeddable policy as code framework to enable fine-grained, logic-based policy decisions that can be extended to source external information to make decisions

Sentinel gives operations teams the governance capabilities they need to ensure that all infrastructure provisioned with Terraform Enterprise complies with their organization's provisioning rules. 

## How to write Sentinel-policies :
  ### a)Sentinel files/file-structure in Github:
  There are two types of files : ".sentinel" , "sentinel.hcl"
  - In <name>.sentinel files, we need to place our policy-code
  - In sentinel.hcl file, we need to mention which policy need to be applied and which level.
    There are three type of levels: 
    - hard-mandatory requires that the policy passes. If a policy fails, the run is halted and may not be applied until the failure is resolved.
    - soft-mandatory is much like hard-mandatory, but allows an administrator to override policy failures on a case-by-case basis.
    - advisory will never interrupt the run, and instead will only surface policy failures as informational to the user.
  - Refer this sample-example for better understanding structure: [Example](https://github.com/Nokku-Organization/Sentinel-Terraform-Enterprise/tree/master/Example)
  
  
  ### b)What is sentinel-policy:
  
  ### c)How to write a policy:
  
  Sentinel expects there to be a main rule. The value of this rule is the result of the entire policy.

If the result of main is true, the policy passes. If the value is anything else (false or a non-boolean value), the policy fails. The exact meaning of what happens a policy passes or fails is dependent on the host system


Sentinel policies are executed top-down

The example below is about the simplest practical example of Sentinel. It is reasonable to imagine this as a realistic policy. This shows that in most cases, Sentinel will be extremely simple:

value = 10
main = value > 5
or
main = rule { request.method is "GET" and request.headers contains "X-Key" }


Rules form the basis of a policy by representing behavior that is either passing or failing (true or false). Rules are a first class language construct in Sentinel. A policy can and should be broken down into rules to aid with readability, testability, and performance.

An example usage of rules is shown below:

is_sunny     = rule { weather is "sunny" }
is_wednesday = rule { day is "wednesday" }
main = rule { is_sunny and is_wednesday }


  
  There are 4 imports we make use of.
  1)tfplan- most commonly used:
  tfplan

    
    ├── module() (function)
    │   └── (module namespace)
    │       ├── path ([]string)
    │       ├── data
    │       │   └── TYPE.NAME[NUMBER]
    │       │       ├── applied (map of keys)
    │       │       └── diff
    │       │           └── KEY
    │       │               ├── computed (bool)
    │       │               ├── new (string)
    │       │               └── old (string)
    │       └── resources
    │           └── TYPE.NAME[NUMBER]
    │               ├── applied (map of keys)
    │               ├── destroy (bool)
    │               ├── requires_new (bool)
    │               └── diff
    │                   └── KEY
    │                       ├── computed (bool)
    │                       ├── new (string)
    │                       └── old (string)
    ├── module_paths ([][]string)
    ├── terraform_version (string)
    ├── variables (map of keys)
    │
    ├── data (root module alias)
    ├── path (root module alias)
    ├── resources (root module alias)
    │
    ├── config (tfconfig namespace alias)
    └── state (tfstate import alias)
    
     
  ### d)Example explanation of a sentinel-policy code

## How to associate Sentinel-policies with Terraform-Enterprise:

## How to test sentinel-policies locally: 
  ### How to download sentinel-policy-simulator locally:
