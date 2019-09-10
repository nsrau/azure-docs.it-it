---
title: File di inclusione
description: File di inclusione
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67180547"
---
I limiti seguenti si applicano ai Backup di Azure.

| **Limite** | **Default** |
| --- | --- |
| Server o computer che possono essere registrati in un insieme di credenziali. | Windows Server/client Windows/System Center Data Protection Manager: 50. <br/><br/> Macchine virtuali IaaS: 1.000.  |
| Dimensioni di un'origine dati nell'archivio dell'insieme di credenziali. |54.400-GB massimo. Il limite non si applica al backup della macchina virtuale IaaS. |
| Insiemi di credenziali di backup in una sottoscrizione di Azure. |insiemi di credenziali 500 per area. |
| Pianificare i backup giornalieri. |Windows Server/Client: Tre giorni.<br/> System Center DPM: Due al giorno. <br/> Macchine virtuali IaaS: Una volta al giorno.  |
| Dischi dati collegati a una macchina virtuale di Azure per il backup. | 16 |
| Singolo disco dati collegato alla macchina virtuale di Azure per il backup.| 4\.095 GB|
