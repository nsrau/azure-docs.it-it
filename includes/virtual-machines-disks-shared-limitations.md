---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471696"
---
In anteprima, i dischi gestiti con dischi condivisi abilitati sono soggetti alle limitazioni seguenti:

- Attualmente disponibile solo con unità SSD Premium.
- Attualmente supportato solo nell'area Stati Uniti centro-occidentali.
- Tutte le macchine virtuali che condividono un disco devono essere distribuite negli stessi [gruppi di posizionamento di prossimità](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Può essere abilitato solo sui dischi dati, non sui dischi del sistema operativo.
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server. per informazioni dettagliate, vedere [requisiti hardware per il clustering di failover e opzioni di archiviazione](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- La memorizzazione nella cache dell'host ReadOnly non è disponibile per le unità SSD Premium con `maxShares>1`.
- I set di disponibilità e i set di scalabilità di macchine virtuali possono essere usati solo con `FaultDomainCount` impostati su 1.
- Il supporto di backup di Azure e Azure Site Recovery non è ancora disponibile.

Se si è interessati a provare i dischi condivisi, [iscriversi per l'anteprima](https://aka.ms/AzureSharedDiskPreviewSignUp).
