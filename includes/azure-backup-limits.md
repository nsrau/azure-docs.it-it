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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238811"
---
I limiti seguenti si applicano ai Backup di Azure.

| **Limite** | **Default** |
| --- | --- |
| Server o macchine che possono essere registrate in un insieme di credenziali. | Windows Server o Windows Client/System Center Data Protection Manager: 50. <br/><br/> Macchine virtuali IaaS: 1,000.  |
| Dimensioni di un'origine dati nell'archiviazione dell'insieme di credenziali. |Massimo 54,400 GB. Il limite non si applica a backup di VM IaaS. |
| Credenziali per il backup in una sottoscrizione di Azure. |500 insiemi di credenziali per ogni area. |
| Pianificare i backup giornalieri. |Windows Server/Client: Tre al giorno.<br/> System Center DPM: Due al giorno. <br/> Macchine virtuali IaaS: Una volta al giorno.  |
| Dischi dati collegati a una VM di Azure per il backup. | 16 |
| Singolo disco dati collegato alla macchina virtuale di Azure per il backup.| 4.095 GB|
