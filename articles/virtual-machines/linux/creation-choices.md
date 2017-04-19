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
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7ff1317cdf6ccfe6b4e5035878fc4e096fcc0f9
ms.lasthandoff: 04/03/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Diversi modi di creare una macchina virtuale Linux
Azure offre la possibilità di creare una macchina virtuale (VM) Linux usando gli strumenti e i flussi di lavoro con cui si ha familiarità. Questo articolo offre un riepilogo delle differenze e degli esempi per la creazione di VM Linux e include l'interfaccia della riga di comando di Azure 2.0. È inoltre possibile visualizzare le opzioni tra cui l'[interfaccia della riga di comando di Azure 1.0](creation-choices-nodejs.md).

L'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) è disponibile per più piattaforme tramite un pacchetto npm, pacchetti forniti tramite distribuzione o un contenitore Docker. Installare la build più appropriata per l'ambiente e accedere a un account Azure usando [az login](/cli/azure/#login)

Gli esempi seguenti usano l'interfaccia della riga di comando di Azure 2.0. Vedere ogni articolo per altre informazioni sui comandi illustrati. È anche possibile trovare esempi sulle scelte di creazione Linux con l'[interfaccia della riga di comando di Azure 1.0](creation-choices-nodejs.md).

* [Creare una VM Linux usando l'interfaccia della riga di comando di Azure 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Questo esempio usa [az group create](/cli/azure/group#create) per creare un gruppo di risorse denominato `myResourceGroup`: 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * Questo esempio usa [az vm create](/cli/azure/vm#create) per creare una VM denominata `myVM` usando l'immagine Debian più recente con Azure Managed Disks e una chiave pubblica denominata `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Per usare dischi non gestiti, aggiungere il flag `--use-unmanaged-disks` al comando precedente. Verrà creato un account di archiviazione. Per altre informazioni, vedere [Azure Managed Disks Overview](../../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks).

* [Creare una VM Linux protetta usando un modello di Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L'esempio seguente usa [az group deployment create](/cli/azure/group/deployment#create) per creare una VM usando un modello archiviato in GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Include la creazione di un servizio di bilanciamento del carico e di più VM in un set di disponibilità.

* [Aggiungere un disco a una VM Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L'esempio seguente usa [az vm disk attach-new](/cli/azure/vm/disk#attach-new) per aggiungere un disco gestito da 50 GB a una VM esistente denominata `myVM`:
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Portale di Azure
Il [portale di Azure](https://portal.azure.com) consente di creare rapidamente una VM perché non richiede installazioni nel sistema. Usare il portale di Azure per creare la VM:

* [Creare una macchina virtuale Linux tramite il portale di Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

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
az vm image list-offers --publisher Canonical --location WestUS
```

Elenca gli SKU disponibili (versioni di distribuzione) di una determinata offerta:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

Elenca tutte le immagini disponibili per una determinata versione:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Per altri esempi sull'esplorazione e l'uso delle immagini disponibili, vedere [Individuare e selezionare immagini di macchine virtuali di Azure con l'interfaccia della riga di comando di Azure](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

* [Distribuzioni approvate per Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Informazioni per distribuzioni non approvate](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Come acquisire una macchina virtuale Linux come modello di Resource Manager](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandi **az vm** di avvio rapido di esempio per l'acquisizione di una VM esistente con dischi non gestiti:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Passaggi successivi
* Creare una VM Linux dal [portale](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), con l'[interfaccia della riga di comando](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oppure usando un [modello di Azure Resource Manager](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Dopo aver creato una VM Linux, [aggiungere un disco dati](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Azioni rapide per [reimpostare una password o chiavi SSH e gestire gli utenti](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

