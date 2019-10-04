---
title: Domande comuni sul monitoraggio Azure Site Recovery
description: Risposte alle domande comuni sul monitoraggio Azure Site Recovery, usando il monitoraggio incorporato e monitoraggio di Azure (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718262"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Domande comuni sul monitoraggio Site Recovery

Questo articolo risponde alle domande più comuni sul monitoraggio di Azure [Site Recovery](site-recovery-overview.md), sull'uso del monitoraggio Site Recovery incorporato e su monitoraggio di azure (log Analytics).

## <a name="general"></a>Generale

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Come viene registrato il valore RPO diverso dal punto di ripristino disponibile più recente?

Site Recovery usa un processo asincrono in più passaggi per la replica di computer in Azure.

- Nel penultimo passaggio della replica le modifiche recenti apportate nella macchina e i metadati vengono copiati in un account di archiviazione di log/cache.
- Queste modifiche, insieme al tag che identifica un punto reversibile, vengono scritte nell'account di archiviazione o nel disco gestito nell'area di destinazione.
- Site Recovery ora può generare un punto di ripristino per il computer.
- A questo punto, il RPO è stato soddisfatto per le modifiche caricate finora nell'account di archiviazione. In altre parole, il computer RPO a questo punto è uguale alla quantità di tempo trascorsa dal timestamp corrispondente al punto ripristinabile.
- A questo punto, Site Recovery seleziona i dati caricati dall'account di archiviazione e li applica ai dischi di replica creati per la macchina.
- Site Recovery genera quindi un punto di ripristino e lo rende disponibile per il ripristino in fase di failover.
- Il punto di ripristino disponibile più recente indica quindi il timestamp corrispondente al punto di ripristino più recente che è già stato elaborato e applicato ai dischi di replica.


Un'ora di sistema non corretta nel computer di origine di replica, o nei server di infrastruttura locale, dimostrerà il valore RPO calcolato. Per la segnalazione accurata degli RPO, assicurarsi che l'orologio di sistema sia preciso in tutti i server e le macchine.



## <a name="inbuilt-site-recovery-logging"></a>Registrazione Site Recovery incorporata


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Perché il numero di VM nella visualizzazione dell'infrastruttura dell'insieme di credenziali è diverso dal numero totale visualizzato negli elementi replicati?

L'ambito della visualizzazione dell'infrastruttura nell'insieme di credenziali viene definito dagli scenari di replica. Solo i computer nello scenario di replica attualmente selezionato sono inclusi nel conteggio per la vista. Inoltre, vengono conteggiate solo le macchine virtuali che sono configurate per la replica in Azure. I computer di cui è stato eseguito il failover o i computer che eseguono la replica in un sito locale non vengono conteggiati nella vista.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Perché il numero di elementi replicati in Essentials è diverso dal numero totale di elementi replicati nel dashboard?

Solo i computer per i quali è stata completata la replica iniziale sono inclusi nel conteggio illustrato in Essentials. Il totale degli elementi replicati include tutti i computer nell'insieme di credenziali, inclusi quelli per cui è attualmente in corso la replica iniziale.

## <a name="azure-monitor-logging"></a>Registrazione di Monitoraggio di Azure


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Con quale frequenza Site Recovery inviare i log di diagnostica al log di monitoraggio di Azure? 

- AzureSiteRecoveryReplicationStats e AzureSiteRecoveryRecoveryPoints vengono inviati ogni 15 minuti.  
- AzureSiteRecoveryReplicationDataUploadRate e AzureSiteRecoveryProtectedDiskDataChurn vengono inviati ogni cinque minuti. 
- AzureSiteRecoveryJobs viene inviato al trigger e al completamento di un processo.
- AzureSiteRecoveryEvents viene inviato ogni volta che viene generato un evento. 
- AzureSiteRecoveryReplicatedItems viene inviato ogni volta che viene apportata una modifica all'ambiente. In genere, il tempo di aggiornamento dei dati è 15 minuti dopo una modifica. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Per quanto tempo vengono conservati i dati nei log di monitoraggio di Azure? 

Per impostazione predefinita, la conservazione è di 31 giorni. È possibile aumentare il periodo nella sezione **utilizzo e costo stimato** dell'area di lavoro log Analytics. Fare clic su **conservazione dei dati**e scegliere l'intervallo.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>Quali sono le dimensioni dei log di diagnostica? 

In genere, le dimensioni di un log sono pari a 15-20 KB. 


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come monitorare con [Site Recovery monitoraggio incorporato](site-recovery-monitor-and-troubleshoot.md)o monitoraggio di [Azure](monitor-log-analytics.md).


