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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471696"
---
Durante l'anteprima, i dischi gestiti con dischi condivisi abilitati sono soggetti alle limitazioni seguenti:

- Attualmente disponibile solo con SSD premium.
- Attualmente supportato solo nella regione degli Stati Uniti centro-occidentali.
- Tutte le macchine virtuali che condividono un disco devono essere distribuite negli stessi gruppi di posizionamento di [prossimità.](../articles/virtual-machines/windows/proximity-placement-groups.md)
- Può essere abilitato solo su dischi dati, non su dischi del sistema operativo.
- Solo i dischi di base possono essere utilizzati con alcune versioni del cluster di failover di Windows Server, per informazioni dettagliate vedere Requisiti hardware e opzioni di archiviazione del [clustering](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)di failover .
- La memorizzazione nella cache dell'host ReadOnly `maxShares>1`non è disponibile per gli SSD premium con .
- I set di disponibilità e i `FaultDomainCount` set di scalabilità delle macchine virtuali possono essere usati solo con impostato su 1.Availability sets and virtual machine scale sets can only be used with to 1.
- Il supporto di Backup di Azure e Azure Site Recovery non è ancora disponibile.

Se sei interessato a provare i dischi condivisi, [iscriviti per la nostra anteprima.](https://aka.ms/AzureSharedDiskPreviewSignUp)
