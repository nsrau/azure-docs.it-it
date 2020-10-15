---
title: Usare l'interfaccia della riga di comando per distribuire VM di Azure
description: Informazioni su come usare l'interfaccia della riga di comando per distribuire macchine virtuali di Azure spot per ridurre i costi.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: e3a48fa9f84a047a08e73bbaa239563dca541541
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978366"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Distribuire macchine virtuali con l'interfaccia della riga di comando di Azure

L'uso di [macchine virtuali di Azure spot](../spot-vms.md) consente di sfruttare la capacità inutilizzata a un notevole risparmio sui costi. Quando, in qualsiasi momento, Azure avrà di nuovo bisogno di quella capacità, l'infrastruttura di Azure rimuoverà le macchine virtuali spot. Le macchine virtuali spot sono pertanto ideali per i carichi di lavoro in grado di gestire le interruzioni, come i processi di elaborazione batch, gli ambienti di sviluppo/test, i carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali spot variano in base all'area e allo SKU. Per altre informazioni, vedere i prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

È possibile impostare un prezzo massimo che si è disposti a pagare, per ora, per la macchina virtuale. Il prezzo massimo per una VM spot può essere impostato in dollari USA (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo su `-1`, la macchina virtuale non verrà eliminata in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per spot o al prezzo di una macchina virtuale standard, che sempre è inferiore, purché siano disponibili capacità e quota. Per altre informazioni sull'impostazione del prezzo massimo, vedere [spot VM-prezzi](../spot-vms.md#pricing).

Il processo di creazione di una macchina virtuale con l'interfaccia della riga di comando di Azure è identico a quello descritto nell' [articolo introduttivo](./quick-create-cli.md). È sufficiente aggiungere il parametro '--Priority spot ', impostare `--eviction-policy` su deallocate (impostazione predefinita) o `Delete` e specificare un prezzo massimo o `-1` . 


## <a name="install-azure-cli"></a>Installare l'interfaccia da riga di comando di Azure

Per creare macchine virtuali spot, è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.74 o successiva. Eseguire **az --version** per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

Accedere ad Azure tramite [az login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Creare una VM spot

Questo esempio Mostra come distribuire una VM Linux spot che non verrà rimossa in base al prezzo. I criteri di rimozione sono impostati per deallocare la macchina virtuale, in modo che possa essere riavviata in un secondo momento. Se si vuole eliminare la macchina virtuale e il disco sottostante quando la macchina virtuale viene rimossa, impostare `--eviction-policy` su `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Dopo aver creato la macchina virtuale, è possibile eseguire una query per visualizzare il prezzo di fatturazione massimo per tutte le macchine virtuali nel gruppo di risorse.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simulare un'eliminazione

È possibile [simulare un'eliminazione](/rest/api/compute/virtualmachines/simulateeviction) di una macchina virtuale spot, per testare il modo in cui l'applicazione effettuerà il ristagno a una rimozione improvvisa. 

Sostituire quanto segue con le informazioni: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**Passaggi successivi**

È anche possibile creare una VM spot usando [Azure PowerShell](../windows/spot-powershell.md), il [portale](../spot-portal.md)o un [modello](spot-template.md).

Eseguire query sulle informazioni sui prezzi correnti usando l' [API prezzi di vendita al dettaglio di Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) per informazioni sui prezzi spot. `meterName`E `skuName` conterranno entrambi `Spot` .

Se si verifica un errore, vedere [codici di errore](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).