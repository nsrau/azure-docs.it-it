---
title: Creare un'infrastruttura di base in Azure usando Terraform | Microsoft Docs
description: Informazioni su come creare risorse di Azure usando Terraform
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3787151651767e594dae21f3cfbf79023fc6db40
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="create-basic-infrastructure-in-azure-by-using-terraform"></a>Creare un'infrastruttura di base in Azure usando Terraform
Questo articolo illustra i passaggi necessari per eseguire il provisioning di una macchina virtuale in Azure e descrive l'infrastruttura sottostante. Spiega come creare script Terraform e come visualizzare le modifiche prima di implementarle nell'infrastruttura cloud. Spiega anche come creare l'infrastruttura in Azure usando Terraform.

Per iniziare, nell'editor di testo desiderato (Visual Studio Code/Sublime/Vim/e così via) creare il file \_terraform_azure101.tf_. Il nome esatto del file non è importante, perché Terraform accetta come parametro il nome della cartella e vengono eseguiti tutti gli script presenti nella cartella. Incollare il codice seguente nel nuovo file:

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_client_id_from_script_execution"
  client_secret   = "your_client_secret_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
Nella sezione `provider` dello script si indica a Terraform di usare un provider di Azure per eseguire il provisioning delle risorse nello script. Per ottenere i valori per subscription_id, client_id, client_secret e tenant_id, vedere [Installare e configurare Terraform per eseguire il provisioning di macchine virtuali e altra infrastruttura in Azure](terraform-install-configure.md). Se sono state create variabili di ambiente per i valori in questo blocco, non è necessario includere il blocco. 

La risorsa `azurerm_resource_group` indica a Terraform di creare un nuovo gruppo di risorse. Altri tipi di risorse disponibili in Terraform sono elencati più avanti in questo articolo.

## <a name="execute-the-script"></a>Eseguire lo script
Dopo avere salvato lo script passare alla console o alla riga di comando e digitare:
```
terraform plan terraformscripts
```
Si presuppone che `terraformscripts` sia la cartella in cui è stato salvato lo script. È stato usato il comando Terraform `plan`, che esamina le risorse definite negli script. Il comando confronta le risorse con le informazioni sullo stato salvate da Terraform e restituisce l'esecuzione pianificata _senza_ creare di fatto risorse in Azure. 

Dopo l'esecuzione del comando viene visualizzata una schermata simile alla seguente:

![Piano Terraform](linux/media/terraform/tf_plan2.png)

Se tutti i dati sono corretti effettuare il provisioning di questo nuovo gruppo di risorse in Azure eseguendo quanto segue: 
```
terraform apply terraformscripts
```
Nel portale di Azure viene visualizzato un nuovo gruppo di risorse vuoto con nome `terraformtest`. Nella sezione seguente si aggiunge una VM e l'infrastruttura di supporto al gruppo di risorse.

## <a name="provision-an-ubuntu-vm-with-terraform"></a>Eseguire il provisioning di una VM Ubuntu con Terraform
È possibile estendere lo script di Terraform creato in precedenza con i dettagli necessari per il provisioning di una macchina virtuale che esegue Ubuntu. Le risorse di cui viene effettuato il provisioning nelle sezioni seguenti sono:

* Una rete con una singola subnet
* Una scheda di interfaccia di rete 
* Un account di archiviazione con un contenitore di archiviazione
* Un indirizzo IP pubblico
* Una macchina virtuale che usa tutte le risorse precedenti 

Per informazioni approfondite sulle singole risorse di Azure Terraform, vedere la [documentazione di Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

Per comodità viene fornita anche la versione completa dello [script di provisioning](#complete-terraform-script).

### <a name="extend-the-terraform-script"></a>Estendere lo script Terraform
Estendere lo script creato in precedenza con le risorse seguenti: 
~~~~
# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}
~~~~
Lo script precedente crea una rete virtuale e una subnet al suo interno. Si noti il riferimento al gruppo di risorse già creato tramite "${azurerm_resource_group.helloterraform.name}" sia nella definizione della rete virtuale che nella definizione della subnet.

~~~~
# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}
~~~~
I frammenti di script precedenti creano un indirizzo IP pubblico e un'interfaccia di rete che usa l'indirizzo IP pubblico creato. Tenere presenti i riferimenti a subnet_id e public_ip_address_id. L'intelligence integrata di Terraform rileva che l'interfaccia di rete presenta una dipendenza dalle risorse che vanno create prima della creazione dell'interfaccia di rete.

~~~~
# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}
~~~~
In questo caso è stato creato un account di archiviazione e in tale account è stato definito un contenitore di archiviazione. L'account di archiviazione è la posizione di archiviazione dei dischi rigidi virtuali (VHD) della VM che sta per essere creata.

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
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
~~~~
Infine il frammento precedente crea una macchina virtuale che usa tutte le risorse già specificate. Tali risorse sono un account e un contenitore di archiviazione per un disco rigido virtuale, un'interfaccia di rete con subnet e indirizzo IP pubblico e il gruppo di risorse già creato. Si noti la proprietà vm_size, in cui lo script specifica un codice SKU A0 di Azure.

### <a name="execute-the-script"></a>Eseguire lo script
Dopo aver salvato l'intero script chiudere la riga di comando o la console e digitare:
```
terraform apply terraformscripts
```
Dopo qualche istante le risorse, compresa la VM, appaiono nel gruppo di risorse `terraformtest` nel portale di Azure.

## <a name="complete-the-terraform-script"></a>Completare lo script Terraform

```
variable "resourcesname" {
  default = "helloterraform"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = "XXX"
  client_id       = "XXX"
  client_secret   = "XXX"
  tenant_id       = "XXX"
}

# create a resource group if it doesn't exist
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}

# create virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "tfvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "tfsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}


# create public IPs
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
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

## <a name="next-steps"></a>Passaggi successivi
È stata creata un'infrastruttura di base in Azure usando Terraform. Per scenari più complessi, inclusi esempi d'uso di servizi di bilanciamento del carico e set di scalabilità delle macchine virtuali, vedere i numerosi [esempi di Terraform per Azure](https://github.com/hashicorp/terraform/tree/master/examples). La [documentazione di Terraform](https://www.terraform.io/docs/providers/azurerm/index.html) include un elenco completo e aggiornato dei provider di Azure supportati.

