---
title: Manutenzione con mantenimento per macchine virtuali Windows in Azure | Microsoft Docs
description: Migrazione di macchine virtuali sul posto per aggiornamenti con mantenimento della memoria.
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 08da5407cc5ddceeba21a558dc0de1008a566bab
ms.lasthandoff: 04/03/2017


---



# <a name="vm-preserving-maintenance-in-place-vm-migration"></a>Manutenzione con mantenimento delle macchine virtuali (migrazione della macchina virtuale sul posto)

Sebbene la maggior parte degli aggiornamenti non abbia alcun impatto sulle macchine virtuali ospitate, vi sono casi in cui gli aggiornamenti ai componenti o ai servizi hanno un'interferenza minima sulle macchine virtuali in esecuzione (senza un riavvio completo della macchina virtuale).

Questi aggiornamenti vengono eseguiti con la tecnologia che consente la migrazione sul posto in tempo reale, anche detta aggiornamento con mantenimento della memoria. Durante l'aggiornamento dell'host, la macchina virtuale viene messa in pausa, mantenendo la memoria nella RAM, mentre l'ambiente host, ad esempio il sistema operativo sottostante, applica gli aggiornamenti e le patch necessari.
La macchina virtuale riprende poi l'esecuzione dopo un periodo di pausa di 30 secondi.
Dopo la ripresa, l’orologio della macchina virtuale viene sincronizzato automaticamente.

Non tutti gli aggiornamenti possono essere distribuiti utilizzando questo meccanismo, ma dato il breve periodo di pausa, la distribuzione degli aggiornamenti in questo modo riduce notevolmente l’impatto sulle macchine virtuali.

Gli aggiornamenti a istanza multipla (le macchine virtuali sono in un set di disponibilità) vengono applicati su un dominio di aggiornamento alla volta.

Le applicazioni in esecuzione in una macchina virtuale ricevono le informazioni sugli aggiornamenti futuri chiamando gli eventi pianificati del servizio metadati. Per altre informazioni sugli eventi pianificati, fare riferimento a [Servizio metadati Azure - eventi pianificati](../virtual-machines-scheduled-events.md).
