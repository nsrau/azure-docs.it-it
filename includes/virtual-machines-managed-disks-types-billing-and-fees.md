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
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443393"
---
**Trasferimenti di dati in uscita**: [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/bandwidth/) (dati in uscita dai datacenter di Azure) vengono fatturati in base all'utilizzo di larghezza di banda.

**Transazioni**: Viene addebitato il numero di transazioni eseguite in un disco gestito Standard. Per le unità HDD Standard, Azure addebita 0,0036 $ per 100.000 transazioni. Le transazioni includono le operazioni sia di lettura che di scrittura nello spazio di archiviazione.

Le unità SSD Standard usano dimensioni dell'unità di I/O di 256 KB. Se il volume dei dati trasferiti è inferiore a 256 KB, viene considerata una singola unità di I/O. Le dimensioni di I/O superiori vengono calcolate come più I/O da 256 KB. Ad esempio, 1.100 KB di I/O corrispondono a cinque unità di I/O.

Non sono previsti costi per le transazioni per un disco gestito Premium.

Per informazioni dettagliate sui prezzi di Managed Disks, vedere [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Tariffa di prenotazione delle macchine virtuali con unità SSD Ultra

Le macchine virtuali di Azure hanno la possibilità di indicare se sono compatibili con le unità Ultra SSD. Una macchina virtuale compatibile con dischi Ultra alloca una capacità di larghezza di banda dedicata tra l'istanza della macchina virtuale di calcolo e l'unità di scala di archiviazione a blocchi per ottimizzare le prestazioni e ridurre la latenza. L'aggiunta di questa capacità alla macchina virtuale comporta un addebito per la prenotazione, che viene applicato solo se si abilita la funzionalità per i dischi Ultra nella macchina virtuale senza collegare un disco Ultra. Quando alla macchina virtuale compatibile con i dischi Ultra viene collegato un disco Ultra, questo addebito non viene applicato. L'addebito viene calcolato per ogni vCPU di cui è stato effettuato il provisioning nella macchina virtuale.

Vedere la [pagina dei prezzi dei dischi di Azure](https://azure.microsoft.com/pricing/details/managed-disks/) per dettagli sui prezzi dei dischi Ultra.