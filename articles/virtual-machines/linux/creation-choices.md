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
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 756711abb014829971af126c5cb60c12e79c920e
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Diversi modi di creare una macchina virtuale Linux
Azure offre la possibilità di creare una macchina virtuale (VM) Linux usando gli strumenti e i flussi di lavoro con cui si ha familiarità. Questo articolo offre un riepilogo delle differenze e degli esempi per la creazione di VM Linux e include l'interfaccia della riga di comando di Azure 2.0. È inoltre possibile visualizzare le opzioni tra cui l'[interfaccia della riga di comando di Azure 1.0](creation-choices-nodejs.md).

L'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) è disponibile per più piattaforme tramite un pacchetto npm, pacchetti forniti tramite distribuzione o un contenitore Docker. Installare la build più appropriata per l'ambiente e accedere a un account Azure usando [az login](/cli/azure/#login)

* [Creare una VM Linux con l'interfaccia della riga di comando di Azure 2.0](quick-create-cli.md)
  
  * Usare [az group create](/cli/azure/group#create) per creare un gruppo di risorse denominato *myResourceGroup*: 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * Usare [az vm create](/cli/azure/vm#create) per creare una VM denominata *myVM* tramite l'immagine *UbuntuLTS* più recente e generare chiavi SSH se sono già presenti in *~/.ssh*:

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Creare una VM Linux con un modello di Azure](create-ssh-secured-vm-from-template.md)
  
  * L'esempio seguente usa [az group deployment create](/cli/azure/group/deployment#create) per creare una VM da un modello archiviato in GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Creare una VM Linux e personalizzarla con cloud-init](tutorial-automate-vm-deployment.md)

* [Creare un'applicazione con carico bilanciato e disponibilità elevata in più VM Linux](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Portale di Azure
Il [portale di Azure](https://portal.azure.com) consente di creare rapidamente una VM perché non richiede installazioni nel sistema. Usare il portale di Azure per creare la VM:

* [Creare una macchina virtuale Linux tramite il portale di Azure](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>Sistema operativo e opzioni dell'immagine
Quando si crea una macchina virtuale, scegliere un'immagine in base al sistema operativo da eseguire. Azure e i relativi partner mettono a disposizione diverse immagini, alcune delle quali includono applicazioni e strumenti preinstallati. In alternativa, caricare una delle immagini personalizzate (vedere la [sezione seguente](#use-your-own-image)).

### <a name="azure-images"></a>Immagini di Azure
Usare i comandi [az vm image](/cli/azure/vm/image) per visualizzare ciò che è disponibile per editore, versione di distribuzione e build.

Elenca gli editori disponibili:

```azurecli
az vm image list-publishers --location eastus
```

Elenca i prodotti disponibili (offerte) per un determinato editore:

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

Elenca gli SKU disponibili (versioni di distribuzione) di una determinata offerta:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

Elenca tutte le immagini disponibili per una determinata versione:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

Per altri esempi sull'esplorazione e l'uso delle immagini disponibili, vedere [Individuare e selezionare immagini di macchine virtuali di Azure con l'interfaccia della riga di comando di Azure](cli-ps-findimage.md).

Il comando [az vm create](/cli/azure/vm#create) ha alias che è possibile usare per accedere rapidamente alle distribuzioni più comuni e alle versioni più recenti. Usare alias è in genere più veloce che specificare editore, offerta, SKU e versione ogni volta che si crea una VM:

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
Per personalizzazioni specifiche è possibile usare un'immagine basata su una VM di Azure esistente acquisendo tale VM. È anche possibile caricare un'immagine creata in locale. Per altre informazioni sulle distribuzioni supportate e su come usare immagini personalizzate, vedere gli articoli seguenti:

* [Distribuzioni approvate per Azure](endorsed-distros.md)
* [Informazioni per distribuzioni non approvate](create-upload-generic.md)
* [Come creare un'immagine da una VM di Azure esistente](tutorial-custom-images.md).
  
  * Comandi di esempio di avvio rapido per la creazione di un'immagine da una VM di Azure esistente:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>Passaggi successivi
* Creare una VM Linux con l'[interfaccia della riga di comando](quick-create-cli.md), il [portale di Azure](quick-create-portal.md) o un [modello di Azure Resource Manager](../windows/cli-deploy-templates.md).
* Dopo aver creato una VM Linux, vedere le [informazioni sui dischi e l'archiviazione di Azure](tutorial-manage-disks.md).
* Azioni rapide per [reimpostare una password o chiavi SSH e gestire gli utenti](using-vmaccess-extension.md).

