---
title: Usare l'interfaccia della riga di comando per distribuire macchine virtuali di Azure spot (anteprima)
description: Informazioni su come usare l'interfaccia della riga di comando per distribuire macchine virtuali di Azure spot per ridurre i costi.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 0635be14937a3688792f65208dcb9d482b9e6d44
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781995"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Anteprima: distribuire le VM spot usando l'interfaccia della riga di comando di Azure

L'uso di [macchine virtuali di Azure spot](spot-vms.md) consente di sfruttare la capacità inutilizzata a un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le VM spot. Quindi, le VM spot sono ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le VM spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere prezzi delle VM per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

È possibile impostare un prezzo massimo che si è disposti a pagare, per ora, per la macchina virtuale. Il prezzo massimo per una VM spot può essere impostato in dollari USA (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di $0,98765 USD all'ora. Se si imposta il prezzo massimo da `-1`, la macchina virtuale non verrà rimossa in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per spot o al prezzo di una macchina virtuale standard, che sempre è inferiore, purché siano disponibili capacità e quota. Per altre informazioni sull'impostazione del prezzo massimo, vedere [spot VM-prezzi](spot-vms.md#pricing).

Il processo di creazione di una macchina virtuale con l'interfaccia della riga di comando di Azure è identico a quello descritto nell' [articolo introduttivo](/azure/virtual-machines/linux/quick-create-cli). È sufficiente aggiungere il parametro '--Priority spot ' e specificare un prezzo massimo o `-1`.

> [!IMPORTANT]
> Le istanze di spot sono attualmente in anteprima pubblica.
> Questa versione di anteprima non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Per la prima parte dell'anteprima pubblica, le istanze di spot avranno un prezzo fisso, quindi non vi saranno eliminazioni basate sul prezzo.


## <a name="install-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Per creare macchine virtuali spot, è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.74 o successiva. Eseguire **az --version** per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

Accedere ad Azure tramite [az login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Creare una VM spot

Questo esempio Mostra come distribuire una VM Linux spot che non verrà rimossa in base al prezzo. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

Dopo aver creato la macchina virtuale, è possibile eseguire una query per visualizzare il prezzo di fatturazione massimo per tutte le macchine virtuali nel gruppo di risorse.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Passaggi successivi**

È anche possibile creare una VM spot usando [Azure PowerShell](../windows/spot-powershell.md) o un [modello](spot-template.md).

Se si verifica un errore, vedere [codici di errore](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
