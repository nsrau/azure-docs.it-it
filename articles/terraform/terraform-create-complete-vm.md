---
title: 'Avvio rapido: Usare Terraform per creare una VM Linux completa in Azure'
description: In questo argomento di avvio rapido si usa Terraform per creare e gestire un ambiente completo per le macchine virtuali Linux in Azure
keywords: azure devops terraform linux vm macchina virtuale
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 03974d68477855d4ff55b7179312c91ba7d0d055
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943530"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Guida introduttiva: Creare un'infrastruttura completa per la macchina virtuale Linux in Azure con Terraform

Terraform consente di definire e creare distribuzioni di infrastrutture complete in Azure. I modelli Terrraform, compilati dall'utente in un formato leggibile, creano e configurano le risorse di Azure in modo coerente e riproducibile. In questo articolo viene illustrato come creare un ambiente Linux completo e le risorse di supporto con Terraform. Verrà anche descritto come [installare e configurare Terraform](terraform-install-configure.md).

> [!NOTE]
> Per il supporto specifico di Terraform, contattare direttamente Terraform usando uno dei relativi canali della community:
>
>    * La [sezione Terraform](https://discuss.hashicorp.com/c/terraform-core) del portale della community contiene domande, casi d'uso e modelli utili.
>
>    * Per domande relative ai provider, visitare la sezione sui [provider di Terraform](https://discuss.hashicorp.com/c/terraform-providers) del portale della community.


## <a name="create-azure-connection-and-resource-group"></a>Creare una connessione ad Azure e il gruppo di risorse

Verranno ora esaminate le singole sezioni di un modello Terraform. È anche disponibile una versione completa del [modello Terraform](#complete-terraform-script) che può essere copiata e incollata.

La sezione `provider` indica a Terraform di usare un provider di Azure. Per ottenere i valori per *subscription_id*, *client_id*, *client_secret* e *tenant_id*, vedere [Installare e configurare Terraform per eseguire il provisioning di macchine virtuali e altra infrastruttura in Azure](terraform-install-configure.md). 

> [!TIP]
> Se si creano variabili di ambiente per i valori o si usa l'[esperienza Azure Cloud Shell Bash](/azure/cloud-shell/overview), non è necessario includere le dichiarazioni di variabili in questa sezione.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

Nella sezione seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `eastus`:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

In altre sezioni si fa riferimento al gruppo di risorse con *${azurerm_resource_group.myterraformgroup.name}* .

## <a name="create-virtual-network"></a>Creare una rete virtuale
La sezione seguente crea una rete virtuale denominata *myVnet* nello spazio di indirizzi *10.0.0.0/16*:

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

La sezione seguente crea una subnet denominata *mySubnet* nella rete virtuale *myVnet*:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Creare un indirizzo IP pubblico
Per accedere alle risorse in Internet, creare e assegnare un indirizzo IP pubblico alla macchina virtuale. La sezione seguente crea un indirizzo IP pubblico denominato *myPublicIP*:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete
I gruppi di sicurezza di rete consentono di controllare il flusso del traffico di rete in ingresso e in uscita dalla macchina virtuale. La sezione seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup* e definisce una regola per consentire il traffico SSH sulla porta TCP 22:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Creare la scheda di interfaccia di rete virtuale
Una scheda di interfaccia di rete virtuale, NIC, connette la macchina virtuale a una rete virtuale specifica, a un indirizzo IP pubblico e a un gruppo di sicurezza di rete. La sezione seguente in un modello Terraform crea una scheda di interfaccia di rete virtuale denominata *myNIC* connessa alle risorse di rete virtuale create:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>Creare un account di archiviazione per la diagnostica
Per archiviare la diagnostica di avvio per una macchina virtuale, è necessario un account di archiviazione. La diagnostica di avvio può aiutare a risolvere i problemi e a monitorare lo stato della macchina virtuale. L'account di archiviazione creato viene usato solo per archiviare i dati della diagnostica di avvio. Poiché ogni account di archiviazione deve avere un nome univoco, la sezione seguente genera un testo casuale:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

A questo punto è possibile creare un account di archiviazione. La sezione seguente crea un account di archiviazione con il nome basato sul testo casuale generato nel passaggio precedente:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Creare macchina virtuale

Il passaggio finale consiste nel creare una macchina virtuale e usare tutte le risorse create. La sezione seguente crea una macchina virtuale denominata *myVM* e associa la scheda di rete virtuale denominata *myNIC*. Viene usata l'immagine più recente di *Ubuntu 16.04-LTS* e viene creato un nome utente *azureuser* con autenticazione della password disabilitata.

 I dati della chiave SSH vengono indicati nella sezione *ssh_keys*. Fornire una chiave SSH pubblica valida nel campo *key_data*.

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Completare lo script Terraform

Per riunire tutte queste sezioni e vedere Terraform in azione, creare un file denominato *terraform_azure.tf* e incollarvi il contenuto seguente:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Compilare e distribuire l'infrastruttura
Con il modello Terraform creato, il primo passaggio consiste nell'inizializzare Terraform. Questo passaggio assicura che Terraform disponga di tutti i prerequisiti necessari per compilare il modello in Azure.

```bash
terraform init
```

Il passaggio successivo riguarda la revisione di Terraform e la convalida del modello. Questo passaggio confronta le risorse richieste con le informazioni sullo stato salvate da Terraform e quindi restituisce l'esecuzione pianificata. *Non* vengono create risorse in Azure.

```bash
terraform plan
```

Dopo l'esecuzione del comando viene visualizzata una schermata simile alla seguente:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Se tutti i dati sono corretti e si è pronti a compilare l'infrastruttura in Azure, applicare il modello in Terraform:

```bash
terraform apply
```

Al termine, l'infrastruttura per la macchina virtuale è pronta. Ottenere l'indirizzo IP pubblico della VM con [az vm show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

È quindi possibile stabilire una connessione SSH alla VM:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Vedere altre informazioni sull'uso di Terraform in Azure](/azure/terraform)