---
title: file di inclusione
description: file di inclusione
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829164"
---
I limiti seguenti si applicano ai Backup di Azure.

| **Limite** | **Default** |
| --- | --- |
| Server o computer che possono essere registrati in un insieme di credenziali. | Windows Server/Windows client/System Center Data Protection Manager: 50. <br/><br/> VM IaaS: 1.000.  |
| Dimensioni di un'origine dati nell'archivio dell'insieme di credenziali. |54.400-GB massimo. Il limite non si applica al backup della macchina virtuale IaaS. |
| Insiemi di credenziali di backup in una sottoscrizione di Azure. |insiemi di credenziali 500 per area. |
| Pianificare i backup giornalieri. |Windows Server/client: tre giorni.<br/> System Center DPM: due al giorno. <br/> Macchine virtuali IaaS: una volta al giorno.  |
| Dischi dati collegati a una macchina virtuale di Azure per il backup. | 16 |
| Singolo disco dati collegato alla macchina virtuale di Azure per il backup.| 32 TB|
