---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471457"
---
Quando si acquista un'istanza di macchina virtuale riservata, è possibile scegliere di ottimizzare la flessibilità delle dimensioni dell'istanza o la priorità della capacità. Per altre informazioni sull'impostazione o la modifica dell'impostazione di ottimizzazione per le istanze di macchine virtuali riservate, vedere [Modificare l'impostazione di ottimizzazione per le istanze di macchine virtuali riservate.](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)

Con un'istanza di macchina virtuale riservata ottimizzata per la flessibilità delle dimensioni delle istanze, la prenotazione acquistata può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di flessibilità delle dimensioni dell'istanza. Ad esempio, se si acquista una prenotazione per una dimensione di macchina virtuale elencata nella serie DSv2, ad esempio Standard_DS5_v2, lo sconto sulla prenotazione può essere applicato alle altre quattro dimensioni elencate nello stesso gruppo di flessibilità delle dimensioni dell'istanza:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Ma tale sconto di prenotazione non si applica alle dimensioni delle macchine virtuali elencate in gruppi di flessibilità delle dimensioni di istanza diverse, ad esempio SKU in DSv2 serie High Memory: Standard_DS11_v2, Standard_DS12_v2 e così via.

All'interno del gruppo di flessibilità delle dimensioni dell'istanza, il numero di macchine virtuali a cui si applica lo sconto di prenotazione dipende dalle dimensioni della macchina virtuale scelte quando si acquista una prenotazione. Dipende anche dalle dimensioni delle macchine virtuali in esecuzione. La colonna ratio confronta il footprint relativo per ogni dimensione della macchina virtuale in quel gruppo di flessibilità delle dimensioni dell'istanza. Usare il valore di rapporto per calcolare come viene applicato lo sconto per la prenotazione alle macchine virtuali in esecuzione.

## <a name="examples"></a>Esempi

Gli esempi seguenti usano le dimensioni e i rapporti nella tabella della serie DSv2.

Si acquista un'istanza di macchina virtuale riservata con le dimensioni Standard_DS4_v2, con il rapporto (o footprint relativo) pari a 8 rispetto alle altre dimensioni nella serie.

- Scenario 1: eseguire otto macchine virtuali con dimensioni Standard_DS1_v2 con un rapporto pari a 1. Lo sconto per la prenotazione si applica a tutte e otto le macchine virtuali.
- Scenario 2: eseguire due macchine virtuali con dimensioni Standard_DS2_v2 con un rapporto pari a 2 ognuna. Eseguire anche una macchina virtuale con dimensioni Standard_DS3_v2 con un rapporto pari a 4. Il footprint complessivo è 2+2+4=8. Pertanto, lo sconto per la prenotazione si applica a tutte e tre le macchine virtuali.
- Scenario 3: eseguire una macchina virtuale Standard_DS5_v2 con un rapporto di 16. Lo sconto per la prenotazione si applica a metà del costo di calcolo di tale macchina virtuale.

Le sezioni seguenti mostrano quali dimensioni sono incluse nello stesso gruppo di serie di dimensioni quando si acquista un'istanza di macchina virtuale riservata ottimizzata per la flessibilità di dimensioni dell'istanza.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Rapporto di flessibilità delle dimensioni delle istanze per le macchine virtualiInstance size flexibility ratio for VMs 

CSV di seguito include i gruppi di flessibilità delle dimensioni dell'istanza, ArmSkuName e i rapporti.  

[Rapporti di flessibilità delle dimensioni delle istanze](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

L'URL del file e lo schema verranno mantenuti in modo da poter utilizzare questo file a livello di codice. I dati saranno presto disponibili anche tramite API.
