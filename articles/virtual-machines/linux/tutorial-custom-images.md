---
title: "Esercitazione: Creare immagini di VM personalizzate con l'interfaccia della riga di comando di Azure"
description: In questa esercitazione viene descritto come usare l'interfaccia della riga di comando di Azure per creare un'immagine di macchina virtuale personalizzata in Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 7fff8539f330c7cbc886577eaf6dbe4417411999
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498141"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Esercitazione: Creare un'immagine personalizzata di una macchina virtuale di Azure con l'interfaccia della riga di comando di Azure

Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. In questa esercitazione viene creata un'immagine personalizzata di una macchina virtuale di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Creare una Raccolta immagini condivise
> * Creare una definizione dell'immagine
> * Creare una versione di immagine
> * Creare una macchina virtuale da un'immagine 
> * Condividere una raccolta di immagini


Questa esercitazione usa l'interfaccia della riga di comando all'interno di [Azure Cloud Shell](../../cloud-shell/overview.md), che viene costantemente aggiornato alla versione più recente. Per aprire Cloud Shell, selezionare **Prova** nella parte superiore di qualsiasi blocco di codice.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.4.0 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Panoramica

La [raccolta di immagini condivise](shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

Raccolta immagini condivise consente di condividere le immagini di macchine virtuali personalizzate con altri utenti. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. 

La funzionalità Raccolta di immagini condivise presenta più tipi di risorse:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Prima di iniziare

La procedura riportata di seguito illustra come prendere una VM esistente e convertirla in un'immagine personalizzata riutilizzabile che è possibile usare per creare nuove istanze di VM.

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, vedere l'[avvio rapido con interfaccia della riga di comando](quick-create-cli.md) per creare una macchina virtuale da usare per questa esercitazione. Quando si esegue l'esercitazione, sostituire i nomi delle risorse dove necessario.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini 

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. 

I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini.   I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

Creare una raccolta di immagini usando [sig az create](/cli/azure/sig#az-sig-create). L'esempio seguente crea un gruppo di risorse denominato *myGalleryRG* nell'area *Stati Uniti orientali* e una raccolta denominata *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Ottenere informazioni sulla VM

Per visualizzare un elenco delle macchine virtuali disponibili, usare [az vm list](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Quando si conosce il nome della macchina virtuale e il gruppo di risorse in cui si trova, per ottenere l'ID della macchina virtuale usare [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Copiare l'ID della macchina virtuale per usarlo in un secondo momento.

## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine

Le definizioni di immagini creano un raggruppamento logico per le immagini. Vengono usate per gestire le informazioni sulle versioni di immagini create al loro interno. 

I nomi delle definizioni di immagini possono essere costituiti da lettere maiuscole o minuscole, numeri, trattini e punti. 

Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](./shared-image-galleries.md#image-definitions).

Creare una definizione di immagine nella raccolta usando [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create). 

In questo esempio la definizione di immagine è denominata *myImageDefinition* ed è relativa a un'immagine [specializzata](./shared-image-galleries.md#generalized-and-specialized-images) del sistema operativo Linux. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

Copiare l'ID della definizione dell'immagine dall'output per usarla in un secondo momento.

## <a name="create-the-image-version"></a>Creare la versione di immagine

Per creare una versione di immagine dalla macchina virtuale, usare [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio la versione dell'immagine è *1.0.0* e verranno create due repliche nell'area *Stati Uniti centro-occidentali*, una replica nell'area *Stati Uniti centro-meridionali* e una replica nell'area *Stati Uniti orientali 2* usando l'archiviazione con ridondanza della zona. Le aree di replica devono includere l'area in cui si trova la macchina virtuale di origine.

Sostituire il valore di `--managed-image` in questo esempio con l'ID della macchina virtuale del passaggio precedente.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> È necessario attendere che la creazione della versione dell'immagine venga interamente completata e replicata prima di poter usare la stessa immagine gestita o creare un'altra versione di immagine.
>
> Quando si crea la versione dell'immagine, è anche possibile archiviare l'immagine nell'archiviazione Premium, aggiungendo `--storage-account-type  premium_lrs`, oppure nell'[archiviazione con ridondanza della zona](../../storage/common/storage-redundancy.md), aggiungendo `--storage-account-type  standard_zrs`.
>

 
## <a name="create-the-vm"></a>Creare la VM

Per creare la macchina virtuale, usare [az vm create](/cli/azure/vm#az-vm-create) con il parametro --specialized per indicare che si tratta di un'immagine specializzata. 

Usare l'ID definizione immagine per `--image` per creare la macchina virtuale dalla versione più recente dell'immagine disponibile. È anche possibile creare la macchina virtuale da una versione specifica fornendo l'ID versione dell'immagine per `--image`. 

In questo esempio viene creata una macchina virtuale dalla versione più recente dell'immagine *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>Condividere la raccolta

È possibile condividere immagini tra sottoscrizioni usando il controllo degli accessi in base al ruolo. È possibile condividere immagini a livello di raccolta, definizione di immagine o versione di immagine. Qualsiasi utente che abbia autorizzazioni di lettura per una versione di immagine, anche tra sottoscrizioni diverse, potrà distribuire una VM usando la versione dell'immagine.

È consigliabile condividere con altri utenti a livello di raccolta. Per ottenere l'ID oggetto della raccolta, usare [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Usare l'ID oggetto come ambito, insieme a un indirizzo di posta elettronica e a [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), per concedere a un utente l'accesso a Raccolta immagini condivise. Sostituire `<email-address>` e `<gallery iD>` con le informazioni personalizzate.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Per altre informazioni su come condividere risorse usando RBCA, vedere [Gestione dell'accesso usando RBCA e L'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="azure-image-builder"></a>Azure Image Builder

Azure offre anche un servizio, basato su Packer, ovvero [Image Builder per macchine virtuali di Azure](./image-builder-overview.md). È sufficiente descrivere le personalizzazioni in un modello per gestire la creazione dell'immagine tramite questo servizio. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'immagine di macchina virtuale personalizzata. Si è appreso come:

> [!div class="checklist"]
> * Creare una Raccolta immagini condivise
> * Creare una definizione dell'immagine
> * Creare una versione di immagine
> * Creare una macchina virtuale da un'immagine 
> * Condividere una raccolta di immagini

Passare all'esercitazione successiva per la descrizione delle macchine virtuali a disponibilità elevata.

> [!div class="nextstepaction"]
> [Creare VM a disponibilità elevata](tutorial-availability-sets.md)
