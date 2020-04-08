# Sentinel-Terraform-Enterprise
Sentinel-Terraform-Enterprise is for managing terraform-enterprise by making restrictions via policies

Terraform Enterprise is our self-hosted distribution of Terraform Cloud. It offers enterprises a private instance of the Terraform Cloud application, with no resource limits and with additional enterprise-grade architectural features like audit logging and SAML single sign-on.

Sentinel is an embeddable policy as code framework to enable fine-grained, logic-based policy decisions that can be extended to source external information to make decisions

Sentinel gives operations teams the governance capabilities they need to ensure that all infrastructure provisioned with Terraform Enterprise complies with their organization's provisioning rules. 

## SENTINEL-POLICY :
  ### a)what is sentinel-policy:
  Terraform Cloud uses Sentinel as part of the Governance & Policy Feature Set to enable granular application of infrastructure. Broadly, Sentinel functions as a safeguard with regulations your organization defines.
  
  ![tf-withoutsentinel](tf-withoutsentinel.png)
  
  ![tf-withsentinel](tf-withsentinel.png)
  
  Sentinel also allows cost-centric policies to be created and then automatically enforced in the Terraform workflow.[clicke-here](https://www.hashicorp.com/blog/announcing-cost-estimation-for-terraform-cloud-and-enterprise/) for info.
  
  Note: For More Info clicke [here](https://learn.hashicorp.com/terraform/sentinel/sentinel-intro#why-use-sentinel)
  ### b)how to write a sentinel-policy:
  
  Sentinel expects there to be a main rule. The value of this rule is the result of the entire policy.

  If the result of main is true, the policy passes. If the value is anything else (false or a non-boolean value), the policy fails. The exact meaning of what happens a policy passes or fails is dependent on the host system

  * Sentinel policies are executed top-down

  The examples below is about the simplest practical example of Sentinel. It is reasonable to imagine this as a realistic policies. This shows that in most cases, Sentinel will be extremely simple:

 ##### Example-1:
        value = 10
        main = value > 5
 ##### Example-2:
      main = rule { request.method is "GET" and request.headers contains "X-Key" }

 #### Rules of Policy :
 Rules form the basis of a policy by representing behavior that is either passing or failing (true or false). Rules are a first class language construct in Sentinel. A policy can and should be broken down into rules to aid with readability, testability, and performance.

 ##### An example usage of rules is shown below:
    is_sunny     = rule { weather is "sunny" }
    is_wednesday = rule { day is "wednesday" }
    main = rule { is_sunny and is_wednesday }

   
  ### c)example explanation of a sentinel-policy code:
  An [Sample Example](https://github.com/Nokku-Organization/Sentinel-Terraform-Enterprise/tree/master/Example) is been given with complete Explanation in that [README](https://github.com/Nokku-Organization/Sentinel-Terraform-Enterprise/tree/master/Example) file
  
  
  ### d)sentinel files/file-structure in github:
  There are two types of files : ".sentinel" , "sentinel.hcl"
  - In <name>.sentinel files, we need to place our policy-code
  - In sentinel.hcl file, we need to mention which policy need to be applied and which level.
    There are three type of levels: 
    - hard-mandatory requires that the policy passes. If a policy fails, the run is halted and may not be applied until the failure is resolved.
    - soft-mandatory is much like hard-mandatory, but allows an administrator to override policy failures on a case-by-case basis.
    - advisory will never interrupt the run, and instead will only surface policy failures as informational to the user.
  - Refer this sample-example for better understanding structure: [Example](https://github.com/Nokku-Organization/Sentinel-Terraform-Enterprise/tree/master/Example) [README.md file you can see the explanation too, where it is not part of the code]
  


## How to associate Sentinel-policies with Terraform-Enterprise:

We have two ways to associate our sentinel-policies we created with Terraform-Enterprise-Organization.
They are: Policies and Policy Sets
(Note: Refer the below image , you will notice Policies , Policy-sets options on the left hand side)

As Mananging Individual policies wit Terraform Enterprise is depricated, we support only Policy-set usage. HenceForth, further discussion is only regarding Policy-sets.

#### Policy-set-Creation:
To manage policy sets, go to the "Policy Sets" section on the organization settings page.

![##policy-set-image](policy-sets-navigate-cd11a99d.png)

Policy sets enforced on all workspaces are marked "All workspaces" in this list; other policy sets show how many workspaces they are enforced on. You may also notice policy sets with an informational tag that reads "Policies managed individually", which indicates that the policy set is not versioned and uses policy relationships (deprecated). These policy sets will need to be migrated in the future.

To create a new policy set, click the "Create a new policy set" button; to edit an existing set, click its entry in the list. Click the "Create policy set" or "Update policy set" button when finished.

When creating or editing a policy set, the following fields are available:

##### Name: 
The name of the policy set, which is used in the UI. Must be unique to your organization. Accepts letters, numbers, -, and _.

##### Description:
A description of the policy set's purpose. The description can be any length and supports Markdown rendering.

##### Scope of policies: 
Whether the set should be enforced on all workspaces, or only on a chosen list of workspaces.

##### VCS repo or "Upload via API": 
This area allows selecting a VCS repository from an existing OAuth client connection. Choosing "Upload via API" will not configure VCS integration, and instead tarballs of policy sets may be uploaded via the API. See the policy set versions for more information on uploading policy sets using the API.

##### VCS Branch: 
This field allows specifying the branch within a VCS repository from which to import new versions of policies. If left blank, the value your version control provides as the default branch of the VCS repository is used.

##### Policies Path: 
This field allows specifying a sub-directory within a VCS repository for the policy set files. This allows maintaining multiple policy sets within a single repository. The value of this field should be the path to the directory containing the sentinel.hcl configuration file of the policy set you wish to configure. Any files in the repository which are not within this path will not be included when the policy set is cloned. Commits to the repository which do not match the specified directory will be ignored. If left blank, the root of the repository is used. A leading / may be used, but is optional (relative paths are assumed to originate from the root of the repository).

##### Workspaces: 
Which workspaces the policy set should be enforced on. This is only shown when the scope of policies is set to "Policies enforced on selected workspaces." Use the drop-down menu and "Add workspace" button to add workspaces, and the trash can (🗑) button to remove them.

##### Parameters: 
A list of key/value parameters that will be sent to the Sentinel runtime when a policy check is being performed for the policy set. If the value can be parsed as JSON, it will be sent to Sentinel as the corresponding type (string, boolean, integer, map or list). If it fails JSON validation, it will be sent as a string. For more information on parameters, see the Sentinel parameter documentation.

##### NOTE: 
FOR Additional INFO : [click here](https://www.terraform.io/docs/cloud/sentinel/manage-policies.html)


## HOW TO TEST SENTINEL-POLICIES : 
You can use four different methods for testing your Terraform Sentinel policies:
a)You can manually test policies against actual Terraform code by using the Terraform UI 
b)Or by using the Terraform CLI with the remote backend to trigger runs against workspaces that use that Terraform code.
c)You can execute automated tests against actual Terraform code by using the Terraform API.
d)You can use the Sentinel Simulator with mocks generated from Terraform plans.

In the first three methods, you are executing plans against your Terraform code and then testing the Sentinel policies against the generated plans. In the fourth method, you only use Terraform to generate your mocks. Because this method employs simulated testing, we recommend deploying policies to a Terraform server and running final testing against actual Terraform plans as well.

##### While the first method is sometimes easier for new Terraform users to start with, it does have some disadvantages:
- It is a manual method rather than an automated method.
- Each test will take longer than if you used the Sentinel Simulator since Terraform has to run a plan against your workspace before it even invokes any Sentinel policies.
- Unless you use Terraform policy sets carefully, you might end up running multiple policies for each test even though you only care about the one you are testing.
- If you use the Terraform UI, all the runs you do to test your policy will end up in the histories of your workspaces and you will need to discard each run you do that passes your policies.

Note: Using the Terraform CLI with the remote backend instead of the Terraform UI avoids the fourth problem because it runs speculative plans which cannot be applied and do not show up in the workspace history in the Terraform UI. This means that you do not need to discard any runs and won't have a long history of them in your workspaces. Additionally, if you use the Terraform CLI, you do not need to put your Terraform code in or link your workspaces to VCS repositories.

#### Since Testing via UI has disadvantages ,further in this guide we will focus on the fourth method described above.(i.e, using SENTINEL SIMULATOR and testing locally)


## TESTING SENTINEL-POLICIES LOCALLY VIA SENTINEL SIMULATOR:
First step is to install sentinel simulator in our machine(where you want test the policies).This iinstance/machine need not require any permissions to install sentinel-simulator or execute sentinel polcies.
  
  ### Installing Sentinel CLI
  we'll use Sentinel CLI to learn how to write and test policies for Terraform Cloud(Terraform Enterprise). Download it [here](https://docs.hashicorp.com/sentinel/downloads/).

  After downloading Sentinel, unzip the package. Sentinel CLI runs as a single binary named sentinel. Any other files in the package can be safely removed and Sentinel will still function.

  After Extracting move this 'sentinel' binary to '/usr/local/bin/' or for exact path setting pls follow the below process.
  #### PATH SETTING:
  The final step is to make sure that the sentinel binary is available on the PATH. See [this page](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux-unix) for instructions on setting the PATH on Linux and Mac. [This page](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) contains instructions for setting the PATH on Windows.


  #### Verifying the Installation:
  After installing Sentinel CLI, verify the installation was successful by opening a new terminal session and checking that the sentinel binary is available. By executing sentinel, you should see help output similar to the following:
      
      $ sentinel
      Usage: sentinel [--version] [--help] <command> [<args>]

      Available commands are:
          apply      Execute a policy and output the result
          fmt        Format Sentinel policy to a canonical format
          test       Test policies
          version    Prints the Sentinel runtime version
      
   If you get an error that the binary could not be found, then your PATH environment variable was not setup properly. Please go back and ensure that your PATH variable contains the directory where Sentinel was installed.
  
  For additional information : [AdditionalInfo](https://learn.hashicorp.com/terraform/sentinel/sentinel-intro#sentinel-cli)
  
  #### Using sentinel-cli to test sentinel-polcies-locally:
  You need to mock the data that you wish your policies to operate on(i.e, download some files containig data related to terraform-plan or terraform-config or etc to your pc)
 
 Mock data can be generated using the UI by expanding the plan status section of the run page, and clicking on the Download Sentinel mocks button.
 
 ![download-mocks-ce82bcf5](download-mocks-ce82bcf5.png)
 
 If the button is not visible, then the plan is ineligible for mock generation or the user doesn't have the necessary permissions. See [Mock Data Availability](https://www.terraform.io/docs/cloud/sentinel/mock.html#mock-data-availability) for more details.

##### Using Mock Data
Note: The v2 mock files are only available on Terraform 0.12 and higher.

Mock data is supplied as a bundled tarball, containing 4 files:

    mock-tfconfig.sentinel    # tfconfig mock data
    mock-tfconfig-v2.sentinel # tfconfig/v2 mock data
    mock-tfplan.sentinel      # tfplan mock data
    mock-tfplan-v2.sentinel   # tfplan/v2 mock data
    mock-tfstate.sentinel     # tfstate mock data
    mock-tfstate-v2.sentinel  # tfstate/v2 mock data
    mock-tfrun.sentinel       # tfrun mock data

The recommended placement of the files is in a subdirectory of the repository holding your policies, so they don't interfere with sentinel test. (While the test data is Sentinel code, it's not a policy and will produce errors if evaluated like one.)

    .
    ├── foo.sentinel
    ├── sentinel.json
    ├── test
    │   └── foo
    │       ├── fail.json
    │       └── pass.json
    └── testdata
        ├── mock-tfconfig.sentinel
        ├── mock-tfconfig-v2.sentinel
        ├── mock-tfplan.sentinel
        ├── mock-tfplan-v2.sentinel
        ├── mock-tfstate.sentinel
        ├── mock-tfstate-v2.sentinel
        └── mock-tfrun.sentinel

##### The "sentinel.json" configuration file would look like:

    {
      "mock": {
        "tfconfig": "testdata/mock-tfconfig.sentinel",
        "tfconfig/v1": "testdata/mock-tfconfig.sentinel",
        "tfconfig/v2": "testdata/mock-tfconfig-v2.sentinel",
        "tfplan": "testdata/mock-tfplan.sentinel",
        "tfplan/v1": "testdata/mock-tfplan.sentinel",
        "tfplan/v2": "testdata/mock-tfplan-v2.sentinel",
        "tfstate": "testdata/mock-tfstate.sentinel",
        "tfstate/v1": "testdata/mock-tfstate.sentinel",
        "tfstate/v2": "testdata/mock-tfstate-v2.sentinel",
        "tfrun": "testdata/mock-tfrun.sentinel"
      }
    }
##### The contents of "test/foo/pass.json" is :
    {
      "mock": {
        "tfconfig": "../../testdata/mock-tfconfig.sentinel",
        "tfconfig/v1": "../../testdata/mock-tfconfig.sentinel",
        "tfconfig/v2": "../../testdata/mock-tfconfig-v2.sentinel",
        "tfplan": "../../testdata/mock-tfplan.sentinel",
        "tfplan/v1": "../../testdata/mock-tfplan.sentinel",
        "tfplan/v2": "../../testdata/mock-tfplan-v2.sentinel",
        "tfstate": "../../testdata/mock-tfstate.sentinel",
        "tfstate/v1": "../../testdata/mock-tfstate.sentinel",
        "tfstate/v2": "../../testdata/mock-tfstate-v2.sentinel",
        "tfrun": "../../testdata/mock-tfrun.sentinel"
      },
      "test": {
        "main": true
      }
    }
 ##### FOR PROPER PLACEMENT OF MOCK-DATA IN YOUR PC , REFER [this example](https://github.com/Nokku-Organization/Sentinel-Terraform-Enterprise/tree/master/Mock-Structure-Example)
 
 ##### FOR DATA INSIDE MOCK REFER [here](https://github.com/Nokku-Organization/Sentinel-Terraform-Enterprise/tree/master/Sentinel-Imports/mocks) 
 
 NOTE: For More Info Refer [here](https://www.terraform.io/docs/cloud/sentinel/mock.html)
