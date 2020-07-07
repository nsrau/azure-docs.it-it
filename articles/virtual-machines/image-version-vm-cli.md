---
title: Creare un'immagine da una macchina virtuale
description: Informazioni su come creare un'immagine in una raccolta di immagini condivise da una macchina virtuale in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f53a6b63c744b0e3e41f7ad22270cd842da57674
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796577"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Creare una versione di immagine da una macchina virtuale in Azure tramite l'interfaccia della riga di comando di Azure

Se è presente una macchina virtuale esistente che si vuole usare per creare più macchine virtuali identiche, è possibile usare tale macchina virtuale per creare un'immagine in una raccolta di immagini condivise usando l'interfaccia della riga di comando di Azure. È anche possibile creare un'immagine da una macchina virtuale usando [Azure PowerShell](image-version-vm-powershell.md).

Una **versione dell'immagine** è quella che si usa per creare una macchina virtuale quando si usa una raccolta di immagini condivise. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Quando si usa una versione di immagine per creare una macchina virtuale, la versione dell'immagine viene usata per creare i dischi per la nuova macchina virtuale. Le versioni delle immagini possono essere usate più volte.


## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, è necessario disporre di una raccolta di immagini condivise esistente. 

È inoltre necessario disporre di una macchina virtuale esistente in Azure, nella stessa area della raccolta. 

Se la macchina virtuale dispone di un disco dati collegato, le dimensioni del disco dati non possono superare 1 TB.

Quando si lavora in questo articolo, sostituire i nomi delle risorse laddove necessario.

## <a name="get-information-about-the-vm"></a>Ottenere informazioni sulla VM

Per visualizzare un elenco delle macchine virtuali disponibili, usare [az vm list](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Quando si conosce il nome della macchina virtuale e il gruppo di risorse in cui si trova, per ottenere l'ID della macchina virtuale usare [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine

Le definizioni di immagini creano un raggruppamento logico per le immagini. Vengono usate per gestire le informazioni sulle versioni di immagini create al loro interno. 

I nomi delle definizioni di immagini possono essere costituiti da lettere maiuscole o minuscole, numeri, trattini e punti. 

Assicurarsi che la definizione di immagine sia del tipo corretto. Se la VM è stata generalizzata, usando Sysprep per Windows o waagent -deprovision per Linux, è necessario creare una definizione di immagine generalizzata usando `--os-state generalized`. Se si vuole usare la VM senza rimuovere gli account utente esistenti, creare una definizione di immagine specializzata usando `--os-state specialized`.

Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Creare una definizione di immagine nella raccolta usando [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

In questo esempio la definizione di immagine è denominata *myImageDefinition* ed è relativa a un'immagine [specializzata](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) del sistema operativo Linux. Per creare una definizione per le immagini usando un sistema operativo Windows, usare `--os-type Windows`. 

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
> Quando si crea la versione dell'immagine, è anche possibile archiviare l'immagine nell'archiviazione Premium, aggiungendo `--storage-account-type  premium_lrs`, oppure nell'[archiviazione con ridondanza della zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs), aggiungendo `--storage-account-type  standard_zrs`.
>

## <a name="next-steps"></a>Passaggi successivi

Creare una VM dall' [immagine generalizzata](vm-generalized-image-version-cli.md) usando l'interfaccia della riga di comando di Azure.
