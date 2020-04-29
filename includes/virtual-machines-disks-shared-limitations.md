---
title: includere il file
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008346"
---
Durante l'anteprima, l'abilitazione dei dischi condivisi è disponibile solo per un subset di tipi di disco. Attualmente solo dischi e SSD Premium possono abilitare i dischi condivisi. Ogni disco gestito con dischi condivisi abilitati è soggetto alle limitazioni seguenti, organizzati in base al tipo di disco:

### <a name="ultra-disks"></a>Dischi Ultra

I dischi Ultra includono un elenco separato di limitazioni, non correlate ai dischi condivisi. Per le limitazioni del disco Ultra, vedere [uso di dischi ultra di Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Quando si condividono dischi Ultra, presentano le seguenti limitazioni aggiuntive:

- Attualmente supportato solo negli Stati Uniti occidentali.
- Attualmente limitato al supporto Azure Resource Manager o SDK.
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server. per informazioni dettagliate, vedere [requisiti hardware per il clustering di failover e opzioni di archiviazione](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>SSD Premium

- Attualmente supportato solo nell'area Stati Uniti centro-occidentali.
- Tutte le macchine virtuali che condividono un disco devono essere distribuite negli stessi [gruppi di posizionamento di prossimità](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Può essere abilitato solo sui dischi dati, non sui dischi del sistema operativo.
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server. per informazioni dettagliate, vedere [requisiti hardware per il clustering di failover e opzioni di archiviazione](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- La memorizzazione nella cache dell'host ReadOnly non è disponibile per `maxShares>1`le unità SSD Premium con.
- I set di disponibilità e i set di scalabilità di macchine `FaultDomainCount` virtuali possono essere usati solo con impostato su 1.
- Il supporto di backup di Azure e Azure Site Recovery non è ancora disponibile.

Se si è interessati a provare i dischi condivisi, [iscriversi per l'anteprima](https://aka.ms/AzureSharedDiskPreviewSignUp).
