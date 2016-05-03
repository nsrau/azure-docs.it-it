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
   ms.date="04/27/2016"
   ms.author="v-livech"/>


# Creare una VM Linux in Azure tramite l'interfaccia della riga di comando

Questo articolo illustra come distribuire rapidamente una macchina virtuale Linux in Azure usando il comando `azure vm quick-create` dell'interfaccia della riga di comando di Azure. Il comando `quick-create` distribuisce una VM con un'infrastruttura di base che è possibile usare per creare un prototipo o testare un concetto molto rapidamente. È il modo più veloce per creare una shell bash Linux. L'articolo richiede un account Azure ([fare clic qui per una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) connessa (`azure login`) e in modalità Resource Manager (`azure config mode arm`). È anche possibile distribuire rapidamente una VM Linux usando il [portale di Azure](virtual-machines-linux-quick-create-portal.md).

## Breve riepilogo del comando

```
# One command to deploy the VM and attach your SSH key
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## Distribuire la VM Linux

Usando lo stesso comando illustrato in precedenza verrà mostrato ogni prompt con l'output previsto.

Per ImageURN verrà usato `canonical:ubuntuserver:14.04.2-LTS:latest` per distribuire una VM Ubuntu 14.04. Per trovare un'immagine nel Marketplace, [cercare un'immagine](virtual-machines-linux-cli-ps-findimage.md) oppure è possibile [caricare un'immagine personalizzata](virtual-machines-linux-create-upload-generic.md).

Nell'analisi dettagliata del comando seguente sostituire i prompt con i valori dell'ambiente in uso, perché qui vengono usati valori di esempio. L'output dovrebbe essere simile al blocco di output seguente.

```bash
# Follow the prompts and enter your own names
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
info:    Executing command vm quick-create
Resource group name: exampleRGname
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: Linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ahmet
Password: ************************************************
Confirm password: ************************************************
```

```bash
########### expected output ###########
+ Looking up the VM "exampleVMname"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/azure_id_rsa.pub
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli38948918364134011018
info:    Could not find the storage account "cli38948918364134011018", trying to create new one
+ Creating storage account "cli38948918364134011018" in "westus"
+ Looking up the storage account cli38948918364134011018
+ Looking up the NIC "examp-westu-3894891836-nic"
info:    An nic with given name "examp-westu-3894891836-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-3894891836-vnet"
info:    Preparing to create new virtual network and subnet
| Creating a new virtual network "examp-westu-3894891836-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-3894891836-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-3894891836-vnet"
+ Looking up the subnet "examp-westu-3894891836-snet" under the virtual network "examp-westu-3894891836-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-3894891836-pip"
info:    PublicIP with given name "examp-westu-3894891836-pip" not found, creating a new one
+ Creating public ip "examp-westu-3894891836-pip"
+ Looking up the public ip "examp-westu-3894891836-pip"
+ Creating NIC "examp-westu-3894891836-nic"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Creating VM "exampleVMname"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Looking up the public ip "examp-westu-3894891836-pip"
data:    Id                              :/subscriptions/<**subscriptionsID**>/resourceGroups/exampleRGname/providers/Microsoft.Compute/virtualMachines/exampleVMname
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
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clife36db80ae0539d2-os-1460152163612
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://<**subscriptionsID**>.blob.core.windows.net/vhds/clife36db80ae0539d2-os-1460152163612.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ahmet
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-4C-B2
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-3894891836-nic
data:          Location                  :westus
data:            Public IP address       :13.88.22.244
data:            FQDN                    :examp-westu-3894891836-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://<**subscriptionsID**>.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Ora è possibile connettersi alla VM con SSH usando la porta SSH predefinita 22 e l'indirizzo IP pubblico (o il nome di dominio completo) indicato nell'output precedente.

```
ahmet@fedora$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

## Passaggi successivi

Il comando `azure vm quick-create` consente di distribuire rapidamente una VM per poter accedere a una shell bash e iniziare a lavorare. L'uso di `vm quick-create` non offre i vantaggi aggiuntivi di un ambiente complesso. Per distribuire una VM Linux personalizzata per l'infrastruttura, è possibile vedere gli articoli seguenti.

- [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](virtual-machines-linux-cli-deploy-templates.md)
- [Creare un ambiente personalizzato per una VM Linux usando direttamente i comandi dell'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Creare una VM Linux in Azure usando i modelli](virtual-machines-linux-cli-deploy-templates.md)
- [Creare una VM Linux protetta con SSH in Azure usando i modelli](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Creare una VM Linux usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md)

Questi articoli illustrano come iniziare a compilare un'infrastruttura di Azure oltre a un numero qualsiasi di strumenti di orchestrazione, configurazione e distribuzione delle infrastrutture proprietarie e open source.

<!---HONumber=AcomDC_0427_2016-->