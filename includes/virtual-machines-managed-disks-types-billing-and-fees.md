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
ms.openlocfilehash: 3e303ce2b6f28500406bacf5b66d26f9c78ba46d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618039"
---
**Trasferimenti di dati in uscita**: [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/bandwidth/) (dati in uscita dai datacenter di Azure) vengono fatturati in base all'utilizzo di larghezza di banda.

**Transazioni**: Viene addebitato il numero di transazioni eseguite in un disco gestito Standard.

Per informazioni dettagliate sui prezzi per Managed Disks, inclusi i costi delle transazioni, vedere [prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Tariffa di prenotazione delle macchine virtuali con unità SSD Ultra

Le macchine virtuali di Azure hanno la possibilità di indicare se sono compatibili con le unità Ultra SSD. Una macchina virtuale compatibile con dischi Ultra alloca una capacità di larghezza di banda dedicata tra l'istanza della macchina virtuale di calcolo e l'unità di scala di archiviazione a blocchi per ottimizzare le prestazioni e ridurre la latenza. L'aggiunta di questa capacità alla macchina virtuale comporta un addebito per la prenotazione, che viene applicato solo se si abilita la funzionalità per i dischi Ultra nella macchina virtuale senza collegare un disco Ultra. Quando alla macchina virtuale compatibile con i dischi Ultra viene collegato un disco Ultra, questo addebito non viene applicato. L'addebito viene calcolato per ogni vCPU di cui è stato effettuato il provisioning nella macchina virtuale.

Vedere la [pagina dei prezzi dei dischi di Azure](https://azure.microsoft.com/pricing/details/managed-disks/) per dettagli sui prezzi dei dischi Ultra.