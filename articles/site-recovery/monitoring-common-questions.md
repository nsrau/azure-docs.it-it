---
title: Domande frequenti sul monitoraggio di Azure Site Recovery
description: Risposte alle domande comuni sul monitoraggio di Azure Site Recovery, usando il monitoraggio integrato e Monitoraggio di Azure (Log Analytics)Get answers to common questions about Azure Site Recovery monitoring, using inbuilt monitoring and Azure Monitor (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68718262"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Domande frequenti sul monitoraggio di Site Recovery

Questo articolo risponde alle domande comuni sul monitoraggio di Azure [Site Recovery,](site-recovery-overview.md)usando il monitoraggio di Site Recovery integrato e Monitoraggio di Azure (Log Analytics).

## <a name="general"></a>Generale

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>In che modo il valore RPO è registrato in modo diverso dall'ultimo punto di ripristino disponibile?

Site Recovery uses a multi-step, asynchronous process to replicate machines to Azure.

- Nel penultimo passaggio della replica le modifiche recenti apportate nella macchina e i metadati vengono copiati in un account di archiviazione di log/cache.
- Queste modifiche, insieme al tag che identifica un punto recuperabile, vengono scritte nell'account di archiviazione/disco gestito nell'area di destinazione.
- Site Recovery può ora generare un punto recuperabile per la macchina.
- A questo punto, l'RPO è stato soddisfatto per le modifiche caricate finora nell'account di archiviazione. In altre parole, l'RPO della macchina a questo punto è uguale alla quantità di tempo trascorso dal timestamp corrispondente al punto recuperabile.
- A questo punto, Site Recovery seleziona i dati caricati dall'account di archiviazione e li applica ai dischi di replica creati per la macchina.
- Site Recovery genera quindi un punto di ripristino e lo rende disponibile per il ripristino in fase di failover.
- Pertanto, l'ultimo punto di ripristino disponibile indica il timestamp corrispondente al punto di ripristino più recente che è già stato elaborato e applicato ai dischi di replica.


Un'ora di sistema non corretta nel computer di origine di replica o nei server dell'infrastruttura locale distorcerà il valore RPO calcolato. Per la segnalazione accurata degli RPO, assicurarsi che l'orologio di sistema sia preciso in tutti i server e le macchine.



## <a name="inbuilt-site-recovery-logging"></a>Registrazione di Site Recovery integrata


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Perché il conteggio delle macchine virtuali nella visualizzazione dell'infrastruttura dell'insieme di credenziali è diverso dal conteggio totale visualizzato in Elementi replicati?

L'ambito della visualizzazione dell'infrastruttura nell'insieme di credenziali viene definito dagli scenari di replica. Solo i computer nello scenario di replica attualmente selezionato vengono inclusi nel conteggio per la visualizzazione. Inoltre, vengono conteggiate solo le macchine virtuali che sono configurate per la replica in Azure. I computer di cui è stato eseguito il failover o i computer che si replicano in un sito locale non vengono conteggiati nella visualizzazione.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Perché il conteggio degli elementi replicati in Essentials è diverso dal conteggio totale degli elementi replicati nel dashboard?

Solo i computer per i quali è stata completata la replica iniziale sono inclusi nel conteggio mostrato in Essentials. Il totale degli elementi replicati include tutti i computer nell'insieme di credenziali, inclusi quelli per i quali è attualmente in corso la replica iniziale.

## <a name="azure-monitor-logging"></a>Registrazione di Monitoraggio di Azure


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Con quale frequenza Site Recovery invia log di diagnostica al log di Monitoraggio di Azure? 

- AzureSiteRecoveryStats e AzureSiteRecoveryRecoveryPoints vengono inviati ogni 15 minuti.  
- AzureSiteRecoveryReplicationDataUploadRate e AzureSiteRecoveryProtectedDiskDataChurn vengono inviati ogni cinque minuti. 
- AzureSiteRecoveryJobs viene inviato al trigger e al completamento di un processo.
- AzureSiteRecoveryEvents viene inviato ogni volta che viene generato un evento. 
- AzureSiteRecoveryReplicatedItems viene inviato ogni volta che viene apportata una modifica all'ambiente. In genere, il tempo di aggiornamento dei dati è 15 minuti dopo una modifica. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Per quanto tempo vengono conservati i dati nei log di Monitoraggio di Azure? 

Per impostazione predefinita, la conservazione è per 31 giorni. È possibile aumentare il periodo nella sezione **Utilizzo e costo stimato** nell'area di lavoro di Log Analytics. Fare clic su **Conservazione dei dati**e scegliere l'intervallo.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>Qual è la dimensione dei log diagnostici? 

In genere la dimensione di un registro è 15-20 KB. 


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire il monitoraggio integrato di [Site Recovery](site-recovery-monitor-and-troubleshoot.md)o Monitoraggio [di Azure.](monitor-log-analytics.md)


