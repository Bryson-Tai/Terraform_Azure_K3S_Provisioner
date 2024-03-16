# Terraform Azurerm with K3s Install on Single Node

## Prerequisite

1. [Terraform](https://spacelift.io/blog/how-to-install-terraform) Installed

2. Generate a pair of SSH Key - [Reference](https://docs.oracle.com/en/cloud/cloud-at-customer/occ-get-started/generate-ssh-key-pair.html#GUID-8B9E7FCB-CEA3-4FB3-BF1A-FD3406A2432F)

3. An Azure Cloud account with Service Principal created - [Reference](https://learn.microsoft.com/en-us/entra/identity-platform/howto-create-service-principal-portal)

## Notes

1. The Linux VM is using Ubuntu-22.04 as base image.
2. Please be aware that the Network Ingress is config to accept all source of IP address.

## Steps to utilize the script at Local Machine

1. Export credentials to your terminal environment

    ```bash
        export ARM_SUBSCRIPTION_ID=<"Subscription ID of your target Azure Subscription">
        export ARM_TENANT_ID=<"Directory (Tenant) ID from Service Principal">
        export ARM_CLIENT_ID=<"Application (Client) ID from Service Principal">
        export ARM_CLIENT_SECRET=<"Secret generated from Service Principal">
    ```

<br>

2. Export SSH Key Path to your terminal environment

    ```bash
        export TF_VAR_public_key_file_path=<Path to your SSH Public Key>
        export TF_VAR_private_key_file_path=<Path to your SSH Private Key>
    ```

<br>

3. Change the remote backend values to match your needs
    - Create a Storage account on Azure Portal
    - Create a Container
    - **Notes:** If you want to use local state, just comment out the backend config block at **$PWD/examples/basic/terraform.tf**

<br>

4. Run the script below to create your K3s VM

    ```bash
        cd ./examples/basic
        terraform init -upgrade
        terraform plan
        terraform apply
    ```

<br>

5. After run successfully, export KUBECONFIG with the k3s_config files

    - You will be able to see the files showed in **./examples/basic**

    ```bash
        export KUBECONFIG=./k3s_config
    ```

# Run on Azure DevOps Pipeline

## Steps to utilize the pipeline script

1. Head over to [Azure DevOps](https://dev.azure.com/)

2. Setup a new pipeline for this repository from the Version Management Platform you prefer.

3. Setup a new service connection to Azure Portal - [Reference](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml)

4. Setup a new environment to include Approval of Deployment - [Reference](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass)

5. Setup a Key Vault to store Variables as below

```text
    ARM_SUBSCRIPTION_ID= <"Subscription ID of your target Azure Subscription">
    ARM_TENANT_ID=<"Directory (Tenant) ID from Service Principal">
    ARM_CLIENT_ID=<"Application (Client) ID from Service Principal">
    ARM_CLIENT_SECRET=<"Secret generated from Service Principal">
```

5. Go to **Azure DevOps > Pipelines > Library** and Create Variable Groups

    - Create a new variable groups
    - Link the groups by using Azure Key Vault as source of credentials
    - Select all variables

6. Store both Public and Private SSH Key files to Secure Files at **Azure DevOps > Pipelines > Library**

7. Create a new Storage account to store your state file

    - **May skip this step if you already setup.**

8. Get values from above and change them in the **azure-pipelines.yml**

9. Start to run the pipeline.

10. **k3s_config** would be uploaded to artifacts. Download it and utilize it on your local machine.
