# NKS-AWS-terraform

Full steps shown in the offical docs here:

https://docs.netapp.com/us-en/kubernetes-service/intro-to-terraform-on-nks.html#get-an-nks-api-token

Pre-reqs:

1 - you need to set up NKS (trial or sign up)

2 - Create an Organization

3 - Under "Organization" -> "setup" configure your hyperscaler credentials

4 - Add an ssh-key pair for your client

Steps:

1 - terraform installed as per: https://learn.hashicorp.com/terraform/getting-started/install.html

2 - install the NKS Terraform plugin for your client OS: https://github.com/NetApp/terraform-provider-nks/releases

3 - run "terraform init" and verify the plugin is found: 

              netapp@terraform-client:~/terraform$ terraform providers
              .
              └── provider.nks

4 - create a directory for your config files: mkdir AWS-NKS

5 - copy or pull the files from this repository

6 - edit the "main.tf" file and set the "startup_worker_count" variable to the number of worker nodes you require initially

7 - edit the "terraform.tfvars" file and set your values as per your NKS setup in the pre-reqs as well as the node variables

8 - export your API Token: export NKS_API_TOKEN="<very long string>"
  
9 - plan your deployment: terraform plan -out "<your name>"
    example:
              terraform plan -out "AWS-NKS01"
              
10 - If you like what you see, apply your deployment: terraform apply "<your name>"
    example: 
              terraform apply "AWS-NKS01"
              
11 - Once your cluster is built and ready, you can edit the "main.tf" file and add a stanza for solutions - example:

              resource "nks_solution" "haproxy" {
                org_id     = "${data.nks_organization.default.id}"
                cluster_id = "${nks_cluster.terraform-cluster.id}"
                solution   = "haproxy"
              }

and then refresh your deployment by running: terraform apply -refresh=true

12 - Finally, when you have had enough of your cluster you can cleanup by running: terraform destroy

NB: It is best to check in the hyperscaler console to ensure all resources get cleaned up as you expect.

