---
title: Diversi modi per creare una VM Linux con l'Interfaccia della riga di comando di Azure 1.0 | Microsoft Docs
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
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 1eb90d44797d66f3e09811918ce5a7f4ad4287c6
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Diversi modi per creare una macchina virtuale Linux in Azure
Azure offre la possibilità di creare una macchina virtuale (VM) Linux usando gli strumenti e i flussi di lavoro con cui si ha familiarità. Questo articolo offre un riepilogo delle differenze e degli esempi per la creazione di VM Linux.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare macchine virtuali in Azure usando una delle versioni seguenti dell'interfaccia della riga di comando:

- Interfaccia della riga di comando di Azure 1.0: interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](../windows/creation-choices.md): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa

L'interfaccia della riga di comando di Azure 1.0 è disponibile per più piattaforme tramite un pacchetto npm, pacchetti forniti tramite distribuzione o un contenitore Docker. Altre informazioni su come [Installare l'interfaccia della riga di comando di Azure](../../cli-install-nodejs.md). Le esercitazioni seguenti offrono esempi relativi all'uso dell'interfaccia della riga di comando di Azure 1.0. Vedere ogni articolo per altre informazioni sui comandi di avvio rapido dell'interfaccia della riga di comando illustrati:

* [Creare una VM Linux dall'interfaccia della riga di comando di Azure per sviluppo e test](quick-create-cli-nodejs.md)
  
  * L'esempio seguente crea una VM CoreOS usando una chiave pubblica denominata *azure_id_rsa.pub*:
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Creare una VM Linux protetta usando un modello di Azure](create-ssh-secured-vm-from-template.md)
  
  * L'esempio seguente crea una VM usando un modello archiviato in GitHub:
    
    ```azurecli
    azure group create --name myResourceGroup --location eastus 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Creare un ambiente Linux completo tramite l'interfaccia della riga di comando di Azure](create-cli-complete-nodejs.md)
  
  * Include la creazione di un servizio di bilanciamento del carico e di più VM in un set di disponibilità.
* [Aggiungere un disco a una VM Linux](add-disk.md)
  
  * L'esempio seguente aggiunge un disco da *5* GB a una VM esistente denominata *myVM*:
    
    ```azurecli
    azure vm disk attach-new \
        --resource-group myResourceGroup \
        --vm-name myVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portale di Azure
Il [portale di Azure](https://portal.azure.com) consente di creare rapidamente una VM perché non richiede installazioni nel sistema. Usare il portale di Azure per creare la VM:

* [Creare una macchina virtuale Linux tramite il portale di Azure](quick-create-portal.md) 

## <a name="operating-system-and-image-choices"></a>Sistema operativo e opzioni dell'immagine
Quando si crea una macchina virtuale, scegliere un'immagine in base al sistema operativo da eseguire. Azure e i relativi partner mettono a disposizione diverse immagini, alcune delle quali includono applicazioni e strumenti preinstallati. In alternativa, caricare una delle immagini personalizzate (vedere la [sezione seguente](#use-your-own-image)).

### <a name="azure-images"></a>Immagini di Azure
Usare i comandi dell'interfaccia della riga di comando `azure vm image` per visualizzare ciò che è disponibile per editore, versione di distribuzione e build.

Elencare gli editori disponibili come segue:

```azurecli
azure vm image list-publishers --location eastus
```

Elencare i prodotti disponibili (offerte) per un determinato editore come segue:

```azurecli
azure vm image list-offers --location eastus --publisher Canonical
```

Elencare gli SKU disponibili (versioni di distribuzione) di una determinata offerta come segue:

```azurecli
azure vm image list-skus --location eastus --publisher Canonical --offer UbuntuServer
```

Elencare tutte le immagini disponibili per una determinata versione come segue:

```azurecli
azure vm image list --location eastus --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

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

* [Distribuzioni approvate per Azure](endorsed-distros.md)
* [Informazioni per distribuzioni non approvate](create-upload-generic.md)
* [Caricare e creare una VM Linux da un'immagine disco personalizzata](upload-vhd.md)
* [Come acquisire una macchina virtuale Linux come modello di Resource Manager](capture-image.md).
  
  * Comandi di avvio rapido di esempio per l'acquisizione di una VM esistente:
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Passaggi successivi
* Creare una VM Linux dal [portale](quick-create-portal.md), con l'[interfaccia della riga di comando](quick-create-cli.md) oppure usando un [modello di Azure Resource Manager](../windows/cli-deploy-templates.md).
* Dopo aver creato una VM Linux, [aggiungere un disco dati](add-disk.md).
* Azioni rapide per [reimpostare una password o chiavi SSH e gestire gli utenti](using-vmaccess-extension.md)


