---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008346"
---
Durante l'anteprima, l'abilitazione dei dischi condivisi è disponibile solo per un sottoinsieme di tipi di disco. Attualmente solo i dischi ultra e gli SSD premium possono abilitare i dischi condivisi. Ogni disco gestito con dischi condivisi abilitati è soggetto alle limitazioni seguenti, organizzate per tipo di disco:

### <a name="ultra-disks"></a>Dischi Ultra

I dischi ultra hanno un proprio elenco separato di limitazioni, non correlate ai dischi condivisi. Per le limitazioni dei dischi ultra, vedere [Uso dei dischi ultra](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)di Azure .

Quando si condividono dischi ultra, questi hanno le seguenti limitazioni aggiuntive:

- Attualmente supportato solo negli Stati Uniti occidentali.
- Attualmente limitato al supporto di Azure Resource Manager o SDK.
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server, per informazioni dettagliate vedere Requisiti hardware e opzioni di archiviazione del [clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)di failover .

### <a name="premium-ssds"></a>SSD Premium

- Attualmente supportato solo nella regione degli Stati Uniti centro-occidentali.
- Tutte le macchine virtuali che condividono un disco devono essere distribuite negli stessi gruppi di posizionamento di [prossimità.](../articles/virtual-machines/windows/proximity-placement-groups.md)
- Può essere abilitato solo su dischi dati, non su dischi del sistema operativo.
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server, per informazioni dettagliate vedere Requisiti hardware e opzioni di archiviazione del [clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)di failover .
- La memorizzazione nella cache dell'host ReadOnly `maxShares>1`non è disponibile per gli SSD premium con .
- I set di disponibilità e i `FaultDomainCount` set di scalabilità delle macchine virtuali possono essere usati solo con impostato su 1.Availability sets and virtual machine scale sets can only be used with to 1.
- Il supporto di Backup di Azure e Azure Site Recovery non è ancora disponibile.

Se sei interessato a provare i dischi condivisi, [iscriviti per la nostra anteprima.](https://aka.ms/AzureSharedDiskPreviewSignUp)
