---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/21/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cdee82ddae7f6edf43765063bb610b743dbf0121
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88809802"
---
L'abilitazione dei dischi condivisi è disponibile solo per un subset di tipi di disco. Attualmente solo dischi e SSD Premium possono abilitare i dischi condivisi. Ogni disco gestito con dischi condivisi abilitati è soggetto alle limitazioni seguenti, organizzati in base al tipo di disco:

### <a name="ultra-disks"></a>Dischi Ultra

I dischi Ultra includono un elenco separato di limitazioni, non correlate ai dischi condivisi. Per le limitazioni del disco Ultra, vedere [uso di dischi ultra di Azure](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Quando si condividono dischi Ultra, presentano le seguenti limitazioni aggiuntive:

- Attualmente limitato al supporto Azure Resource Manager o SDK. 
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server. per informazioni dettagliate, vedere [requisiti hardware per il clustering di failover e opzioni di archiviazione](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

I dischi Ultra condivisi sono disponibili in tutte le aree che supportano i dischi ultra per impostazione predefinita e non richiedono l'iscrizione per l'uso.

### <a name="premium-ssds"></a>SSD Premium

- Attualmente supportato solo in [un subset di aree](#regional-availability).
- Attualmente limitato al supporto Azure Resource Manager o SDK. 
- Può essere abilitato solo sui dischi dati, non sui dischi del sistema operativo.
- La memorizzazione nella cache dell'host **ReadOnly** non è disponibile per le unità SSD Premium con `maxShares>1` .
- L'espansione del disco non è disponibile per SSD Premium con `maxShares>1` .
- Quando si usano i set di disponibilità e i set di scalabilità di macchine virtuali con i dischi condivisi di Azure, l' [allineamento del dominio di errore di archiviazione](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) con il dominio di errore della macchina virtuale non viene applicato per il disco dati condiviso.
- Quando si usano i [gruppi di posizionamento prossimità (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md), tutte le macchine virtuali che condividono un disco devono far parte dello stesso PPG.
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server. per informazioni dettagliate, vedere [requisiti hardware per il clustering di failover e opzioni di archiviazione](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Il supporto di backup di Azure e Azure Site Recovery non è ancora disponibile.

#### <a name="regional-availability"></a>Disponibilità a livello di area

Le unità SSD Premium condivise sono supportate solo nelle aree geografiche seguenti:

- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Stati Uniti centro-occidentali
- Stati Uniti centro-meridionali
- Stati Uniti centrali
- Corea centrale
- Canada centrale
- Canada orientale
- US Gov Virginia
- US Gov Arizona

Se si è interessati a provare unità SSD Premium condivise, [iscriversi per l'accesso](https://aka.ms/AzureSharedDiskGASignUp).