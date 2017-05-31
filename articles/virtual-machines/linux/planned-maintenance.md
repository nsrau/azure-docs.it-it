---
title: Manutenzione pianificata per VM Linux in Azure | Microsoft Docs
description: Informazioni sulla manutenzione pianificata di Azure e su come influisce sulle macchine virtuali Windows eseguite in Azure.
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 28c2fb5a67eca0c5ab2f0299bba7c11375e10558
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017


---

# <a name="planned-maintenance-for-linux-virtual-machines"></a>Manutenzione pianificata delle macchine virtuali Linux 

Microsoft Azure esegue periodicamente aggiornamenti a livello globale per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host sottostante alle macchine virtuali. Tali aggiornamenti includono l'applicazione di patch ai componenti software nell'ambiente host (sistema operativo, hypervisor e diversi agenti distribuiti nell'host), l'aggiornamento dei componenti di rete e la rimozione delle autorizzazioni per l'hardware.

La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali ospitate o i servizi cloud.

Vi sono casi tuttavia in cui gli aggiornamenti hanno conseguenze sulle macchine virtuali ospitate:

-   La manutenzione con mantenimento delle VM che usa la migrazione delle VM sul posto fa riferimento a una classe di aggiornamenti in cui le macchine virtuali non vengono riavviate durante la manutenzione.

-   La manutenzione con riavvio delle VM che richiede un riavvio a la ridistribuzione nelle macchine virtuali ospitate.

Si noti che questa pagina descrive il modo in cui Microsoft Azure esegue la manutenzione pianificata. Per altre informazioni sugli eventi non pianificati (interruzioni), vedere [Gestire la disponibilità delle macchine virtuali](../windows/manage-availability.md).
