---
title: Utilizzare moduli Terraform per creare l'infrastruttura di macchine virtuali Linux con bilanciamento del carico in Azure
description: Informazioni su come usare moduli Terraform per creare un cluster di macchine virtuali Linux con bilanciamento del carico in Azure
keywords: terraform, devops, macchina virtuale, rete, moduli
author: rloutlaw
manager: justhe
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 10/19/2017
ms.author: routlaw
ms.openlocfilehash: 86fee5446065ffa8b5e8186ba8d8f56aa3efc2ea
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="create-a-vm-cluster-with-terraform-using-custom-hcl"></a>Creare un cluster di macchine virtuali con Terraform usando un linguaggio HCL personalizzato

Questa esercitazione illustra la procedura per creare un cluster di elaborazione di piccole dimensioni usando il linguaggio [Hashicorp configurazione Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL). La configurazione crea un servizio di bilanciamento del carico, due macchine virtuali Linux in un [set di disponibilità](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) e tutte le risorse di rete necessarie.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare l'autenticazione con Azure
> * Creare il modello Terraform
> * Visualizzare le modifiche con il piano
> * Applicare la configurazione per creare il cluster

## <a name="set-up-authentication-with-azure"></a>Configurare l'autenticazione con Azure

> [!TIP]
> Se si [usano le variabili di ambiente di Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) o si esegue questa esercitazione in [Azure Cloud Shell](/azure/cloud-shell/overview), ignorare questo passaggio.

 Vedere [Installare Terraform e configurare l'accesso ad Azure](/azure/virtual-machines/linux/terraform-install-configure) per creare un'entità servizio di Azure. Usare questa entità servizio per compilare un nuovo file `azureProviderAndCreds.tf` in una directory vuota con il codice seguente:

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Creare il modello

Creare un nuovo modello Terraform denominato `main.tf` con il codice seguente: 

```tf
resource "azurerm_resource_group" "test" {
  name     = "acctestrg"
  location = "West US 2"
}

resource "azurerm_virtual_network" "test" {
  name                = "acctvn"
  address_space       = ["10.0.0.0/16"]
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"
}

resource "azurerm_subnet" "test" {
  name                 = "acctsub"
  resource_group_name  = "${azurerm_resource_group.test.name}"
  virtual_network_name = "${azurerm_virtual_network.test.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "test" {
  name                         = "publicIPForLB"
  location                     = "${azurerm_resource_group.test.location}"
  resource_group_name          = "${azurerm_resource_group.test.name}"
  public_ip_address_allocation = "static"
}

resource "azurerm_lb" "test" {
  name                = "loadBalancer"
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"

  frontend_ip_configuration {
    name                 = "publicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.test.id}"
  }
}

resource "azurerm_lb_backend_address_pool" "test" {
  resource_group_name = "${azurerm_resource_group.test.name}"
  loadbalancer_id     = "${azurerm_lb.test.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_network_interface" "test" {
  count               = 2
  name                = "acctni${count.index}"
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"

  ip_configuration {
    name                          = "testConfiguration"
    subnet_id                     = "${azurerm_subnet.test.id}"
    private_ip_address_allocation = "dynamic"
    load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
  }
}

resource "azurerm_managed_disk" "test" {
  count                = 2
  name                 = "datadisk_existing_${count.index}"
  location             = "${azurerm_resource_group.test.location}"
  resource_group_name  = "${azurerm_resource_group.test.name}"
  storage_account_type = "Standard_LRS"
  create_option        = "Empty"
  disk_size_gb         = "1023"
}

resource "azurerm_availability_set" "avset" {
  name                         = "avset"
  location                     = "${azurerm_resource_group.test.location}"
  resource_group_name          = "${azurerm_resource_group.test.name}"
  platform_fault_domain_count  = 2
  platform_update_domain_count = 2
  managed                      = true
}

resource "azurerm_virtual_machine" "test" {
  count                 = 2
  name                  = "acctvm${count.index}"
  location              = "${azurerm_resource_group.test.location}"
  availability_set_id   = "${azurerm_availability_set.avset.id}"
  resource_group_name   = "${azurerm_resource_group.test.name}"
  network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
  vm_size               = "Standard_DS1_v2"

  # Uncomment this line to delete the OS disk automatically when deleting the VM
  # delete_os_disk_on_termination = true

  # Uncomment this line to delete the data disks automatically when deleting the VM
  # delete_data_disks_on_termination = true

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "myosdisk${count.index}"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  # Optional data disks
  storage_data_disk {
    name              = "datadisk_new_${count.index}"
    managed_disk_type = "Standard_LRS"
    create_option     = "Empty"
    lun               = 0
    disk_size_gb      = "1023"
  }

  storage_data_disk {
    name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
    managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
    create_option   = "Attach"
    lun             = 1
    disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
  }

  os_profile {
    computer_name  = "hostname"
    admin_username = "testadmin"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }

  tags {
    environment = "staging"
  }
}
```

## <a name="visualize-the-changes-with-plan"></a>Visualizzare le modifiche con il piano

Eseguire `terraform plan` per visualizzare in anteprima l'infrastruttura di macchine virtuali creata dal modello.

![Piano Terraform](media/terraformPlanVmsWithModules.png)


## <a name="create-the-virtual-machines-with-apply"></a>Creare le macchine virtuali con il comando apply

Eseguire `terraform apply` per effettuare il provisioning delle macchine virtuali in Azure.

![Comando apply di Terraform](media/terraformApplyVmsWithModules.png)

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare l'elenco dei [moduli Terraform per Azure](https://registry.terraform.io/modules/Azure)
- Creare un [set di scalabilità di macchine virtuali con Terraform]()