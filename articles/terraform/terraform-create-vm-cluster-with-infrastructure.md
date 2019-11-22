---
title: 'Esercitazione: Creare un cluster di macchine virtuali di Azure con Terraform e HCL'
description: Usare Terraform e HCL per creare un cluster di macchine virtuali Linux con bilanciamento del carico in Azure
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 7fee1518c36407f4e6607cc9204f9615b024f56f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837714"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Esercitazione: Creare un cluster di macchine virtuali di Azure con Terraform e HCL

Questa esercitazione illustra come creare un piccolo cluster di elaborazione con [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

Si apprenderà come eseguire le attività seguenti:

> [!div class="checklist"]
> * Configurare l'autenticazione di Azure.
> * Creare un file di configurazione di Terraform.
> * Usare un file di configurazione di Terraform per creare un servizio di bilanciamento del carico.
> * Usare un file di configurazione di Terraform per distribuire due macchine virtuali Linux in un set di disponibilità.
> * Inizializzare Terraform.
> * Creare un piano di esecuzione di Terraform.
> * Applicare il piano di esecuzione di Terraform per creare le risorse di Azure.

## <a name="1-set-up-azure-authentication"></a>1. Configurare l'autenticazione di Azure

> [!NOTE]
> Se si usano le [variabili di ambiente di Terraform](/azure/virtual-machines/linux/terraform-install-configure) o si esegue questa esercitazione in [Azure Cloud Shell](terraform-cloud-shell.md), ignorare questo passaggio.

In questa sezione vengono generati un'entità servizio di Azure e due file di configurazione contenenti le credenziali dell'entità di sicurezza.

1. [Configurare un'entità servizio Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) per consentire a Terraform di eseguire il provisioning di risorse in Azure. Durante la creazione dell'entità servizio, prendere nota dei valori per l'ID sottoscrizione, i tenant, l'ID app e la password.

2. Aprire un prompt dei comandi.

3. Creare una directory vuota in cui archiviare i file Terraform.

4. Creare un nuovo file che contiene le dichiarazioni delle variabili. È possibile assegnare un nome qualsiasi a questo file con un'estensione `.tf`.

5. Copiare il codice seguente nel file di dichiarazione delle variabili:

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Creare un nuovo file che contiene i valori per le variabili Terraform. È uso comune denominare il file delle variabili Terraform `terraform.tfvars`, in quanto Terraform carica automaticamente qualsiasi file denominato `terraform.tfvars` (o che segue un modello `*.auto.tfvars`) se presente nella directory corrente. 

7. Copiare il codice seguente nel file di dichiarazione delle variabili. Assicurarsi di sostituire i segnaposto come segue: Per `subscription_id`, usare l'ID sottoscrizione di Azure specificato durante l'esecuzione di `az account set`. Per `tenant_id`, usare il valore `tenant` restituito da `az ad sp create-for-rbac`. Per `client_id`, usare il valore `appId` restituito da `az ad sp create-for-rbac`. Per `client_secret`, usare il valore `password` restituito da `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Creare un file di configurazione Terraform

In questa sezione viene creato un file che contiene le definizioni delle risorse per l'infrastruttura.

1. Creare un file denominato `main.tf`. 

2. Copiare le definizioni delle risorse di esempio seguenti nel file `main.tf` appena creato: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = [azurerm_lb_backend_address_pool.test.id]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
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
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
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

Il [comando terraform init](https://www.terraform.io/docs/commands/init.html) viene usato per inizializzare una directory che contiene i file di configurazione Terraform, vale a dire i file creati con le sezioni precedenti. È consigliabile eseguire sempre il comando `terraform init` dopo aver scritto una nuova configurazione di Terraform. 

> [!TIP]
> Il comando `terraform init` è idempotent, vale a dire che può essere chiamato ripetutamente con lo stesso risultato. Se pertanto si lavora in un ambiente di collaborazione e si ritiene che i file di configurazione possano essere stati modificati, è sempre consigliabile chiamare il comando `terraform init` prima di eseguire o applicare un piano.

Per inizializzare Terraform, eseguire il comando seguente:

  ```bash
  terraform init
  ```

  ![Inizializzazione di Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Creare un piano di esecuzione Terraform

Il [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) viene usato per creare un piano di esecuzione. Per generare un piano di esecuzione, Terraform aggrega tutti i file `.tf` nella directory corrente. 

Il [parametro -out](https://www.terraform.io/docs/commands/plan.html#out-path) salva il piano di esecuzione in un file di output. Questa funzionalità consente di risolvere i problemi di concorrenza comuni negli ambienti con più sviluppatori. Uno di questi problemi risolti dal file di output è lo scenario seguente:

1. Lo sviluppatore 1 crea il file di configurazione.
1. Lo sviluppatore 2 modifica il file di configurazione.
1. Lo sviluppatore 1 applica (esegue) il file di configurazione.
1. Lo sviluppatore 1 ottiene risultati imprevisti non sapendo che lo sviluppatore 2 ha modificato la configurazione.

Specificando un file di output, lo sviluppatore 1 impedisce allo sviluppatore 2 di influire sull'attività dello sviluppatore 1. 

Se non è necessario salvare il piano di esecuzione, eseguire il comando seguente:

  ```bash
  terraform plan
  ```

Se è necessario salvare il piano di esecuzione, eseguire il comando seguente. Sostituire i segnaposto con i valori appropriati per l'ambiente.

  ```bash
  terraform plan -out=<path>
  ```

Un altro parametro utile è [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Per impostazione predefinita, Terraform prova a trovare il file delle variabili come segue:
- File denominato `terraform.tfvars`
- File denominato usando il modello seguente: `*.auto.tfvars`

Tuttavia, il file delle variabili non deve necessariamente seguire nessuna delle due convenzioni precedenti. In tal caso, specificare il nome del file delle variabili con il parametro `-var-file`. L'esempio seguente illustra questo punto:

```hcl
terraform plan -var-file <my-variables-file.tf>
```

Terraform determina le azioni necessarie per ottenere lo stato specificato nel file di configurazione.

![Creazione di un piano di esecuzione Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Applicare un piano di esecuzione Terraform

Il passaggio finale di questa esercitazione consiste nell'usare il [comando terraform apply](https://www.terraform.io/docs/commands/apply.html) per applicare il set di azioni generate dal comando `terraform plan`.

Se si vuole applicare il piano di esecuzione più recente, eseguire il comando seguente:

  ```bash
  terraform apply
  ```

Se si vuole applicare il piano di esecuzione salvato in precedenza, eseguire il comando seguente. Sostituire i segnaposto con i valori appropriati per l'ambiente:

  ```bash
  terraform apply <path>
  ```

![Applicazione di un piano di esecuzione Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Creare un set di scalabilità di macchine virtuali di Azure tramite Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)