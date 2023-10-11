<!-- BEGIN_TF_DOCS -->
# container

This module manages the hashicorp/azurerm container resources.
For more information see https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs > container

_<-- This file is autogenerated, please do not change. -->_

## Requirements

| Name | Version |
|------|---------|
| terraform | >=1.3 |
| azurerm | >=3.42.0 |

## Providers

| Name | Version |
|------|---------|
| azurerm | >=3.42.0 |

## Resources

| Name | Type |
|------|------|
| azurerm_container_registry.container_registry | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| container_registry | Resource definition, default settings are defined within locals and merged with var settings. For more information look at [Outputs](#Outputs). | `any` | `{}` | no |

## Outputs

| Name | Description |
|------|-------------|
| container_registry | Outputs all attributes of resource_type. |
| variables | Displays all configurable variables passed by the module. __default__ = predefined values per module. __merged__ = result of merging the default values and custom values passed to the module |

## Examples

Minimal configuration to install the desired resources with the module

```hcl
module "container" {
  source = "registry.terraform.io/telekom-mms/container/azurerm"
  container_registry = {
    crmms = {
      location            = "westeurope"
      resource_group_name = "rg-mms-github"
    }
  }
}
```

Advanced configuration to install the desired resources with the module

```hcl
module "network" {
  source = "registry.terraform.io/telekom-mms/network/azurerm"
  virtual_network = {
    vn-app-mms = {
      location            = "westeurope"
      resource_group_name = "rg-mms-github"
      address_space       = ["173.0.0.0/23"]
    }
  }
  subnet = {
    snet-app-mms = {
      resource_group_name  = module.network.virtual_network["vn-app-mms"].resource_group_name
      address_prefixes     = ["173.0.0.0/23"]
      virtual_network_name = module.network.virtual_network["vn-app-mms"].name
    }
  }
}

module "container" {
  source = "registry.terraform.io/telekom-mms/container/azurerm"
  container_registry = {
    crmms = {
      location            = "westeurope"
      resource_group_name = "rg-mms-github"
      sku                 = "Premium"
      network_rule_set = {
        ip_rule = {
          proxy = {
            ip_range = "172.0.0.2/32"
          }
        }
        virtual_network = {
          module.network.subnet["snet-app-mms"].name = {
            subnet_id = module.network.subnet["snet-app-mms"].id
          }
        }
      }
      tags = {
        project     = "mms-github"
        environment = terraform.workspace
        managed-by  = "terraform"
      }
    }
  }
}
```
<!-- END_TF_DOCS -->