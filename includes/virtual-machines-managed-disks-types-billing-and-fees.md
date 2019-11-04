---
title: file di inclusione
description: file di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73412979"
---
**Trasferimenti di dati in uscita**: i [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/bandwidth/) (dati in uscita dai data center di Azure) vengono fatturati in base all'uso della larghezza di banda.

**Transazioni**: viene addebitato il numero di transazioni eseguite su un disco gestito standard. Per le unità SSD standard, ogni operazione di I/O inferiore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O. Le operazioni di i/O superiori a 256 KiB di velocità effettiva sono considerate più I/O di dimensioni 256 KiB. Per i dischi rigidi standard, ogni operazione di I/O viene considerata come una singola transazione, indipendentemente dalle dimensioni di I/O.

Per informazioni dettagliate sui prezzi per Managed Disks, inclusi i costi delle transazioni, vedere [prezzi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Costo prenotazione macchina virtuale ultra disk

Le macchine virtuali di Azure sono in grado di indicare se sono compatibili con dischi Ultra. Una macchina virtuale compatibile con dischi Ultra alloca una capacità di larghezza di banda dedicata tra l'istanza della macchina virtuale di calcolo e l'unità di scala di archiviazione a blocchi per ottimizzare le prestazioni e ridurre la latenza. L'aggiunta di questa capacità alla macchina virtuale comporta un addebito per la prenotazione, che viene applicato solo se si abilita la funzionalità per i dischi Ultra nella macchina virtuale senza collegare un disco Ultra. Quando alla macchina virtuale compatibile con i dischi Ultra viene collegato un disco Ultra, questo addebito non viene applicato. L'addebito viene calcolato per ogni vCPU di cui è stato effettuato il provisioning nella macchina virtuale. 

> [!Note]
> Per le [dimensioni delle VM Core vincolate](../articles/virtual-machines/linux/constrained-vcpu.md), la tariffa di prenotazione è basata sul numero effettivo di vCPU e non sui core vincolati. Per Standard_E32-8s_v3, la tariffa di prenotazione sarà basata su 32 core. 

Per informazioni sui prezzi di dischi Ultra, vedere la [pagina dei prezzi di Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/) .

### <a name="azure-disk-reservation"></a>Prenotazione dischi di Azure

La prenotazione del disco è la possibilità di acquistare un anno di spazio di archiviazione su disco in anticipo a uno sconto, riducendo i costi totali. Quando si acquista una prenotazione su disco, si seleziona uno SKU del disco specifico in un'area di destinazione, ad esempio 10 unità SSD Premium P30 (1TiB) nell'area Stati Uniti orientali 2 per un periodo di un anno. L'esperienza di prenotazione è simile alle istanze di macchina virtuale (VM) riservate. È possibile aggregare le prenotazioni di macchine virtuali e dischi per ottimizzare il risparmio. Per il momento, la prenotazione dei dischi di Azure offre un piano di impegno annuale per gli SKU SSD Premium da P30 (1TiB) a P80 (32 TiB) in tutte le aree di produzione. Per altri dettagli sui prezzi dei dischi riservati, vedere la [pagina prezzi di dischi di Azure](https://azure.microsoft.com/pricing/details/managed-disks/).