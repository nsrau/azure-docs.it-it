---
title: Creare un cluster di macchine virtuali con Terraform e HCL
description: Usare Terraform e il linguaggio HCL (HashiCorp Configuration Language) per creare un cluster di macchine virtuali Linux con un servizio di bilanciamento del carico in Azure
keywords: terraform, devops, macchina virtuale, rete, moduli
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/13/2017
ms.author: tarcher
ms.openlocfilehash: 2435d694e6a1671a234d02f90860e5cafe98c2df
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Creare un cluster di macchine virtuali con Terraform e HCL

Questa esercitazione illustra la procedura per creare un cluster di elaborazione di piccole dimensioni usando il linguaggio [Hashicorp configurazione Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL). La configurazione crea un servizio di bilanciamento del carico, due macchine virtuali Linux in un [set di disponibilità](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) e tutte le risorse di rete necessarie.

In questa esercitazione:

> [!div class="checklist"]
> * Configurare l'autenticazione di Azure
> * Creare un file di configurazione Terraform
> * Inizializzare Terraform
> * Creare un piano di esecuzione Terraform
> * Applicare un piano di esecuzione Terraform

## <a name="1-set-up-azure-authentication"></a>1. Configurare l'autenticazione di Azure

> [!NOTE]
> Se si usano le [variabili di ambiente di Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) o si esegue questa esercitazione in [Azure Cloud Shell](terraform-cloud-shell.md), ignorare questo passaggio.

In questa sezione vengono generati un'entità servizio di Azure e due file di configurazione contenenti le credenziali dell'entità di sicurezza.

1. [Configurare un'entità servizio Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) per consentire a Terraform di eseguire il provisioning di risorse in Azure. Durante la creazione dell'entità servizio, prendere nota dei valori per l'ID sottoscrizione, i tenant, l'ID app e la password.

2. Aprire un prompt dei comandi.

3. Creare una directory vuota in cui archiviare i file Terraform.

4. Creare un nuovo file che contiene le dichiarazioni delle variabili. È possibile assegnare un nome qualsiasi a questo file con un'estensione `.tf`.

5. Copiare il codice seguente nel file di dichiarazione delle variabili:

  ```tf
  variable subscription_id {}
  variable tenant_id {}
  variable client_id {}
  variable client_secret {}
  
  provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
  }
  ```

6. Creare un nuovo file che contiene i valori per le variabili Terraform. È uso comune denominare il file delle variabili Terraform `terraform.tfvars` in quanto Terraform carica automaticamente qualsiasi file denominato `terraform.tfvars` (o che segue un modello di `*.auto.tfvars`) se presente nella directory corrente. 

7. Copiare il codice seguente nel file di dichiarazione delle variabili. Assicurarsi di sostituire i segnaposto nel modo seguente: per `subscription_id`, usare l'ID sottoscrizione di Azure specificato durante l'esecuzione di `az account set`. Per `tenant_id`, usare il valore `tenant` restituito da `az ad sp create-for-rbac`. Per `client_id`, usare il valore `appId` restituito da `az ad sp create-for-rbac`. Per `client_secret`, usare il valore `password` restituito da `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Creare un file di configurazione Terraform

In questa sezione viene creato un file che contiene le definizioni delle risorse per l'infrastruttura.

1. Creare un file denominato `main.tf`. 

2. Copiare le definizioni delle risorse di esempio seguenti nel file `main.tf` appena creato: 

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

## <a name="3-initialize-terraform"></a>3. Inizializzare Terraform 

Il [comando terraform init](https://www.terraform.io/docs/commands/init.html) viene usato per inizializzare una directory che contiene i file di configurazione Terraform, vale a dire i file creati con le sezioni precedenti. È consigliabile eseguire sempre il comando `terraform init` dopo avere scritto una nuova configurazione Terraform. 

> [!TIP]
> Il comando `terraform init` è idempotent, vale a dire che può essere chiamato ripetutamente con lo stesso risultato. Se pertanto si lavora in un ambiente di collaborazione e si ritiene che i file di configurazione possano essere stati modificati, è sempre consigliabile chiamare il comando `terraform init` prima di eseguire o applicare un piano.

Per inizializzare Terraform, eseguire il comando seguente:

  ```cmd
  terraform init
  ```

  ![Inizializzazione di Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Creare un piano di esecuzione Terraform

Il [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) viene usato per creare un piano di esecuzione. Per generare un piano di esecuzione, Terraform aggrega tutti i file `.tf` nella directory corrente. 

Se si lavora in un ambiente di collaborazione in cui la configurazione può cambiare tra l'ora di creazione del piano di esecuzione e l'ora di applicazione del piano, è necessario usare il [parametro -out del comando terraform plan](https://www.terraform.io/docs/commands/plan.html#out-path) per salvare il piano di esecuzione in un file. In caso contrario, se si lavora in un ambiente utente singolo, è possibile omettere il parametro `-out`.

Se il nome del file delle variabili di Terraform non è `terraform.tfvars` e non segue il modello `*.auto.tfvars`, è necessario specificare il nome file usando il [parametro -var-file del comando terraform plan](https://www.terraform.io/docs/commands/plan.html#var-file-foo) quando si esegue il comando `terraform plan`.

Durante l'elaborazione del comando `terraform plan`, Terraform esegue un aggiornamento e determina quali azioni sono necessarie per ottenere lo stato desiderato specificato nei file di configurazione.

Se non occorre salvare il piano di esecuzione, eseguire il comando seguente:

  ```cmd
  terraform plan
  ```

Se occorre salvare il piano di esecuzione, eseguire il comando seguente (sostituendo il segnaposto &lt;path> con il percorso di output desiderato):

  ```cmd
  terraform plan -out=<path>
  ```

![Creazione di un piano di esecuzione Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Applicare un piano di esecuzione Terraform

Il passaggio finale di questa esercitazione consiste nell'usare il [comando terraform apply](https://www.terraform.io/docs/commands/apply.html) per applicare il set di azioni generate dal comando `terraform plan`.

Se si vuole applicare il piano di esecuzione più recente, eseguire il comando seguente:

  ```cmd
  terraform apply
  ```

Se si vuole applicare un piano di esecuzione salvato in precedenza, eseguire il comando seguente (sostituendo il segnaposto &lt;path> con il percorso che contiene il piano di esecuzione salvato):

  ```cmd
  terraform apply <path>
  ```

![Applicazione di un piano di esecuzione Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare l'elenco dei [moduli Terraform per Azure](https://registry.terraform.io/modules/Azure)
- Creare un [set di scalabilità di macchine virtuali con Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)