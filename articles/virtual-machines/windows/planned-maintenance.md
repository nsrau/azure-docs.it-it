---
title: Manutenzione pianificata per macchine virtuali Windows in Azure | Microsoft Docs
description: "Informazioni sulla manutenzione pianificata di Azure e su come può influire sulle macchine virtuali Windows eseguite in Azure."
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 198c1804b342b8faf406a08eff7bc42994b9dd0d
ms.lasthandoff: 03/31/2017


---
# <a name="planned-maintenance-for-windows-virtual-machines"></a>Manutenzione pianificata per le macchine virtuali Windows 

Microsoft Azure esegue periodicamente aggiornamenti a livello globale per migliorare l'affidabilità, le prestazioni e la sicurezza dell'infrastruttura host sottostante alle macchine virtuali. Tali aggiornamenti includono l'applicazione di patch ai componenti software nell'ambiente host (sistema operativo, hypervisor e diversi agenti distribuiti nell'host), l'aggiornamento dei componenti di rete e la rimozione delle autorizzazioni per l'hardware.

La maggior parte di questi aggiornamenti viene eseguita senza alcuna conseguenza per le macchine virtuali ospitate o i servizi cloud.

In alcuni casi tuttavia gli aggiornamenti hanno conseguenze sulle macchine virtuali ospitate:

-   Il mantenimento della manutenzione delle VM con la migrazione delle VM sul posto fa riferimento a una classe di aggiornamenti in cui le macchine virtuali non vengono riavviate durante la manutenzione.

-   Il riavvio della manutenzione delle VM che richiede un riavvio a una ridistribuzione nelle macchine virtuali ospitate.

Si noti che questa pagina descrive come Microsoft Azure esegue la manutenzione pianificata. Per altre informazioni sugli eventi non pianificati (interruzioni), vedere [Gestire la disponibilità delle macchine virtuali](manage-availability.md).
