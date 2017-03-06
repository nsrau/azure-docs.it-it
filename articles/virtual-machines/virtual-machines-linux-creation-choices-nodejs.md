---
title: Diversi modi per creare una VM Linux | Microsoft Docs
description: Informazioni sui diversi modi per creare una macchina virtuale Linux in Azure, con collegamenti a strumenti ed esercitazioni per ogni metodo.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 67d4fee2fc59651903d4c02d1fce84c7b81e5da1
ms.openlocfilehash: e038515302acc4871687ad592c44fcf9a352bfe6
ms.lasthandoff: 02/27/2017


---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Diversi modi per creare una macchina virtuale Linux in Azure
Azure offre la possibilità di creare una macchina virtuale (VM) Linux usando gli strumenti e i flussi di lavoro con cui si ha familiarità. Questo articolo offre un riepilogo delle differenze e degli esempi per la creazione di VM Linux.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare macchine virtuali in Azure usando una delle versioni seguenti dell'interfaccia della riga di comando:

- Interfaccia della riga di comando di Azure 1.0: interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](virtual-machines-linux-creation-choices.md): l'interfaccia della riga di comando di nuova generazione per il modello di distribuzione di gestione delle risorse

L'interfaccia della riga di comando di Azure 1.0 è disponibile per più piattaforme tramite un pacchetto npm, pacchetti forniti tramite distribuzione o un contenitore Docker. Altre informazioni su come [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). Le esercitazioni seguenti offrono esempi relativi all'uso dell'interfaccia della riga di comando di Azure 1.0. Vedere ogni articolo per altre informazioni sui comandi di avvio rapido dell'interfaccia della riga di comando illustrati:

* [Creare una VM Linux dall'interfaccia della riga di comando di Azure per sviluppo e test](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L'esempio seguente crea una VM CoreOS usando una chiave pubblica denominata `azure_id_rsa.pub`:
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L'esempio seguente crea una VM usando un modello archiviato in GitHub:
    
    ```azurecli
    azure group create --name myResourceGroup --location WestUS 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Include la creazione di un servizio di bilanciamento del carico e di più VM in un set di disponibilità.
* [Aggiungere un disco a una VM Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L'esempio seguente aggiunge un disco da 5 GB a una VM esistente denominata `TestVM`:
    
    ```azurecli
    azure vm disk attach-new --resource-group myResourceGroup  --vm-name myVM \
      --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portale di Azure
Il [portale di Azure](https://portal.azure.com) consente di creare rapidamente una VM perché non richiede installazioni nel sistema. Usare il portale di Azure per creare la VM:

* [Creare una macchina virtuale Linux tramite il portale di Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Sistema operativo e opzioni dell'immagine
Quando si crea una macchina virtuale, scegliere un'immagine in base al sistema operativo da eseguire. Azure e i relativi partner mettono a disposizione diverse immagini, alcune delle quali includono applicazioni e strumenti preinstallati. In alternativa, caricare una delle immagini personalizzate (vedere la [sezione seguente](#use-your-own-image)).

### <a name="azure-images"></a>Immagini di Azure
Usare i comandi dell'interfaccia della riga di comando `azure vm image` per visualizzare ciò che è disponibile per editore, versione di distribuzione e build.

Elencare gli editori disponibili come segue:

```azurecli
azure vm image list-publishers --location WestUS
```

Elencare i prodotti disponibili (offerte) per un determinato editore come segue:

```azurecli
azure vm image list-offers --location WestUS --publisher Canonical
```

Elencare gli SKU disponibili (versioni di distribuzione) di una determinata offerta come segue:

```azurecli
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Elencare tutte le immagini disponibili per una determinata versione come segue:

```azurecli
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Per altri esempi sull'esplorazione e l'uso delle immagini disponibili, vedere [Individuare e selezionare immagini di macchine virtuali di Azure con l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I comandi `azure vm quick-create` e `azure vm create` hanno alias che è possibile usare per accedere rapidamente alle distribuzioni più comuni e alle versioni più recenti. Usare alias è in genere più veloce che specificare editore, offerta, SKU e versione ogni volta che si crea una VM:

| Alias | Autore | Offerta | SKU | Versione |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7,2 |più recenti |
| CoreOS |CoreOS |CoreOS |Stabile |più recenti |
| Debian |credativ |Debian |8 |più recenti |
| openSUSE |SUSE |openSUSE |13.2 |più recenti |
| RHEL |Redhat |RHEL |7,2 |più recenti |
| SLES |SLES |SLES |12-SP1 |più recenti |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |più recenti |

### <a name="use-your-own-image"></a>Usare la propria immagine
Per personalizzazioni specifiche è possibile usare un'immagine basata su una VM di Azure esistente *acquisendo* tale VM. È anche possibile caricare un'immagine creata in locale. Per altre informazioni sulle distribuzioni supportate e su come usare immagini personalizzate, vedere gli articoli seguenti:

* [Distribuzioni approvate per Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Informazioni per distribuzioni non approvate](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Caricare e creare una VM Linux da un'immagine disco personalizzata](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Come acquisire una macchina virtuale Linux come modello di Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandi di avvio rapido di esempio per l'acquisizione di una VM esistente:
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Passaggi successivi
* Creare una VM Linux dal [portale](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), con l'[interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oppure usando un [modello di Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Dopo aver creato una VM Linux, [aggiungere un disco dati](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Azioni rapide per [reimpostare una password o chiavi SSH e gestire gli utenti](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


