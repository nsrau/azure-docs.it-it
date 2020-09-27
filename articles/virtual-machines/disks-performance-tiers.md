---
title: Modificare le prestazioni di Azure Managed Disks
description: Informazioni sui livelli di prestazioni per Managed disks e su come modificare i livelli di prestazioni per i dischi gestiti esistenti.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7da500c3f18b7bf7057b0c5875bc9b39136a6483
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396587"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Livelli di prestazioni per Managed Disks (anteprima)

Archiviazione su disco di Azure attualmente offre funzionalità di espansione predefinite per ottenere prestazioni più elevate per la gestione di traffico imprevisto a breve termine. Le unità SSD Premium sono in grado di aumentare le prestazioni del disco senza aumentare le dimensioni effettive del disco, consentendo di soddisfare le esigenze di prestazioni del carico di lavoro e di ridurre i costi. questa funzionalità è attualmente in anteprima. Questa soluzione è ideale per gli eventi che richiedono temporaneamente un livello di prestazioni costantemente più elevato, ad esempio l'acquisto di festività, il test delle prestazioni o l'esecuzione di un ambiente di training. Per gestire questi eventi, è possibile selezionare un livello di prestazioni superiore purché necessario e tornare al livello originale quando le prestazioni aggiuntive non sono più necessarie.

## <a name="how-it-works"></a>Come funziona

Quando si distribuisce o si effettua il provisioning di un disco per la prima volta, il livello di prestazioni di base per tale disco viene impostato in base alle dimensioni del disco con provisioning È possibile selezionare un livello di prestazioni superiore per soddisfare una domanda più elevata e, quando le prestazioni non sono più necessarie, è possibile tornare al livello di prestazioni iniziale della linea di base.

Le modifiche apportate alla fatturazione cambiano a seconda del livello. Se, ad esempio, si esegue il provisioning di un disco P10 (128 GiB), il livello di prestazioni della linea di base è impostato su P10 (500 IOPS e 100 MB/s) e verrà fatturato in base alla tariffa P10. È possibile aggiornare il livello in modo che corrisponda alle prestazioni di P50 (7500 IOPS e 250 MB/s) senza aumentare le dimensioni del disco, durante i quali verrà addebitata la tariffa di P50. Quando le prestazioni più elevate non sono più necessarie, è possibile tornare al livello P10 e il disco verrà nuovamente fatturato in base alla tariffa P10.

| Dimensioni del disco | Livello di prestazioni di base | Può essere aggiornato a |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | nessuno |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | nessuno |

Per informazioni sulla fatturazione, vedere [prezzi dei dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Restrizioni

- Attualmente supportato solo per le unità SSD Premium.
- Prima di modificare i livelli, i dischi devono essere scollegati da una macchina virtuale in esecuzione.
- L'uso dei livelli di prestazioni P60, p70 e P80 è limitato ai dischi di 4096 GiB o versione successiva.
- Il livello di prestazioni di un disco può essere modificato una sola volta ogni 24 ore.

## <a name="regional-availability"></a>Disponibilità a livello di area

La regolazione del livello di prestazioni di un disco gestito è attualmente disponibile solo per le unità SSD Premium nelle aree seguenti:

- Stati Uniti centro-occidentali 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Creare un disco dati vuoto con un livello superiore rispetto al livello di base

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Creare un disco del sistema operativo con un livello superiore rispetto al livello di base da un'immagine di Azure Marketplace

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>Aggiornare il livello di un disco

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>Mostra il livello di un disco

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Passaggi successivi

Se è necessario ridimensionare un disco per sfruttare i livelli di prestazioni più elevati, vedere gli articoli sull'argomento:

- [Espandere dischi rigidi virtuali in una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](linux/expand-disks.md)
- [Espandere un disco gestito collegato a una macchina virtuale Windows](windows/expand-os-disk.md)
