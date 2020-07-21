---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d2cf7dbcd97c8f740447607eaf443bc3ea4a6733
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500599"
---
L'abilitazione dei dischi condivisi è disponibile solo per un subset di tipi di disco. Attualmente solo dischi e SSD Premium possono abilitare i dischi condivisi. Ogni disco gestito con dischi condivisi abilitati è soggetto alle limitazioni seguenti, organizzati in base al tipo di disco:

### <a name="ultra-disks"></a>Dischi Ultra

I dischi Ultra includono un elenco separato di limitazioni, non correlate ai dischi condivisi. Per le limitazioni del disco Ultra, vedere [uso di dischi ultra di Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Quando si condividono dischi Ultra, presentano le seguenti limitazioni aggiuntive:

- Attualmente limitato al supporto Azure Resource Manager o SDK. 
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server. per informazioni dettagliate, vedere [requisiti hardware per il clustering di failover e opzioni di archiviazione](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>SSD Premium

- Attualmente supportato solo nell'area Stati Uniti centro-occidentali.
- Attualmente limitato al supporto Azure Resource Manager o SDK. 
- Può essere abilitato solo sui dischi dati, non sui dischi del sistema operativo.
- La memorizzazione nella cache dell'host **ReadOnly** non è disponibile per le unità SSD Premium con `maxShares>1` .
- L'espansione del disco non è disponibile per SSD Premium con `maxShares>1` .
- Quando si usano i set di disponibilità e i set di scalabilità di macchine virtuali con i dischi condivisi di Azure, l' [allineamento del dominio di errore di archiviazione](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) con il dominio di errore della macchina virtuale non viene applicato per il disco dati condiviso.
- Quando si usano i [gruppi di posizionamento prossimità (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md), tutte le macchine virtuali che condividono un disco devono far parte dello stesso PPG.
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server. per informazioni dettagliate, vedere [requisiti hardware per il clustering di failover e opzioni di archiviazione](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Il supporto di backup di Azure e Azure Site Recovery non è ancora disponibile.

Se si è interessati a provare i dischi condivisi, [iscriversi per l'accesso](https://aka.ms/AzureSharedDiskGASignUp).