---
title: Usare l'interfaccia della riga di comando per distribuire macchine virtuali di Azure Spot (anteprima)Use CLI to deploy Azure Spot VMs (Preview)
description: Informazioni su come usare l'interfaccia della riga di comando per distribuire macchine virtuali di Azure Spot per risparmiare sui costi.
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
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77163093"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Anteprima: Distribuire macchine virtuali spot usando l'interfaccia della riga di comando di AzurePreview: Deploy Spot VMs using the Azure CLI

L'uso delle [macchine virtuali Di Azure Spot](spot-vms.md) consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento in cui Azure richiede la capacità, l'infrastruttura di Azure rimuovere le macchine virtuali Spot.At any in time when Azure needs the capacity back, the Azure infrastructure will evict Spot VMs. Pertanto, le macchine virtuali Spot sono ideali per i carichi di lavoro in grado di gestire interruzioni come processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di elaborazione di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali Spot sono variabili, in base all'area e all'SKU. Per altre informazioni, vedere Prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 

È possibile impostare un prezzo massimo che si è disposti a pagare, all'ora, per la macchina virtuale. Il prezzo massimo per una macchina virtuale Spot può essere impostato in dollari USA (USD), usando fino a 5 cifre decimali. Ad esempio, `0.98765`il valore sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il `-1`prezzo massimo su , la macchina virtuale non verrà rimossa in base al prezzo. Il prezzo per la macchina virtuale sarà il prezzo corrente per Spot o il prezzo per una macchina virtuale standard, che è sempre inferiore, purché siano disponibili capacità e quote. Per altre informazioni sull'impostazione del prezzo massimo, vedere [Spot VMs - Pricing](spot-vms.md#pricing).

Il processo per creare una macchina virtuale con Spot usando l'interfaccia della riga di comando di Azure è lo stesso descritto [nell'articolo delle guide rapide.](/azure/virtual-machines/linux/quick-create-cli) Basta aggiungere il parametro '--priority Spot' `-1`e fornire un prezzo massimo o .

> [!IMPORTANT]
> Le istanze spot sono attualmente in anteprima pubblica.
> Questa versione di anteprima non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="install-azure-cli"></a>Installare l'interfaccia da riga di comando di Azure

Per creare macchine virtuali Spot, è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.74 o successiva. Eseguire **az --version** per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

Accedere ad Azure tramite [az login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Creare una macchina virtuale SpotCreate a Spot VM

In questo esempio viene illustrato come distribuire una macchina virtuale Linux Spot che non verrà rimossa in base al prezzo. 

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

Dopo aver creato la macchina virtuale, è possibile eseguire una query per visualizzare il prezzo massimo di fatturazione per tutte le macchine virtuali nel gruppo di risorse.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Passaggi successivi**

È anche possibile creare una macchina virtuale Spot usando [Azure PowerShell](../windows/spot-powershell.md) o un [modello.](spot-template.md)

Se si verifica un errore, vedere [Codici di errore](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
