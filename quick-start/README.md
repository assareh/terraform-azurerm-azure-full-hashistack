# Azure HashiStack Terraform Module

### Provisions resources for a HashiStack auto-scaling group in Azure. 

## Deployment Prerequisites

1. In order to perform the steps in this guide, you will need to have an Azure subscription for which you can create Service Principals as well as network and compute resources. You can create a free Azure account [here](https://azure.microsoft.com/en-us/free/).

2. Certain steps will require entering commands through the Azure CLI. You can find out more about installing it [here](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

3. Create Azure API Credentials: set up the main Service Principal that will be used for Packer and Terraform:
    * [https://www.terraform.io/docs/providers/azurerm/index.html]()
    * The above steps will create a Service Principal with the [Contributor](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-built-in-roles#contributor) role in your Azure subscription

4. `export` environment variables for the main (Packer/Terraform) Service Principal. For example, create an `env.sh` file with the following values (obtained from step `1` above):

    ```
    # Exporting variables in both cases just in case, no pun intended
    export ARM_SUBSCRIPTION_ID="aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa"
    export ARM_CLIENT_ID="bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
    export ARM_CLIENT_SECRET="cccccccc-cccc-cccc-cccc-cccccccccccc"
    export ARM_TENANT_ID="dddddddd-dddd-dddd-dddd-dddddddddddd"
    export subscription_id="aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa"
    export client_id="bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
    export client_secret="cccccccc-cccc-cccc-cccc-cccccccccccc"
    ```

5. Finally, create a read-only Azure Service Principal (using the Azure CLI) that will be used to perform the Consul auto-join (make note of these values as you will use them later in this guide):

    ```
    $ az ad sp create-for-rbac --role="Reader" --scopes="/subscriptions/[YOUR_SUBSCRIPTION_ID]"
    ```

## Variables

### Required

- `name`: The name to use on all of the resources.

### Optional

- `environment`: Name of the environment for resource tagging (ex: dev, prod, etc).
- `provider`: Provider name to be used in the templated scripts run as part of cloud-init
- `local_ip_url`: The URL to use to get a resource's IP address at runtime.
- `admin_username`: The username to use for each VM.
- `admin_password`: The password to use for each VM.
- `azure_region`: The Azure Region to use for all resources (ex: westus, eastus).
- `azure_os`: The operating system to use on each VM.
- `azure_vm_size`: The size to use for each VM.
- `azure_vnet_cidr_block`: The public network CIDRs to add to the virtual network.
- `hashistack_consul_version`: The version number of Consul to install on the VM at runtime.
- `hashistack_vault_version`: The version number of Vault to install on the VM at runtime.
- `hashistack_nomad_version`: The version number of Nomad to install on the VM at runtime.
- `hashistack_consul_url`: URL to download Consul version if internet not accessible.
- `hashistack_vault_url`: URL to download Vault version if internet not accessible.
- `hashistack_nomad_url`: URL to download Nomad version if internet not accessible.
- `consul_server_config_override`: Any additional Consul server coonfiguration to override defaults.
- `consul_client_config_override`: Any additional Consul client coonfiguration to override defaults.
- `vault_config_override`: Any additional Vault coonfiguration to override defaults.
- `nomad_config_override`: Any additional Nomad coonfiguration to override defaults.

## Outputs

- `quick_jumphost_ssh_string`: Copy paste this string to SSH into the jumphost.
- `consul_ui`: Use this link to access the Consul UI.
- `vault_ui`: Use this link to access the Vault UI.
- `nomad_ui`: Use this link to access the Nomad UI.

## Viewing Logs

Example of viewing `cloud-init` logs:

```
$ tail -f /var/log/cloud-init-output.log
```

Example of viewing `consul` server logs:

```
$ journalctl _SYSTEMD_UNIT=consul.service
```

## Authors

HashiCorp Solutions Engineering Team.

## License

Mozilla Public License Version 2.0. See LICENSE for full details.