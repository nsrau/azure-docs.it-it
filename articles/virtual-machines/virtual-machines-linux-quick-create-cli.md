<properties
   pageTitle="Creare rapidamente una VM Linux in Azure tramite l'interfaccia della riga di comando | Microsoft Azure"
   description="Creare una VM Linux in Azure tramite l'interfaccia della riga di comando."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>


# Creare una VM Linux in Azure tramite l'interfaccia della riga di comando

Questo articolo illustra come creare rapidamente una VM Linux in Azure con il comando `azure vm quick-create` dell'interfaccia della riga di comando di Azure, che crea una VM con un'infrastruttura di base che è possibile usare per creare il prototipo o testare un concetto molto rapidamente. L'articolo richiede un account Azure. ([Ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) in modalità Azure Resource Manager (`azure config mode arm`).

## Breve riepilogo del comando

```
# One command to quickly the VM that prompts for arguments
chrisL@fedora$ azure vm quick-create
```

## Procedura dettagliata

### Creare la VM Linux

Nel comando seguente è possibile usare qualsiasi immagine si desideri, ma questo esempio usa `canonical:ubuntuserver:14.04.2-LTS:latest` per creare rapidamente una VM. Per trovare un'immagine nel marketplace, [cercare un'immagine](virtual-machines-linux-cli-ps-findimage.md) oppure è possibile [caricare un'immagine personalizzata](virtual-machines-linux-create-upload-generic.md). Verrà visualizzata una schermata simile alla seguente.

Negli esempi di comandi seguenti sostituire i valori compresi tra &lt; e &gt; con i valori dell'ambiente locale.

```bash
# Create the Linux VM using prompts
username@macbook$ azure vm quick-create
info:    Executing command vm quick-create
Resource group name: exampleResourceGroup
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "exampleVMname"
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli133501687
info:    Could not find the storage account "cli1301687", trying to create new one
+ Creating storage account "cli133501687" in "westus"
+ Looking up the storage account cli133501687
+ Looking up the NIC "quick-westu-1363648838-nic"
info:    An nic with given name "quick-westu-1363648838-nic" not found, creating a new one
+ Looking up the virtual network "quick-westu-1363648838-vnet"
info:    Preparing to create new virtual network and subnet
\ Creating a new virtual network "quick-westu-1363648838-vnet" [address prefix: "10.0.0.0/16"] with subnet "quick-westu-13636488+8-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "quick-westu-1363648838-vnet"
+ Looking up the subnet "quick-westu-1363648838-snet" under the virtual network "quick-westu-1363648838-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "quick-westu-1363648838-pip"
info:    PublicIP with given name "quick-westu-1363648838-pip" not found, creating a new one
+ Creating public ip "quick-westu-1363648838-pip"
+ Looking up the public ip "quick-westu-1363648838-pip"
+ Creating NIC "quick-westu-1363648838-nic"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Creating VM "quickcreate"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Looking up the public ip "quick-westu-1363648838-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :ubuntuserver
data:        Sku                         :14.04.2-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli350d386daac1f01c-os-1457063387485
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1361687.blob.core.windows.net/vhds/cli350d386daac1f01c-os-1457063387485.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-E9-66
data:          Provisioning State        :Succeeded
data:          Name                      :quick-westu-1363648838-nic
data:          Location                  :westus
data:            Public IP address       :137.135.33.58
data:            FQDN                    :quick-westu-1363648838-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://cli13601687.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Ora è possibile connettere il protocollo SSH alla VM sulla porta SSH 22 predefinita.

`azure vm quick-create` crea rapidamente una VM a cui è possibile accedere per iniziare a lavorare. Non include tuttavia un ambiente complesso, quindi se si vuole personalizzare l'ambiente è possibile [usare un modello di Azure Resource Manager per creare rapidamente una distribuzione specifica](virtual-machines-linux-cli-deploy-templates.md) o [creare un ambiente personalizzato per una VM Linux usando direttamente i comandi dell'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-deploy-templates.md).

L'esempio precedente crea:

- un gruppo di risorse di Azure per distribuire la VM in
- un account di archiviazione di Azure per conservare il file con estensione vhd che rappresenta l'immagine della VM;
- una rete virtuale di Azure e una subnet per offrire la connettività alla VM;
- una scheda di interfaccia di rete (NIC) virtuale per associare la VM alla rete;
- un indirizzo IP pubblico e un prefisso del sottodominio per offrire un indirizzo Internet per l'uso esterno e quindi crea la VM Linux all’interno dell’ambiente.

Questa VM viene esposta direttamente a Internet ed è protetta solo da nome utente e password.

## Passaggi successivi

A questo punto è stata creata rapidamente una VM Linux da usare per scopi di test o dimostrazione. È possibile creare un ambiente di esecuzione più protetto con una VM Linux in Azure:

- [Creare una VM Linux in Azure usando i modelli di Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Creare una VM Linux protetta con SSH in Azure usando i modelli di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Creare una VM Linux in Azure usando l'interfaccia della riga di comando di Azure e personalizzando l'infrastruttura](virtual-machines-linux-create-cli-complete.md)

È anche possibile usare un numero qualsiasi di strumenti di orchestrazione, configurazione e distribuzione delle infrastrutture proprietarie e open source.

<!---HONumber=AcomDC_0406_2016-->