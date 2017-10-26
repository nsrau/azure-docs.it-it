---
title: Creare una VM Linux usando l'interfaccia della riga di comando di Azure 1.0 |Microsoft Docs
description: Creare una VM Linux in Azure tramite l'interfaccia della riga di comando 1.0
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: v-livech
ms.openlocfilehash: 71bb55ab4800389c956cebd00eb3e6e506610153
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-linux-vm-using-the-azure-cli-10"></a>Creare una VM Linux usando l'interfaccia della riga di comando di Azure 1.0

Questo articolo illustra come distribuire rapidamente una macchina virtuale (VM) Linux in Azure usando il comando `azure vm quick-create` nell'interfaccia della riga di comando di Azure. Il comando `quick-create` distribuisce una VM all'interno di un'infrastruttura di base protetta, che può essere usata per creare un prototipo o testare un concetto rapidamente.

> [!NOTE]
Per creare una VM usando l'interfaccia della riga di comando di Azure 2.0, vedere [Creare una VM con l'interfaccia della riga di comando di Azure](../windows/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

È anche possibile distribuire rapidamente una VM Linux usando il [portale di Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

L'articolo richiede [file di chiave pubblica e privata SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="quick-commands"></a>Comandi rapidi

L'esempio seguente mostra come distribuire una VM CoreOS e collegare la chiave SSH (Secure Shell). Gli argomenti possono variare:

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

La seguente procedura riguarda la distribuzione di una VM UbuntuLTS, passo passo, con spiegazioni su ogni passaggio.

## <a name="vm-quick-create-aliases"></a>Alias del comando VM quick-create

Per scegliere rapidamente una distribuzione è possibile usare gli alias dell'interfaccia della riga di comando di Azure con mapping alle distribuzioni di sistemi operativi più diffuse. La tabella seguente elenca gli alias, a partire dall'interfaccia della riga di comando di Azure versione 0.10. Per impostazione predefinita, tutte le distribuzioni che usano `quick-create` fanno uso di macchine virtuali con risorse di archiviazione basate su unità SSD, che garantiscono un provisioning più veloce e accesso al disco a prestazioni elevate. Questi alias rappresentano una minima parte delle distribuzioni disponibili in Azure. Per trovare altre immagini in Azure Marketplace, è possibile [cercare un'immagine in PowerShell](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [sul Web](https://azure.microsoft.com/marketplace/virtual-machines/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) o [caricare un'immagine personalizzata](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

| Alias | Autore | Offerta | SKU | Versione |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7,2 |più recenti |
| CoreOS |CoreOS |CoreOS |Stabile |più recenti |
| Debian |credativ |Debian |8 |più recenti |
| openSUSE |SUSE |openSUSE |13.2 |più recenti |
| RHEL |Red Hat |RHEL |7,2 |più recenti |
| UbuntuLTS |Canonical |Ubuntu Server |14.04.4-LTS |più recenti |

Le sezioni seguenti illustrano come usare l'alias `UbuntuLTS` per l'opzione **ImageURN** (`-Q`) per distribuire Ubuntu Server 14.04.4 LTS.

L'esempio `quick-create` precedente ha solo chiamato il flag `-M` per identificare la chiave pubblica SSH da caricare durante la disabilitazione delle password SSH. Viene quindi chiesto di specificare gli argomenti seguenti:

* Nome del gruppo di risorse: per il primo gruppo di risorse di Azure in genere viene accettata una stringa qualsiasi.
* Nome della VM.
* posizione. `westus` o `westeurope` sono valori predefiniti idonei
* Linux: per indicare ad Azure quale sistema operativo viene usato.
* username

L'esempio seguente illustra come specificare tutti i valori in modo che non vengano richieste altre conferme. Il funzionamento è garantito, purché sia disponibile un file `~/.ssh/id_rsa.pub` come file di chiave pubblica in formato ssh-rsa:

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

L'output dovrebbe essere simile al blocco di output seguente:

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
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
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Accedere alla nuova VM
Connettersi alla macchina virtuale usando l'indirizzo IP pubblico elencato nell'output. È anche possibile usare il nome di dominio completo (FQDN) elencato:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

Il processo di accesso dovrebbe essere simile al blocco di output seguente:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Passaggi successivi
Il comando `azure vm quick-create` consente di distribuire rapidamente una macchina virtuale per poter accedere a una shell bash e iniziare a lavorare. L'uso di `vm quick-create` , tuttavia, non consente un controllo esteso né permette di creare un ambiente più complesso.  Per informazioni su come distribuire una VM Linux personalizzata per l'infrastruttura, è possibile vedere gli articoli seguenti:

* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux usando un modello di Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

È anche possibile [usare il driver di Azure `docker-machine` con vari comandi per creare rapidamente una VM Linux come host docker](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
