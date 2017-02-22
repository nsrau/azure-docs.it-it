---
title: Diversi modi per creare una macchina virtuale Linux in Azure | Microsoft Azure
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
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 42ee74ac250e6594616652157fe85a9088f4021a
ms.openlocfilehash: 23862762fcf0939ce84859fdae0274421c0bb5fe


---
# <a name="different-ways-to-create-a-linux-vm-including-the-azure-cli-20-preview"></a>Diversi modi per creare una macchina virtuale Linux, inclusa l'interfaccia della riga di comando di Azure 2.0 (anteprima)
Azure offre la possibilità di creare una macchina virtuale (VM) Linux usando gli strumenti e i flussi di lavoro con cui si ha familiarità. Questo articolo offre un riepilogo delle differenze e degli esempi per la creazione di VM Linux.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare macchine virtuali in Azure usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](virtual-machines-linux-creation-choices-nodejs.md): l'interfaccia della riga di comando per i modelli di distribuzione classici e di gestione delle risorse
- Interfaccia della riga di comando di Azure 2.0 (anteprima): interfaccia della riga di comando di nuova generazione per il modello di distribuzione di gestione delle risorse (questo articolo)

L'[interfaccia della riga di comando di Azure 2.0 (anteprima)](/cli/azure/install-az-cli2) è disponibile per più piattaforme tramite un pacchetto npm, pacchetti forniti tramite distribuzione o un contenitore Docker. Installare la build più appropriata per l'ambiente e accedere a un account Azure usando [az login](/cli/azure/#login)

Gli esempi seguenti usano l'interfaccia della riga di comando di Azure 2.0 (anteprima). Vedere ogni articolo per altre informazioni sui comandi illustrati. È anche possibile trovare esempi sulle scelte di creazione Linux con l'[interfaccia della riga di comando di Azure 1.0](virtual-machines-linux-creation-choices-nodejs.md).

* [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0 (anteprima)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Questo esempio usa [az group create](/cli/azure/group#create) per creare un gruppo di risorse denominato `myResourceGroup`: 
    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * Questo esempio usa [az vm create](/cli/azure/vm#create) per creare una VM denominata `myVM` usando l'immagine Debian più recente con Azure Managed Disks e una chiave pubblica denominata `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Per usare dischi non gestiti, aggiungere il flag `--use-unmanaged-disks` al comando precedente. Verrà creato un account di archiviazione. Per altre informazioni, vedere [Azure Managed Disks Overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks).

* [Creare una VM Linux protetta usando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L'esempio seguente usa [az group deployment create](/cli/azure/group/deployment#create) per creare una VM usando un modello archiviato in GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Include la creazione di un servizio di bilanciamento del carico e di più VM in un set di disponibilità.

* [Aggiungere un disco a una VM Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L'esempio seguente usa [az vm disk attach-new](/cli/azure/vm/disk#attach-new) per aggiungere un disco non gestito da 5 GB denominato `myDataDisk.vhd` a una VM esistente denominata `myVM`:
  
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://mystorageaccount.blob.core.windows.net/vhds/myDataDisk.vhd
    ```

## <a name="azure-portal"></a>Portale di Azure
Il [portale di Azure](https://portal.azure.com) consente di creare rapidamente una VM perché non richiede installazioni nel sistema. Usare il portale di Azure per creare la VM:

* [Creare una macchina virtuale Linux tramite il portale di Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Sistema operativo e opzioni dell'immagine
Quando si crea una macchina virtuale, scegliere un'immagine in base al sistema operativo da eseguire. Azure e i relativi partner mettono a disposizione diverse immagini, alcune delle quali includono applicazioni e strumenti preinstallati. In alternativa, caricare una delle immagini personalizzate (vedere la [sezione seguente](#use-your-own-image)).

### <a name="azure-images"></a>Immagini di Azure
Usare i comandi [az vm image](/cli/azure/vm/image) per visualizzare ciò che è disponibile per editore, versione di distribuzione e build.

Elenca gli editori disponibili:

```azurecli
az vm image list-publishers --location WestUS
```

Elenca i prodotti disponibili (offerte) per un determinato editore:

```azurecli
az vm image list-offers --publisher-name Canonical --location WestUS
```

Elenca gli SKU disponibili (versioni di distribuzione) di una determinata offerta:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer --location WestUS
```

Elenca tutte le immagini disponibili per una determinata versione:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Per altri esempi sull'esplorazione e l'uso delle immagini disponibili, vedere [Individuare e selezionare immagini di macchine virtuali di Azure con l'interfaccia della riga di comando di Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Il comando **az vm create** ha alias che è possibile usare per accedere rapidamente alle distribuzioni più comuni e alle versioni più recenti. Usare alias è in genere più veloce che specificare editore, offerta, SKU e versione ogni volta che si crea una VM:

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
* [Come acquisire una macchina virtuale Linux come modello di Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandi **az vm** di avvio rapido di esempio per l'acquisizione di una VM esistente con dischi non gestiti:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Passaggi successivi
* Creare una VM Linux dal [portale](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), con l'[interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oppure usando un [modello di Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Dopo aver creato una VM Linux, [aggiungere un disco dati](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Azioni rapide per [reimpostare una password o chiavi SSH e gestire gli utenti](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO2-->


