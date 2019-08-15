---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: a98a941477fc83a104b55ed91f457c5a48f90d59
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029807"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate

Con un'istanza di macchina virtuale riservata ottimizzata per la flessibilità delle dimensioni dell'istanza, la prenotazione acquistata può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di flessibilità delle dimensioni dell'istanza. Se ad esempio si acquista una prenotazione per le dimensioni di una macchina virtuale elencate nella serie DSv2, ad esempio Standard_DS5_v2, lo sconto di prenotazione può essere applicato alle altre quattro dimensioni elencate nello stesso gruppo di flessibilità delle dimensioni dell'istanza:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Lo sconto per le prenotazioni non si applica tuttavia alle dimensioni delle VM elencate in gruppi di flessibilità delle dimensioni dell'istanza diversi, ad esempio gli SKU nella memoria alta della serie DSv2: Standard_DS11_v2, Standard_DS12_v2 e così via.

All'interno del gruppo di flessibilità delle dimensioni dell'istanza, il numero di macchine virtuali a cui si applica lo sconto della prenotazione dipende dalle dimensioni della macchina virtuale selezionate quando si acquista una prenotazione. Dipende anche dalle dimensioni delle macchine virtuali in esecuzione. La colonna ratio confronta il footprint relativo per ogni dimensione di macchina virtuale in tale gruppo di flessibilità delle dimensioni dell'istanza. Usare il valore di rapporto per calcolare come viene applicato lo sconto per la prenotazione alle macchine virtuali in esecuzione.

## <a name="examples"></a>Esempi

Gli esempi seguenti usano le dimensioni e i rapporti nella tabella della serie DSv2.

Si acquista un'istanza di macchina virtuale riservata con le dimensioni Standard_DS4_v2, con il rapporto (o footprint relativo) pari a 8 rispetto alle altre dimensioni nella serie.

- Scenario 1: Eseguire otto macchine virtuali con dimensioni Standard_DS1_v2 con un rapporto pari a 1. Lo sconto per la prenotazione si applica a tutte e otto le macchine virtuali.
- Scenario 2: Eseguire due macchine virtuali con dimensioni Standard_DS2_v2 ciascuna con un rapporto pari a 2. Eseguire anche una macchina virtuale con dimensioni Standard_DS3_v2 con un rapporto pari a 4. Il footprint complessivo è 2+2+4=8. Pertanto, lo sconto per la prenotazione si applica a tutte e tre le macchine virtuali.
- Scenario 3: Eseguire una macchina virtuale Standard_DS5_v2 con un rapporto di 16. Lo sconto per la prenotazione si applica a metà del costo di calcolo di tale macchina virtuale.

Le sezioni seguenti mostrano quali dimensioni sono incluse nello stesso gruppo di serie di dimensioni quando si acquista un'istanza di macchina virtuale riservata ottimizzata per la flessibilità di dimensioni dell'istanza.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Rapporto di flessibilità delle dimensioni dell'istanza per le macchine virtuali 

Il volume CSV seguente include i gruppi di flessibilità delle dimensioni dell'istanza, ArmSkuName e i rapporti.  

[Rapporti di flessibilità delle dimensioni dell'istanza](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

L'URL del file e lo schema vengono mantenuti corretti per poter utilizzare questo file a livello di codice. I dati saranno disponibili anche tramite l'API a breve.
