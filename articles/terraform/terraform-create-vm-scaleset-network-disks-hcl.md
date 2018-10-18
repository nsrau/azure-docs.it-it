---
title: Usare Terraform per creare un set di scalabilità di macchine virtuali di Azure
description: Esercitazione sull'uso di Terraform per configurare e specificare la versione di un set di scalabilità di macchine virtuali di Azure completo di una rete virtuale e di dischi collegati gestiti
services: terraform
ms.service: terraform
keywords: terraform, devops, macchina virtuale, Azure, set di scalabilità, rete, archiviazione, moduli
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 06/04/2018
ms.openlocfilehash: 7ae97274b03dda4dcf5150c8faacc7d406dad9fd
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389616"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Usare Terraform per creare un set di scalabilità di macchine virtuali di Azure

I [set di scalabilità di macchine virtuali di Azure](/azure/virtual-machine-scale-sets) consentono di creare e gestire un gruppo macchine virtuali identiche con bilanciamento del carico in cui il numero di istanze di macchine virtuali può aumentare o diminuire automaticamente in risposta alla domanda o a una pianificazione definita. 

In questa esercitazione si imparerà come usare [Azure Cloud Shell](/azure/cloud-shell/overview) per eseguire le attività seguenti:

> [!div class="checklist"]
> * Configurare una distribuzione di Terraform
> * Usare variabili e output per la distribuzione di Terraform 
> * Creare e distribuire l'infrastruttura di rete
> * Creare e distribuire un set di scalabilità di macchine virtuali e collegarlo alla rete
> * Creare e distribuire un jumpbox per connettersi alle macchine virtuali tramite SSH

> [!NOTE]
> La versione più recente dei file di configurazione di Terraform usati in questo articolo si trovano nel [repository Awesome Terraform su Github](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

- **Installare Terraform**: seguire la procedura illustrata nell'articolo [Installare Terraform e configurare l'accesso ad Azure](/azure/virtual-machines/linux/terraform-install-configure).

- **Creare una coppia di chiavi SSH**: se non si ha già una coppia di chiavi SSH, seguire la procedura illustrata nell'articolo [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Creare la struttura di directory

1. Accedere al [portale di Azure](http://portal.azure.com).

1. Aprire [Azure Cloud Shell](/azure/cloud-shell/overview). Se in precedenza non è stato selezionato un ambiente, selezionare **Bash** come ambiente.

    ![Prompt di Cloud Shell](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Passare alla directory `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crea una directory denominata `vmss`.

    ```bash
    mkdir vmss
    ```

1. Passare alla nuova directory:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Creare il file di definizioni delle variabili
In questa sezione vengono definite le variabili che consentono di personalizzare le risorse create da Terraform.

In Azure Cloud Shell seguire questa procedura:

1. Creare un file denominato `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Attivare la modalità di inserimento con il tasto I.

1. Incollare il codice seguente nell'editor:

  ```JSON
  variable "location" {
    description = "The location where resources will be created"
  }

  variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
  }

  variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
  }
  ```

1. Premere ESC per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Creare il file di definizioni di output
In questa sezione viene illustrato come creare il file che descrive l'output dopo la distribuzione.

In Azure Cloud Shell seguire questa procedura:

1. Creare un file denominato `output.tf`.

    ```bash
    vi output.tf
    ```

1. Attivare la modalità di inserimento con il tasto I.

1. Incollare il codice seguente nell'editor per esporre il nome di dominio completo (FQDN) per le macchine virtuali. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Premere ESC per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definire l'infrastruttura di rete in un modello
In questa sezione viene illustrato come creare l'infrastruttura di rete seguente in un nuovo gruppo di risorse di Azure: 

  - Una rete virtuale con lo spazio indirizzi 10.0.0.0/16 
  - Una subnet con lo spazio indirizzi 10.0.2.0/24
  - Due indirizzi IP pubblici. Uno viene usato dal servizio di bilanciamento del carico del set di scalabilità di macchine virtuali, l'altro viene usato per la connessione al jumpbox SSH.

In Azure Cloud Shell seguire questa procedura:

1. Creare un file denominato `vmss.tf` per descrivere l'infrastruttura del set di scalabilità di macchine virtuali.

    ```bash
    vi vmss.tf
    ```

1. Attivare la modalità di inserimento con il tasto I.

1. Incollare il codice seguente alla fine del file per esporre il nome di dominio completo (FQDN) per le macchine virtuali. 

  ```JSON
  resource "azurerm_resource_group" "vmss" {
    name     = "${var.resource_group_name}"
    location = "${var.location}"
    tags     = "${var.tags}"
  }

  resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
  }

  resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    tags                = "${var.tags}"
  }

  resource "azurerm_subnet" "vmss" {
    name                 = "vmss-subnet"
    resource_group_name  = "${azurerm_resource_group.vmss.name}"
    virtual_network_name = "${azurerm_virtual_network.vmss.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "vmss" {
    name                         = "vmss-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    public_ip_address_allocation = "static"
    domain_name_label            = "${random_string.fqdn.result}"
    tags                         = "${var.tags}"
  }
  ```

1. Premere ESC per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>Effettuare il provisioning dell'infrastruttura di rete
Usando Azure Cloud Shell dalla directory in cui sono stati creati i file di configurazione (con estensione tf) seguire questa procedura:

1. Inizializzare Terraform.

  ```bash
  terraform init 
  ```

1. Eseguire il comando seguente per distribuire l'infrastruttura definita in Azure.

  ```bash
  terraform apply
  ```

  Terraform chiede di specificare un valore per la "posizione" poiché in `variables.tf` è stata definita la variabile **location** ma non è mai stata impostata. È possibile immettere una qualsiasi posizione valida, ad esempio "Stati Uniti occidentali" e quindi premere INVIO (racchiudere tra parentesi i valori contenenti spazi).

1. Terraform stampa l'output come definito nel file `output.tf`. Come illustrato nello screenshot seguente, il formato del nome di dominio completo sarà &lt;id>.&lt;posizione>.cloudapp.azure.com. Il valore id è un valore calcolato, mentre il valore posizione è il valore specificato durante l'esecuzione di Terraform.

  ![Nome di dominio completo del set di scalabilità di macchine virtuali per l'indirizzo IP pubblico](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Nel menu principale del portale di Azure selezionare **Gruppi di risorse**.

1. Nella scheda **Gruppi di risorse** selezionare **myResourceGroup** per visualizzare le risorse che sono state create da Terraform.
  ![Risorse di rete del set di scalabilità di macchine virtuali](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Aggiungere un set di scalabilità di macchine virtuali

In questa sezione viene illustrato come aggiungere al modello le risorse seguenti:

- Un servizio di bilanciamento del carico di Azure e le regole per gestire l'applicazione e collegarla all'indirizzo IP pubblico configurato in precedenza in questo articolo
- Un pool di indirizzi back-end di Azure da assegnare al servizio di bilanciamento del carico 
- Una porta probe di integrità usata dall'applicazione e configurata nel servizio di bilanciamento del carico 
- Un set di scalabilità di macchine virtuali posto dietro il servizio di bilanciamento del carico in esecuzione nella rete virtuale distribuita in precedenza in questo articolo
- [Nginx](http://nginx.org/) nei nodi del set di scalabilità di macchine virtuali usando [cloud-init](http://cloudinit.readthedocs.io/en/latest/).

In Cloud Shell seguire questa procedura:

1. Aprire il file di configurazione `vmss.tf`.

  ```bash
  vi vmss.tf
  ```

1. Andare alla fine del file e attivare la modalità Append con il tasto A.

1. Incollare il codice seguente alla fine del file:

  ```JSON
  resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.vmss.id}"
    }

    tags = "${var.tags}"
  }

  resource "azurerm_lb_backend_address_pool" "bpepool" {
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id     = "${azurerm_lb.vmss.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_lb_probe" "vmss" {
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id     = "${azurerm_lb.vmss.id}"
    name                = "ssh-running-probe"
    port                = "${var.application_port}"
  }

  resource "azurerm_lb_rule" "lbnatrule" {
      resource_group_name            = "${azurerm_resource_group.vmss.name}"
      loadbalancer_id                = "${azurerm_lb.vmss.id}"
      name                           = "http"
      protocol                       = "Tcp"
      frontend_port                  = "${var.application_port}"
      backend_port                   = "${var.application_port}"
      backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
      frontend_ip_configuration_name = "PublicIPAddress"
      probe_id                       = "${azurerm_lb_probe.vmss.id}"
  }

  resource "azurerm_virtual_machine_scale_set" "vmss" {
    name                = "vmscaleset"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    upgrade_policy_mode = "Manual"

    sku {
      name     = "Standard_DS1_v2"
      tier     = "Standard"
      capacity = 2
    }

    storage_profile_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = "osdisk"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    storage_profile_data_disk {
      lun          = 0
      caching        = "ReadWrite"
      create_option  = "Empty"
      disk_size_gb   = 10
    }

    os_profile {
      computer_name_prefix = "vmlab"
      admin_username       = "${var.admin_user}"
      admin_password       = "${var.admin_password}"
      custom_data          = "${file("web.conf")}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = "${azurerm_subnet.vmss.id}"
        load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
      }
    }

    tags = "${var.tags}"
}
  ```

1. Premere ESC per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1. Creare un file denominato `web.conf` da usare come configurazione cloud-init per le macchine virtuali che fanno parte del set di scalabilità. 

    ```bash
    vi web.conf
    ```

1. Attivare la modalità di inserimento con il tasto I.

1. Incollare il codice seguente nell'editor:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Premere ESC per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1. Aprire il file di configurazione `variables.tf`.

  ```bash
  vi variables.tf
  ```

1. Andare alla fine del file e attivare la modalità Append con il tasto A.

1. Personalizzare la distribuzione incollando il codice seguente alla fine del file:

  ```JSON
  variable "application_port" {
      description = "The port that you want to expose to the external load balancer"
      default     = 80
  }

  variable "admin_user" {
      description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
      default     = "azureuser"
  }

  variable "admin_password" {
      description = "Default password for admin account"
  }
  ``` 

1. Premere ESC per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1. Creare un piano Terraform per visualizzare la distribuzione del set di scalabilità di macchine virtuali. È necessario specificare una password a scelta, nonché la posizione per le risorse.

  ```bash
  terraform plan
  ```

  L'output del comando dovrebbe essere simile a quello illustrato nello screenshot seguente:

  ![Output della creazione del set di scalabilità di macchine virtuali](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Distribuire le nuove risorse in Azure.

  ```bash
  terraform apply 
  ```

  L'output del comando dovrebbe essere simile a quello illustrato nello screenshot seguente:

  ![Gruppo di risorse del set di scalabilità di macchine virtuali di Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Aprire un browser e connettersi all'FQDN restituito dal comando. 

  ![Risultati della connessione all'FQDN](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Aggiungere un jumpbox SSH
Un *jumpbox* SSH è un server singolo usato come tramite per poter accedere ad altri server nella rete. In questo passaggio vengono configurate le risorse seguenti:

- Un'interfaccia di rete (o jumpbox) connessa alla stessa subnet del set di scalabilità di macchine virtuali.

- Una macchina virtuale connessa a questa interfaccia di rete. Questo "jumpbox" è accessibile in remoto. Dopo avere stabilito la connessione, è possibile eseguire SSH in qualsiasi macchina virtuale nel set di scalabilità.

1. Aprire il file di configurazione `vmss.tf`.

  ```bash
  vi vmss.tf
  ```

1. Andare alla fine del file e attivare la modalità Append con il tasto A.

1. Incollare il codice seguente alla fine del file:

  ```JSON
  resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    public_ip_address_allocation = "static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = "${var.tags}"
  }

  resource "azurerm_network_interface" "jumpbox" {
    name                = "jumpbox-nic"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    ip_configuration {
      name                          = "IPConfiguration"
      subnet_id                     = "${azurerm_subnet.vmss.id}"
      private_ip_address_allocation = "dynamic"
      public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
    }

    tags = "${var.tags}"
  }

  resource "azurerm_virtual_machine" "jumpbox" {
    name                  = "jumpbox"
    location              = "${var.location}"
    resource_group_name   = "${azurerm_resource_group.vmss.name}"
    network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "jumpbox-osdisk"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    os_profile {
      computer_name  = "jumpbox"
      admin_username = "${var.admin_user}"
      admin_password = "${var.admin_password}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = "${var.tags}"
  }
  ```

1. Aprire il file di configurazione `output.tf`.

  ```bash
  vi output.tf
  ```

1. Andare alla fine del file e attivare la modalità Append con il tasto A.

1. Incollare il codice seguente alla fine del file per visualizzare il nome host del jumpbox al termine della distribuzione:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Premere ESC per disattivare la modalità di inserimento.

1. Salvare il file e chiudere l'editor vi immettendo il comando seguente:

    ```bash
    :wq
    ```

1. Distribuire il jumpbox.

  ```bash
  terraform apply 
  ```

Al termine della distribuzione, il contenuto del gruppo di risorse è simile a quello illustrato nello screenshot seguente:

![Gruppo di risorse del set di scalabilità di macchine virtuali di Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> La possibilità di accedere con una password è disabilitata nel jumpbox e nel set di scalabilità di macchine virtuali distribuito. Accedere con SSH per accedere alle macchine virtuali.

## <a name="environment-cleanup"></a>Pulizia dell'ambiente 

Per eliminare le risorse di Terraform che sono state create in questa esercitazione, immettere il comando seguente in Cloud Shell:

```bash
terraform destroy
```

Il processo di eliminazione può richiedere alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare Terraform per creare un set di scalabilità di macchine virtuali di Azure. Di seguito sono segnalate altre risorse di approfondimento su Terraform di Azure: 

 [Hub Terraform in Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure provider documentation (Documentazione sul provider Terraform in Azure)](http://aka.ms/terraform)  
 [Terraform Azure provider source (Codice sorgente del provider Terraform in Azure)](http://aka.ms/tfgit)  
 [Terraform Azure modules (Moduli del provider Terraform in Azure)](http://aka.ms/tfmodules)