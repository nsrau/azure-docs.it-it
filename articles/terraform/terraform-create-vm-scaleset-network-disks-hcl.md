---
title: "Usare Terraform e creare un set di scalabilità di macchine virtuali di Azure con HCL."
description: "Usare Terraform per configurare e specificare la versione di un set di scalabilità di macchine virtuali completo di una rete virtuale e di dischi collegati gestiti."
keywords: "terraform, devops, set di scalabilità, macchina virtuale, rete, archiviazione, moduli"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/04/2017
ms.topic: article
ms.openlocfilehash: 7a4e21d547b3d2b2399f9f68b1babd9f82a421b7
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="use-terraform-to-plan-and-create-a-networked-azure-vm-scale-set-with-managed-storage"></a>Usare Terraform per pianificare e creare un set di scalabilità di macchine virtuali di Azure connesso in rete con archiviazione gestita

In questo articolo viene usato [Terraform](https://www.terraform.io/) per creare e distribuire un [set di scalabilità di macchine virtuali di Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) con dischi gestiti tramite [Hashicorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare la distribuzione di Terraform
> * Usare variabili e output per la distribuzione di Terraform 
> * Creare e distribuire l'infrastruttura di rete
> * Creare e distribuire un set di scalabilità di macchine virtuali e collegarlo alla rete
> * Creare e distribuire un jumpbox per connettersi alle macchine virtuali tramite SSH

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

- [Installare Terraform e configurare l'accesso ad Azure](/azure/virtual-machines/linux/terraform-install-configure)
- [Creare una coppia di chiavi SSH](/azure/virtual-machines/linux/mac-create-ssh-keys) se non ne esiste già una.

## <a name="create-the-file-structure"></a>Creare la struttura di file

Creare tre nuovi file in una directory vuota con i nomi seguenti:

- `variables.tf` Questo file contiene i valori delle variabili usate nel modello.
- `output.tf` Questo file descrive le impostazioni che verranno visualizzate dopo la distribuzione.
- `vmss.tf` Codice per l'infrastruttura del set di scalabilità di macchine virtuali.

## <a name="create-the-variables-and-output-definitions"></a>Creare le variabili e le definizioni di output

In questo passaggio vengono definite le variabili che consentono di personalizzare le risorse create da Terraform.

Modificare il file `variables.tf`, copiare il codice seguente e quindi salvare le modifiche.

```tf 
variable "location" {
  description = "The location where resources will be created"
  default     = "West US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources will be created"
  default     = "myResourceGroup"
}
```

Modificare il file `output.tf` e copiare il codice seguente per esporre il nome di dominio completo per le macchine virtuali. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definire l'infrastruttura di rete in un modello

In questo passaggio viene creata l'infrastruttura di rete seguente in un nuovo gruppo di risorse di Azure: 

  - Una rete virtuale con lo spazio indirizzi 10.0.0.0/16 
  - Una subnet con lo spazio indirizzi 10.0.2.0/24
  - Due indirizzi IP pubblici. Uno viene usato dal servizio di bilanciamento del carico del set di scalabilità di macchine virtuali, l'altro viene usato per la connessione al jumpbox SSH.


Modificare e copiare il codice seguente nel file `vmss.tf`: 

```tf
resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
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
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> È consigliabile contrassegnare le risorse distribuite in Azure per semplificarne l'identificazione in futuro.

## <a name="create-the-network-infrastructure"></a>Creare l'infrastruttura di rete

Inizializzare l'ambiente Terraform eseguendo il comando seguente nella directory in cui sono stati creati i file `.tf`:

```bash
terraform init 
```

Eseguire quindi il comando seguente per distribuire l'infrastruttura in Azure.

```bash
terraform apply
```

Verificare che l'FQDN dell'indirizzo IP pubblico corrisponda alla configurazione: ![FQDN Terraform del set di scalabilità di macchine virtuali per l'indirizzo IP pubblico](./media/tf-create-vmss-step4-fqdn.png)


Il gruppo di risorse deve avere queste risorse: ![Risorse di rete Terraform del set di scalabilità di macchine virtuali](./media/tf-create-vmss-step4-rg.png)


## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Modificare l'infrastruttura per aggiungere il set di scalabilità di macchine virtuali

In questo passaggio vengono aggiunte le risorse seguenti al modello:

- Un servizio di bilanciamento del carico di Azure e le regole per gestire l'applicazione e collegarla all'indirizzo IP pubblico configurato in precedenza.
- Pool di indirizzi back-end di Azure da assegnare al servizio di bilanciamento del carico 
- Una porta probe di integrità usata dall'applicazione e configurata nel servizio di bilanciamento del carico 
- Un set di scalabilità di macchine virtuali posto dietro il servizio di bilanciamento del carico, in esecuzione nella rete virtuale distribuita in precedenza
- [Nginx](http://nginx.org/) nei nodi del set di scalabilità di macchine virtuali usando un'estensione di script personalizzata.

Aggiungere il codice seguente alla fine del file `vmss.tf`.

```tf
resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
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
    name              = ""
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
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
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

  extension { 
    name = "vmssextension"
    publisher = "Microsoft.OSTCExtensions"
    type = "CustomScriptForLinux"
    type_handler_version = "1.2"
    settings = <<SETTINGS
    {
        "commandToExecute": "sudo apt-get -y install nginx"
    }
    SETTINGS
  }

  tags {
    environment = "codelab"
  }
}
```

Personalizzare la distribuzione aggiungendo il codice seguente a `variables.tf`:

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Distribuire il set di scalabilità di macchine virtuali in Azure

Eseguire il comando seguente per visualizzare la distribuzione del set di scalabilità di macchine virtuali:

```bash
terraform plan
```

L'output del comando deve essere simile al seguente.
![Piano Terraform per l'aggiunta del set di scalabilità di macchine virtuali](./media/tf-create-vmss-step6-plan884d3aefd9708a711bc09a66e85eb149c23a3ccff959655ec00418168b2bd481.png)

Distribuire quindi le risorse aggiuntive in Azure: 

```bash
terraform apply 
```

Il contenuto del gruppo di risorse deve essere simile al seguente:

![Gruppo di risorse del set di scalabilità di macchine virtuali Terraform](./media/tf-create-vmss-step6-apply.png)

Aprire un browser e connettersi all'FQDN restituito dal comando. 

## <a name="add-an-ssh-jumpbox-to-the-existing-network"></a>Aggiungere un jumpbox SSH alla rete esistente 

In questo passaggio vengono configurate le risorse seguenti:
- Un'interfaccia di rete connessa alla stessa subnet del set di scalabilità della macchina virtuale.
- Una macchina virtuale connessa a questa interfaccia di rete. Questo "jumpbox" è accessibile in remoto. Dopo avere stabilito la connessione, è possibile eseguire SSH in qualsiasi macchina virtuale nel set di scalabilità.



Aggiungere il codice seguente alla fine del file `vmss.tf`:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
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

  tags {
    environment = "codelab"
  }
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
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Modificare `outputs.tf` e aggiungere il codice seguente per visualizzare il nome host del jumpbox al termine della distribuzione:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Distribuire il jumpbox

Distribuire il jumpbox.

```bash
terraform apply 
```

Al termine della distribuzione, il contenuto del gruppo di risorse è simile al seguente:

![Gruppo di risorse del set di scalabilità di macchine virtuali Terraform](./media/tf-create-create-vmss-step8.png)

> [!NOTE]
> L'accesso con una password è disabilitato nel jumpbox e nel set di scalabilità di macchine virtuali distribuito. Accedere con SSH per accedere alle macchine virtuali.

## <a name="clean-up-the-environment"></a>Pulire l'ambiente

I comandi seguenti eliminano le risorse create in questa esercitazione:

```bash
terraform destroy
```

Digitare `yes` quando viene chiesto di confermare l'eliminazione delle risorse. Il processo di eliminazione richiede alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito un set di scalabilità di macchine virtuali in Azure usando Terraform. Si è appreso come:

> [!div class="checklist"]
> * Inizializzare la distribuzione di Terraform
> * Usare variabili e output per la distribuzione di Terraform 
> * Creare e distribuire un'infrastruttura di rete
> * Creare e distribuire un set di scalabilità di macchine virtuali e collegarlo a un ambiente esistente
> * Creare e distribuire un jumpbox per connettersi alle macchine virtuali tramite SSH 
