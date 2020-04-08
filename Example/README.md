## Explanation of Example Sentinel-Policy(Restricting EC2 instance instance creation based on its Instance-Type):

Step by Step , we can see how this ec2-restrict-sentinel policy is written:
## Step-1:
At First Main rule will be Executed.Depending on the main-rule, policy will be passed or failed.
If Main-rule is evaluates to boolean "true" then the policy is passed whereas if boolean is "false" or any other value , then policy will be failed.
##### code-snippet-for-main-rule:
    # Main rule
    main = rule {
      instances_validated
    }
    
Here in our example, main-rule is calling "validate_attribute_in_the_list" function via "instances_validated".
So, If "validate_attribute_in_the_list" function return "true",our policy will be passed otherwise our policy will be failed.
##### code-snippet:
    # Call the validation function
    instances_validated = validate_attribute_in_list("aws_instance",
                          "instance_type", allowed_types)




### Step-2:

Lets first look into the function as whole and then lets subdivide.
Then function motive is to obtain resources-list-info of all aws_instances from terraform-plan and compare these planned-instances-instanceType with allowed instance types , thereby returning boolean false (if planning instance-InstanceType is not allowed in allowed-Instance-type) otherwise boolean true.

##### code-snippet-for-"validate_attribute_in_list"-function:
        # Validate that all instances of a specified resource type being modified have
        # a specified top-level attribute in a given list
        validate_attribute_in_list = func(type, attribute, allowed_values) {

          validated = true

          # Get all resource instances of the specified type
          resource_instances = find_resources_from_plan(type)

          # Loop through the resource instances
          for resource_instances as address, r {

            # Skip resource instances that are being destroyed
            # to avoid unnecessary policy violations.
            # Used to be: if length(r.diff) == 0
            if r.destroy and not r.requires_new {
              print("Skipping resource", address, "that is being destroyed.")
              continue
            }

            # Determine if the attribute is computed
            if r.diff[attribute].computed else false is true {
              print("Resource", address, "has attribute", attribute,
                    "that is computed.")
              # If you want computed values to cause the policy to fail,
              # uncomment the next line.
              # validated = false
            } else {
              # Validate that each instance has allowed value
              if r.applied[attribute] else "" not in allowed_values {
                print("Resource", address, "has attribute", attribute, "with value",
                      r.applied[attribute] else "",
                      "that is not in the allowed list:", allowed_values)
                validated = false
              }
            }

          }
          return validated
        }

#####  explanation for above code-snippet-for-"validate_attribute_in_list"-function :
--> In the second line of the above code-snippet , "find_resources_from_plan" is been invoked which return the info of aws-instances from terraform plan. This Terraform-plan is refered by importing tfplan (this tfplan inturn refered from mockdata , for example of the mockdata click [here](https://github.com/Nokku-Organization/Sentinel-Terraform-Enterprise/blob/master/Sentinel-Imports/mocks/ec2-instance-mock-tfplan.sentinel) )

    # Find all resources of a specific type from all modules using the tfplan import
    find_resources_from_plan = func(type) {

      resources = {}

      # Iterate over all modules in the tfplan import
      for tfplan.module_paths as path {
        # Iterate over the named resources of desired type in the module
        for tfplan.module(path).resources[type] else {} as name, instances {
          # Iterate over resource instances
          for instances as index, r {

            # Get the address of the instance
            if length(path) == 0 {
              # root module
              address = type + "." + name + "[" + string(index) + "]"
            } else {
              # non-root module
              address = "module." + strings.join(path, ".module.") + "." +
                        type + "." + name + "[" + string(index) + "]"
            }

            # Add the instance to resources map, setting the key to the address
            resources[address] = r
          }
        }
      }
      
      
  --> After getting resources from "find_resources_from_plan" function, our "validate_attribute_in_list" function iterate over all resources, and find whether planned instanceType is allowed or not.
     -Here in this code "if-else" structure is important and followed as below, otherwise we may encounter error.
     -"if r.diff[attribute].computed else false is true" this type of statements are necessary to follow , because if " r.diff[attribute].computed" is undefinded then the policy will break at that point and policy will fail. but in this case if the attribute value is undefined , false is considered and policy won't break.
  
           # Determine if the attribute is computed
            if r.diff[attribute].computed else false is true {
            } else {
              # Validate that each instance has allowed value
              if r.applied[attribute] else "" not in allowed_values {
                print("Resource", address, "has attribute", attribute, "with value",
                      r.applied[attribute] else "",
                      "that is not in the allowed list:", allowed_values)
                validated = false
              }
            }
