---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73412979"
---
**Trasferimenti di dati in uscita**: i [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/bandwidth/) (dati in uscita dai data center di Azure) vengono fatturati in base all'uso della larghezza di banda.

**Transazioni**: Viene addebitato il numero di transazioni eseguite su un disco gestito standard. Per gli SSD standard, ogni operazione di I/O minore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O.For standard SSDs, each I/O operation less than or equal to 256 KiB of throughput is considered a single I/O operation. Le operazioni di I/O superiori a 256 KiB di velocità effettiva sono considerate operazioni di I/O multiple della dimensione 256 KiB. Per i dischi rigidi standard, ogni operazione di I/O viene considerata come una singola transazione, indipendentemente dalle dimensioni dei / o.

Per informazioni dettagliate sui prezzi per i dischi gestiti, inclusi i costi delle [transazioni, vedere Determinazione dei prezzi dei dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Tariffa di prenotazione VM ultra disco

Le macchine virtuali di Azure hanno la possibilità di indicare se sono compatibili con i dischi ultra. Una macchina virtuale compatibile con dischi Ultra alloca una capacità di larghezza di banda dedicata tra l'istanza della macchina virtuale di calcolo e l'unità di scala di archiviazione a blocchi per ottimizzare le prestazioni e ridurre la latenza. L'aggiunta di questa capacità alla macchina virtuale comporta un addebito per la prenotazione, che viene applicato solo se si abilita la funzionalità per i dischi Ultra nella macchina virtuale senza collegare un disco Ultra. Quando alla macchina virtuale compatibile con i dischi Ultra viene collegato un disco Ultra, questo addebito non viene applicato. L'addebito viene calcolato per ogni vCPU di cui è stato effettuato il provisioning nella macchina virtuale. 

> [!Note]
> Per [le dimensioni delle macchine virtuali core vincolate,](../articles/virtual-machines/linux/constrained-vcpu.md)la commissione di prenotazione si basa sul numero effettivo di vCPU e non sui core vincolati. Per Standard_E32 8s_v3, la quota di prenotazione sarà basata su 32 core. 

Fare riferimento alla pagina dei prezzi di [Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) per informazioni sui prezzi ultra disco.

### <a name="azure-disk-reservation"></a>Prenotazione disco di AzureAzure disk reservation

Prenotazione disco è la possibilità di acquistare un anno di spazio di archiviazione su disco in anticipo con uno sconto, riducendo il costo totale. Quando si acquista una prenotazione su disco, si seleziona uno SKU disco specifico in un'area di destinazione, ad esempio 10 P30 (1TiB) SSD premium nell'area Stati Uniti orientali 2 per un periodo di un anno. L'esperienza di prenotazione è simile alle istanze di macchine virtuali (VM) riservate. È possibile raggruppare le prenotazioni di macchine virtuali e disco per ottimizzare il risparmio. Per il momento, la prenotazione di dischi di Azure offre un piano di impegno di un anno per SKU SSD premium da P30 (1TiB) a P80 (32 TiB) in tutte le aree di produzione. Per altre informazioni sui prezzi dei dischi riservati, vedere La pagina dei prezzi dei dischi di Azure.For more details on the Reserved [Disks pricing, see Azure Disks pricing page.](https://azure.microsoft.com/pricing/details/managed-disks/)