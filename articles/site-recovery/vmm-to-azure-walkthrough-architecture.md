---
title: Esaminare l'architettura della replica Hyper-V (con System Center VMM) in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre una panoramica dei componenti e dell'architettura usati durante la replica in Azure di macchine virtuali Hyper-V locali presenti in cloud VMM con il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: df4e227d02901153d3cfcfd4dfd4f11de180763a
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2017
---
# <a name="step-1-review-the-architecture"></a>Passaggio 1: Esaminare l'architettura


Questo articolo descrive i componenti e i processi usati durante la replica in Azure di macchine virtuali Hyper-V locali presenti in cloud System Center Virtual Machine Manager (VMM) tramite il servizio [Azure Site Recovery](site-recovery-overview.md).

Per inviare commenti è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Componenti dell'architettura

Sono diversi i componenti interessati dalla replica in Azure di macchine virtuali Hyper-V presenti in cloud VMM.

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Azzurro** | In Azure sono necessari un account Microsoft Azure, un account di archiviazione di Azure e una rete di Azure. | I dati replicati vengono salvati nell'account di archiviazione e,quando si verifica un failover dal sito locale, vengono create VM di Azure con i dati replicati.<br/><br/> Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Server VMM** | Il server VMM ha uno o più cloud contenenti host Hyper-V. | Nel server VMM è necessario installare il provider di Site Recovery per orchestrare la replica con Site Recovery e registrare il server nell'insieme di credenziali di Servizi di ripristino.
**Host Hyper-V** | Uno o più host/cluster Hyper-V gestiti da VMM. |  L'agente di Servizi di ripristino viene installato in ogni host o membro del cluster.
**VM Hyper-V** | Una o più macchine virtuali in esecuzione in un server host Hyper-V. | Non è necessario installare esplicitamente alcun componente nelle macchine virtuali.
**Rete** |Reti VM e logiche configurate nel server VMM. È necessario che una rete VM sia collegata a una rete logica associata al cloud. | Le reti VM vengono mappate in reti virtuali di Azure, in modo che le macchine virtuali di Azure si trovino in una rete quando vengono create dopo il failover.

Informazioni sui prerequisiti di distribuzione e i requisiti per ognuno di questi componenti sono disponibili nella [matrice di supporto](site-recovery-support-matrix-to-azure.md).


**Figura 1: Eseguire la replica in Azure di VM in host Hyper-V di cloud VMM**

![Componenti](./media/vmm-to-azure-walkthrough-architecture/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-process"></a>Processo di replica

**Figura 2: Processo di replica e ripristino per la replica Hyper-V in Azure**

![flusso di lavoro](./media/vmm-to-azure-walkthrough-architecture/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>Abilitare la protezione

1. Dopo aver abilitato la protezione per una macchina virtuale Hyper-V, nel portale di Azure o in locale, viene avviato **Abilita protezione**.
2. Il processo controlla se il computer è conforme ai prerequisiti, prima di richiamare [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), per impostare la replica con le impostazioni configurate.
3. Il processo avvia la replica iniziale richiamando il metodo [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), per inizializzare una replica della macchina virtuale completa e inviare i dischi virtuali della VM ad Azure.
4. Il processo può essere monitorato nella scheda **Processi**.
        ![Elenco dei processi](media/vmm-to-azure-walkthrough-architecture/image1.png) ![Abilitare protezione in dettaglio](media/vmm-to-azure-walkthrough-architecture/image2.png)

### <a name="replicate-the-initial-data"></a>Replicare i dati iniziali

1. Quando viene attivata la replica iniziale, viene acquisito uno [snapshot della VM Hyper-V](https://technet.microsoft.com/library/dd560637.aspx).
2. I dischi rigidi virtuali vengono replicati uno alla volta fino a quando non vengono copiati tutti in Azure. L'operazione potrebbe richiedere alcuni minuti, a seconda delle dimensioni della macchina virtuale e della larghezza di banda di rete. Per informazioni su come ottimizzare l'utilizzo della rete, vedere [How to manage on-premises to Azure protection network bandwidth usage](https://support.microsoft.com/kb/3056159)(Come gestire l'utilizzo della larghezza di banda di rete per la protezione da ambiente locale ad Azure).
3. Se vengono apportate modifiche al disco mentre è in corso la replica iniziale, la gestione delle repliche di Hyper-V tiene traccia delle modifiche sotto forma di log di replica di Hyper-V (con estensione hrl). Questi file si trovano nella stessa cartella dei dischi. A ogni disco è associato un file con estensione hrl, che verrà inviato alla risorsa di archiviazione secondaria.
4. Si noti che lo snapshot e i file di log usano risorse del disco durante l'esecuzione della replica iniziale.
5. Al termine della replica iniziale, lo snapshot della macchina virtuale viene eliminato. Nel log le modifiche differenziali al disco vengono sincronizzate e unite al disco padre.


### <a name="finalize-protection"></a>Finalizzare la protezione

1. Al termine della replica iniziale, il processo **Finalizza la protezione nella macchina virtuale** configura la rete e altre impostazioni successive alla replica, in modo che la macchina virtuale sia protetta.
    ![Processo Finalizza la protezione](media/vmm-to-azure-walkthrough-architecture/image3.png)
2. Se si esegue la replica in Azure, potrebbe essere necessario modificare le impostazioni della macchina virtuale in modo che sia pronta per il failover. A questo punto è possibile eseguire un failover di test per controllare che tutto funzioni come previsto.

### <a name="replicate-the-delta"></a>Replicare il differenziale

1. Dopo la replica iniziale, viene avviata la sincronizzazione differenziale in base alle impostazioni della replica.
2. La gestione delle repliche di Hyper-V tiene traccia delle modifiche in un disco rigido virtuale sotto forma di file con estensione hrl. A ogni disco configurato per la replica è associato un file con estensione hrl. I log vengono inviati all'account di archiviazione del cliente al termine della replica iniziale. Quando un log è in transito verso Azure, le modifiche nel disco primario vengono registrate in un altro file di log nella stessa directory.
3. Durante la replica iniziale e differenziale, è possibile monitorare la macchina virtuale nella relativa visualizzazione. [Altre informazioni](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="synchronize-replication"></a>Sincronizzare la replica

1. Se la replica differenziale non riesce e una replica completa risulta costosa a livello di larghezza di banda o di tempo richiesto, la macchina virtuale verrà contrassegnata per la risincronizzazione. Ad esempio, se i file con estensione hrl raggiungono il 50% delle dimensioni del disco, la macchina virtuale verrà contrassegnata per la risincronizzazione.
2.  La risincronizzazione riduce al minimo la quantità di dati inviati calcolando i checksum delle macchine virtuali di origine e di destinazione e inviando solo i dati relativi al differenziale. La risincronizzazione usa un algoritmo di suddivisione in blocchi a blocchi fissi che suddivide appunto i file di origine e di destinazione in blocchi fissi. I checksum per ogni blocco vengono generati e messi a confronto per determinare quali blocchi dell'origine devono essere applicati alla destinazione.
3. Al termine della risincronizzazione, dovrebbe riprendere la normale replica differenziale. Per impostazione predefinita, la risincronizzazione è pianificata per l'esecuzione automatica negli orari non lavorativi, ma è possibile risincronizzare manualmente una macchina virtuale. Ad esempio, se si verifica un'interruzione di rete o un'altra interruzione, è possibile riprendere la risincronizzazione. A tale scopo, selezionare la macchina virtuale nel portale > **Risincronizza**.

    ![Risincronizzazione manuale](media/vmm-to-azure-walkthrough-architecture/image4.png)


### <a name="retry-logic"></a>Logica di retry

Se si verifica un errore di replica, per impostazione predefinita viene effettuato un nuovo tentativo. Tale logica può essere classificata nelle due categorie seguenti:

**Categoria** | **Dettagli**
--- | ---
**Errori irreversibili** | Non viene eseguito alcun nuovo tentativo. Lo stato della macchina virtuale sarà **Critico** e sarà necessario l'intervento di un amministratore. Esempi di questi errori includono: catena VHD interrotta, stato non valido per la macchina virtuale di replica, errori di autenticazione di rete: errori di autorizzazione, errori di macchina virtuale non trovata (per i server Hyper-V autonomi).
**Errori reversibili** | Vengono eseguiti nuovi tentativi durante ogni intervallo di replica usando un backoff esponenziale che permette di aumentare l'intervallo tra i tentativi dall'inizio del primo tentativo di 1, 2, 4, 8 e 10 minuti. Se l'errore persiste, viene eseguito un nuovo tentativo ogni 30 minuti. Alcuni esempi: errori di rete, errori di spazio su disco insufficiente, condizioni di memoria insufficiente. |



## <a name="failover-and-failback-process"></a>Processo di failover e failback

1. È possibile eseguire un [failover](site-recovery-failover.md) pianificato o non pianificato dalle VM Hyper-V locali ad Azure. Se si esegue un failover pianificato, le macchine virtuali di origine vengono arrestate per assicurare che non si verifichino perdite di dati.
2. È possibile effettuare il failover di un solo computer o creare [piani di ripristino](site-recovery-create-recovery-plans.md) per orchestrare il failover di più computer.
4. Dopo avere eseguito il failover, le VM di replica create verranno visualizzate in Azure. È possibile assegnare un indirizzo IP pubblico alla VM, se necessario.
5. Si esegue quindi il commit del failover per avviare l'accesso al carico di lavoro dalla VM di Azure di replica.
6. Quando il sito locale primario è di nuovo disponibile, è possibile [effettuare il failback](site-recovery-failback-from-azure-to-hyper-v.md). Si avvia un failover pianificato da Azure al sito primario. Per un failover pianificato è possibile scegliere di effettuare il failback nella stessa VM o in una posizione alternativa e di sincronizzare le modifiche tra Azure e l'ambiente locale, per evitare perdite di dati. Quando le VMs vengono create in locale, si esegue il commit del failover.




## <a name="next-steps"></a>Passaggi successivi

Passare a [Step 2: Review the deployment prerequisites](vmm-to-azure-walkthrough-prerequisites.md) (Passaggio 2: Esaminare i prerequisiti della distribuzione)
