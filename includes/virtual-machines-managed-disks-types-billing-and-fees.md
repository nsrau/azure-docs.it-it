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
ms.openlocfilehash: 268467796e67caf2d20fedb44d83fd455a09b83e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147879"
---
**Trasferimenti di dati in uscita**: [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/bandwidth/) (dati in uscita dai datacenter di Azure) vengono fatturati in base all'utilizzo di larghezza di banda.

**Transazioni**: Viene addebitato il numero di transazioni eseguite in un disco gestito Standard. Per le unità SSD standard, ogni operazione dei / o minore o uguale a 256 KB di velocità effettiva viene considerato una singola operazione dei / o. Operazioni dei / o superiore a 256 KB di velocità effettiva sono considerate diversi i/o di dimensione 256 KB. Per unità disco rigido Standard, ogni operazione dei / o viene considerato come una singola transazione, indipendentemente dalle dimensioni i/o.

Per informazioni dettagliate sui prezzi per Managed Disks, inclusi i costi delle transazioni, vedere [prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Tariffa di prenotazione delle macchine virtuali con unità SSD Ultra

Le macchine virtuali di Azure hanno la possibilità di indicare se sono compatibili con le unità Ultra SSD. Una macchina virtuale compatibile con dischi Ultra alloca una capacità di larghezza di banda dedicata tra l'istanza della macchina virtuale di calcolo e l'unità di scala di archiviazione a blocchi per ottimizzare le prestazioni e ridurre la latenza. L'aggiunta di questa capacità alla macchina virtuale comporta un addebito per la prenotazione, che viene applicato solo se si abilita la funzionalità per i dischi Ultra nella macchina virtuale senza collegare un disco Ultra. Quando alla macchina virtuale compatibile con i dischi Ultra viene collegato un disco Ultra, questo addebito non viene applicato. L'addebito viene calcolato per ogni vCPU di cui è stato effettuato il provisioning nella macchina virtuale.

Vedere la [pagina dei prezzi dei dischi di Azure](https://azure.microsoft.com/pricing/details/managed-disks/) per dettagli sui prezzi dei dischi Ultra.