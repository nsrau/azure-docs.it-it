---
title: Migliorare le prestazioni del disco gestito di Azure
description: Informazioni sui livelli di prestazioni per Managed disks e su come aggiornare i livelli di prestazioni per i dischi gestiti.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4ad0ce1aef45dc4126d3ce17c7093b885f1b8bd7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938015"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Livelli di prestazioni per Managed Disks (anteprima)

Archiviazione su disco di Azure attualmente offre funzionalità di espansione predefinite per ottenere prestazioni più elevate per la gestione di traffico imprevisto a breve termine. Le unità SSD Premium sono in grado di aumentare le prestazioni del disco senza aumentare le dimensioni effettive del disco, consentendo di soddisfare le esigenze di prestazioni del carico di lavoro e di ridurre i costi. Questa soluzione è ideale per gli eventi che richiedono temporaneamente un livello di prestazioni costantemente più elevato, ad esempio l'acquisto di festività, il test delle prestazioni o l'esecuzione di un ambiente di training. Per gestire questi eventi, è possibile selezionare un livello di prestazioni superiore purché necessario e tornare al livello originale quando le prestazioni aggiuntive non sono più necessarie.

## <a name="how-it-works"></a>Funzionamento

Quando si distribuisce o si effettua il provisioning di un disco per la prima volta, il livello di prestazioni di base per tale disco viene impostato in base alle dimensioni del disco con provisioning È possibile selezionare un livello di prestazioni superiore per soddisfare una domanda più elevata e, quando le prestazioni non sono più necessarie, è possibile tornare al livello di prestazioni iniziale della linea di base. Se, ad esempio, si esegue il provisioning di un disco P10 (128 GiB), il livello di prestazioni di base è impostato su P10 (500 IOPS e 100 MB/s). È possibile aggiornare il livello in modo che corrisponda alle prestazioni di P50 (7500 IOPS e 250 MB/s) senza aumentare le dimensioni del disco e tornare a P10 quando le prestazioni più elevate non sono più necessarie.

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

## <a name="restrictions"></a>Restrizioni

- Attualmente supportato solo per le unità SSD Premium.
- Prima di modificare i livelli, i dischi devono essere scollegati da una macchina virtuale in esecuzione.
- L'uso dei livelli di prestazioni P60, p70 e P80 è limitato ai dischi di 4096 GiB o versione successiva.

## <a name="regional-availability"></a>Disponibilità a livello di area

La regolazione del livello di prestazioni di un disco gestito è attualmente disponibile solo per le unità SSD Premium nelle aree seguenti:

- Stati Uniti centro-occidentali 
- Stati Uniti orientali 2 
- Europa occidentale
- Australia orientale 
- Australia sud-orientale 
- India meridionale

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Crea/aggiorna un disco dati con un livello superiore rispetto al livello di base

1. Creare un disco dati vuoto con un livello superiore rispetto al livello di base o aggiornare il livello di un disco in un livello superiore rispetto al livello di base utilizzando il modello di esempio [CreateUpdateDataDiskWithTier.js](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. Confermare il livello del disco

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>Crea/aggiorna un disco del sistema operativo con un livello superiore rispetto al livello di base

1. Creare un disco del sistema operativo da un'immagine del Marketplace o aggiornare il livello di un disco del sistema operativo superiore rispetto al livello di base usando il modello di esempio [CreateUpdateOSDiskWithTier.jsin](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. Confermare il livello del disco
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Passaggi successivi

Se è necessario ridimensionare un disco per sfruttare i livelli di prestazioni più elevati, vedere gli articoli sull'argomento:

- [Espandere dischi rigidi virtuali in una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](linux/expand-disks.md)
- [Espandere un disco gestito collegato a una macchina virtuale Windows](windows/expand-os-disk.md)