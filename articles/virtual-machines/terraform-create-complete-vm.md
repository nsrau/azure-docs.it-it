---
title: Creare un'infrastruttura di base in Azure usando Terraform | Microsoft Docs
description: Informazioni su come creare risorse di Azure con Terraform
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 8b8b3b0b46f79058ee69b9a2014581df433f8d3f
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="create-basic-infrastructure-in-azure-using-terraform"></a>Creare un'infrastruttura di base in Azure usando Terraform
Questo articolo illustra in dettaglio i passaggi necessari per eseguire il provisioning di macchine virtuali, insieme all'infrastruttura sottostante, in Azure. Spiega come creare script Terraform e come visualizzare le modifiche prima di apportarle nell'infrastruttura cloud. Spiega anche come creare l'infrastruttura in Azure usando Terraform.

Per iniziare, nell'editor di testo di propria scelta (Visual Studio Code/Sublime/Vim/e così via) creare un file denominato _terraform_azure101.tf_. Il nome esatto del file non è importante, poiché Terraform accetta il nome della cartella come parametro: vengono eseguiti tutti gli script nella cartella. Incollare il codice seguente nel nuovo file:

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
Nella sezione "provider" dello script, indicare a Terraform di usare un provider di Azure per eseguire il provisioning delle risorse nello script. Fare riferimento alla [guida per l'installazione e la configurazione di Terraform](terraform-install-configure.md) per ottenere i valori per subscription_id, client_id, client_secret e tenant_id. Si noti inoltre che se sono state create variabili di ambiente per i valori in questo blocco, non è necessario includerlo. 

La risorsa "azure_rm_resource_group" indica a Terraform di creare un nuovo gruppo di risorse. Di seguito vengono mostrati altri tipi di risorse disponibili in Terraform.

## <a name="executing-the-script"></a>Esecuzione dello script
Dopo avere salvato lo script, chiudere la riga di comando o la console e digitare:
```
terraform plan terraformscripts
```
Nell'esempio precedente si presuppone che "terraformscripts" sia la cartella in cui è stato salvato lo script. Viene usato il comando Terraform "plan", che esamina le risorse definite negli script, le confronta con le informazioni sullo stato salvate da Terraform e quindi restituisce l'esecuzione pianificata _senza_ effettivamente creare risorse in Azure. 

Dopo l'esecuzione del comando sopra indicato, dovrebbe essere visualizzata una schermata simile alla seguente:

![Immagine di plan di Terraform](linux/media/terraform/tf_plan2.png)

Se tutto sembra corretto, proseguire con il provisioning di questo nuovo gruppo di risorse in Azure eseguendo: 
```
terraform apply terraformscripts
```
Se si esamina il portale di Azure ora, si vede il nuovo gruppo di risorse vuoto denominato "terraformtest". Nella sezione seguente si aggiunge una VM e tutta la relativa infrastruttura di supporto al gruppo di risorse.

## <a name="provisioning-ubuntu-vm-with-terraform"></a>Provisioning di macchina virtuale Ubuntu con Terraform
È possibile estendere lo script di Terraform creato in precedenza con i dettagli necessari a eseguire il provisioning di una macchina virtuale che esegue Ubuntu. L'elenco di risorse di cui si eseguirà il provisioning nelle sezioni seguenti include: una rete con una singola subnet, una scheda di interfaccia di rete, un account di archiviazione con un contenitore di archiviazione, un indirizzo IP pubblico e una macchina virtuale che usa tutte le risorse precedenti. Per informazioni approfondite su ognuna delle risorse di Azure Terraform, consultare la [documentazione di Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

Per comodità viene fornita anche la versione completa dello [script di provisioning](#complete-terraform-script).

### <a name="extending-the-terraform-script"></a>Estensione dello script Terraform
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
Lo script precedente crea una rete virtuale e una subnet al suo interno. Si noti il riferimento al gruppo di risorse che è già stato creato tramite "${azurerm_resource_group.helloterraform.name}" sia nella definizione della rete virtuale e che nella definizione della subnet.

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
I frammenti di script precedenti creano un indirizzo IP pubblico e un'interfaccia di rete che usa l'indirizzo IP pubblico creato. Tenere presenti i riferimenti a subnet_id e public_ip_address_id. Terraform dispone di una intelligence integrata che gli permette di capire che l'interfaccia di rete ha una dipendenza sulle risorse che devono essere create prima della creazione dell'interfaccia di rete.

~~~~
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
~~~~
Qui è stato creato un account di archiviazione ed è stato definito un contenitore di archiviazione al suo interno. È qui che si archiviano i dischi rigidi virtuali per la macchina virtuale che si sta per creare.

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
Il frammento di codice precedente infine crea una macchina virtuale che usa tutte le risorse di cui è già stato eseguito il provisioning: account e contenitore di archiviazione per un disco rigido virtuale (VHD), interfaccia di rete con indirizzo IP pubblico e subnet specificata, nonché gruppo di risorse che è già stato creato. Si noti la proprietà vm_size, in cui lo script specifica un codice SKU A0 di Azure.

### <a name="executing-the-script"></a>Esecuzione dello script
Dopo avere salvato l'intero script, chiudere la riga di comando o la console e digitare:
```
terraform apply terraformscripts
```
Dopo qualche istante si dovrebbero vedere le risorse, inclusa una macchina virtuale, nel gruppo di risorse "terraformtest" nel portale di Azure.

## <a name="complete-terraform-script"></a>Completare lo script Terraform

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
È stata creata un'infrastruttura di base in Azure usando Terraform. Per scenari più complessi, inclusi alcuni esempi sull'utilizzo di servizi di bilanciamento del carico, i set di scalabilità di macchine virtuali, vedere i numerosi [esempi di Terraform per Azure](https://github.com/hashicorp/terraform/tree/master/examples). I [documenti Terraform](https://www.terraform.io/docs/providers/azurerm/index.html) includono un elenco completo e aggiornato dei provider di Azure supportati.
