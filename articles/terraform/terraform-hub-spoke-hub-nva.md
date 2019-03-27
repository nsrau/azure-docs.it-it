---
title: Creare un'appliance di rete virtuale hub con Terraform in Azure
description: L'esercitazione implementa la creazione di una rete virtuale hub che funge da punto di connessione comune tra tutte le altre reti
services: terraform
ms.service: azure
keywords: terraform, hub e spoke, reti, reti ibride, devops, macchina virtuale, azure, peering reti virtuali, hub-spoke, hub.
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 4155a67f70ccc238c6046c07dded7f0214689617
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993517"
---
# <a name="tutorial-create-a-hub-virtual-network-appliance-with-terraform-in-azure"></a>Esercitazione: Creare un'appliance di rete virtuale hub con Terraform in Azure

Un **dispositivo VPN** è un dispositivo che fornisce connettività esterna a una rete locale. Il dispositivo VPN può essere un dispositivo hardware o una soluzione software. Un esempio di soluzione software è il servizio Routing e Accesso remoto (RRAS) in Windows Server 2012. Per altre informazioni sulle appliance VPN, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN](/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

Azure supporta un'ampia gamma di appliance di rete virtuale. Per questa esercitazione si usa un'immagine Ubuntu. Per altre informazioni sulla vasta gamma di soluzioni per dispositivi supportate in Azure, vedere la [home page delle appliance di rete](https://azure.microsoft.com/solutions/network-appliances/).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Usare HCL (HashiCorp Language) per implementare la rete virtuale hub nella topologia hub-spoke
> * Usare Terraform per creare una macchina virtuale della rete hub che fungerà da appliance
> * Usare Terraform per abilitare le route usando le estensioni CustomScript
> * Usare Terraform per creare tabelle di route gateway hub-spoke

## <a name="prerequisites"></a>Prerequisiti

1. [Creare una topologia di rete hub-spoke ibrida con Terraform in Azure](./terraform-hub-spoke-introduction.md).
1. [Creare una rete virtuale locale con Terraform in Azure](./terraform-hub-spoke-on-prem.md).
1. [Creare una rete virtuale hub con Terraform in Azure](./terraform-hub-spoke-hub-network.md).

## <a name="create-the-directory-structure"></a>Creare la struttura di directory

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire [Azure Cloud Shell](/azure/cloud-shell/overview). Se in precedenza non è stato selezionato un ambiente, selezionare **Bash** come ambiente.

    ![Prompt di Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Passare alla directory `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Passare alla nuova directory:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-network-appliance"></a>Dichiarare l'appliance di rete hub

Creare il file di configurazione Terraform che dichiara la rete virtuale locale.

1. In Cloud Shell creare un file denominato `hub-nva.tf`.

    ```bash
    code hub-nva.tf
    ```

1. Incollare il codice seguente nell'editor:
    
    ```JSON
    locals {
      prefix-hub-nva         = "hub-nva"
      hub-nva-location       = "CentralUS"
      hub-nva-resource-group = "hub-nva-rg"
    }

    resource "azurerm_resource_group" "hub-nva-rg" {
      name     = "${local.prefix-hub-nva}-rg"
      location = "${local.hub-nva-location}"

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_network_interface" "hub-nva-nic" {
      name                 = "${local.prefix-hub-nva}-nic"
      location             = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-nva-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-hub-nva}"
        subnet_id                     = "${azurerm_subnet.hub-dmz.id}"
        private_ip_address_allocation = "Static"
        private_ip_address            = "10.0.0.36"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_virtual_machine" "hub-nva-vm" {
      name                  = "${local.prefix-hub-nva}-vm"
      location              = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name   = "${azurerm_resource_group.hub-nva-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.hub-nva-nic.id}"]
      vm_size               = "${var.vmsize}"

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-hub-nva}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_virtual_machine_extension" "enable-routes" {
      name                 = "enable-iptables-routes"
      location             = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-nva-rg.name}"
      virtual_machine_name = "${azurerm_virtual_machine.hub-nva-vm.name}"
      publisher            = "Microsoft.Azure.Extensions"
      type                 = "CustomScript"
      type_handler_version = "2.0"

      settings = <<SETTINGS
        {
            "fileUris": [
            "https://raw.githubusercontent.com/mspnp/reference-architectures/master/scripts/linux/enable-ip-forwarding.sh"
            ],
            "commandToExecute": "bash enable-ip-forwarding.sh"
        }
    SETTINGS

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_route_table" "hub-gateway-rt" {
      name                          = "hub-gateway-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name           = "toHub"
        address_prefix = "10.0.0.0/16"
        next_hop_type  = "VnetLocal"
      }

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "hub-gateway-rt-hub-vnet-gateway-subnet" {
      subnet_id      = "${azurerm_subnet.hub-gateway-subnet.id}"
      route_table_id = "${azurerm_route_table.hub-gateway-rt.id}"
      depends_on = ["azurerm_subnet.hub-gateway-subnet"]
    }

    resource "azurerm_route_table" "spoke1-rt" {
      name                          = "spoke1-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-mgmt" {
      subnet_id      = "${azurerm_subnet.spoke1-mgmt.id}"
      route_table_id = "${azurerm_route_table.spoke1-rt.id}"
      depends_on = ["azurerm_subnet.spoke1-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-workload" {
      subnet_id      = "${azurerm_subnet.spoke1-workload.id}"
      route_table_id = "${azurerm_route_table.spoke1-rt.id}"
      depends_on = ["azurerm_subnet.spoke1-workload"]
    }

    resource "azurerm_route_table" "spoke2-rt" {
      name                          = "spoke2-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_in_ip_address = "10.0.0.36"
        next_hop_type          = "VirtualAppliance"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-mgmt" {
      subnet_id      = "${azurerm_subnet.spoke2-mgmt.id}"
      route_table_id = "${azurerm_route_table.spoke2-rt.id}"
      depends_on = ["azurerm_subnet.spoke2-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-workload" {
      subnet_id      = "${azurerm_subnet.spoke2-workload.id}"
      route_table_id = "${azurerm_route_table.spoke2-rt.id}"
      depends_on = ["azurerm_subnet.spoke2-workload"]
    }

    ```

1. Salvare il file e uscire dall'editor.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una rete virtuale spoke con Terraform in Azure](./terraform-hub-spoke-spoke-network.md)