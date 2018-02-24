---
title: Architettura della replica da Hyper-V ad Azure in Azure Site Recovery | Microsoft Docs
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante la replica di macchine virtuali Hyper-V locali (senza VMM) in Azure con il servizio Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.openlocfilehash: dd3dcf325ed5a628c98ac63683440e1796aa8c3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="hyper-v-to-azure-replication-architecture"></a>Architettura della replica da Hyper-V ad Azure


Questo articolo descrive l'architettura e i processi usati per la replica, il failover e il ripristino di macchine virtuali (VM) Hyper-V tra host Hyper-V locali e Azure, usando il servizio [Azure Site Recovery](site-recovery-overview.md).

È facoltativamente possibile gestire gli host Hyper-V in cloud privati System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Componenti dell'architettura - Hyper-V senza VMM

La tabella e il grafico seguenti offrono una visualizzazione generale dei componenti usati per la replica Hyper-V in Azure, quando gli host Hyper-V non sono gestiti da VMM.

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Azure** | Sono necessari una sottoscrizione di Azure, un account di archiviazione di Azure e una rete di Azure. | I dati replicati da carichi di lavoro di macchine virtuali locali vengono archiviati nell'account di archiviazione. Le macchine virtuali di Azure vengono create con i dati dei carichi di lavoro replicati durante il failover dal sito locale.<br/><br/> Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Hyper-V** | Durante la distribuzione di Site Recovery, è necessario raccogliere gli host e i cluster Hyper-V in siti Hyper-V. È necessario installare il provider di Azure Site Recovery e l'agente di Servizi di ripristino in ogni computer Hyper-V. | Il provider orchestra la replica con Site Recovery su Internet. L'agente di Servizi di ripristino gestisce la replica dei dati.<br/><br/> Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure vengono anche crittografati.
**VM Hyper-V** | Una o più macchine virtuali in esecuzione in Hyper-V. | Non è necessario installare in modo esplicito alcun componente nelle macchine virtuali.


**Architettura della replica da Hyper-V ad Azure (senza VMM)**

![Architecture](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Componenti dell'architettura - Hyper-V con VMM

La tabella e il grafico seguenti offrono una visualizzazione generale dei componenti usati per la replica Hyper-V in Azure, quando gli host Hyper-V vengono gestiti in cloud VMM.

**Componente** | **Requisito** | **Dettagli**
--- | --- | ---
**Azure** | Sono necessari una sottoscrizione di Azure, un account di archiviazione di Azure e una rete di Azure. | I dati replicati da carichi di lavoro di macchine virtuali locali vengono archiviati nell'account di archiviazione. Quando si verifica un failover dal sito locale, vengono create le VM di Azure con i dati replicati.<br/><br/> Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Server VMM** | Il server VMM ha uno o più cloud contenenti host Hyper-V. | È necessario installare il provider di Site Recovery nel server VMM per orchestrare la replica con Site Recovery e registrare il server nell'insieme di credenziali di Servizi di ripristino.
**Host Hyper-V** | Uno o più host/cluster Hyper-V gestiti da VMM. |  L'agente di Servizi di ripristino viene installato in ogni host o membro del cluster.
**VM Hyper-V** | Una o più macchine virtuali in esecuzione in un server host Hyper-V. | Non è necessario installare esplicitamente alcun componente nelle macchine virtuali.
**Rete** | Reti VM e logiche configurate nel server VMM. La rete delle macchine virtuali deve essere collegata a una rete logica associata al cloud. | Viene eseguito il mapping delle reti delle macchine virtuali alle reti virtuali di Azure. Quando le macchine virtuali di Azure vengono create dopo il failover, vengono aggiunte alla rete di Azure di cui è stato eseguito il mapping alla rete delle macchine virtuali.

**Architettura della replica da Hyper-V ad Azure (con VMM)**

![Componenti](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Processo di replica

![Replica da Hyper-V ad Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Processo di replica e ripristino**


### <a name="enable-protection"></a>Abilitare la protezione

1. Dopo aver abilitato la protezione per una macchina virtuale Hyper-V, nel portale di Azure o in locale, viene avviato **Abilita protezione**.
2. Il processo controlla se il computer è conforme ai prerequisiti, prima di richiamare [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), per impostare la replica con le impostazioni configurate.
3. Il processo avvia la replica iniziale richiamando il metodo [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), per inizializzare una replica della macchina virtuale completa e inviare i dischi virtuali della VM ad Azure.
4. Il processo può essere monitorato nella scheda **Processi**.      ![Elenco dei processi](media/hyper-v-azure-architecture/image1.png)![Abilitare protezione in dettaglio](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Replica iniziale dei dati

1. Quando viene attivata la replica iniziale, viene acquisito uno [snapshot della macchina virtuale Hyper-V](https://technet.microsoft.com/library/dd560637.aspx).
2. I dischi rigidi virtuali nella macchina virtuale vengono replicati uno per volta fino a quando non vengono copiati tutti in Azure. Questa operazione può richiedere tempo, a seconda delle dimensioni della macchina virtuale e della larghezza di banda di rete. [Informazioni](https://support.microsoft.com/kb/3056159) su come aumentare la larghezza di banda di rete.
3. Se vengono apportate modifiche ai dischi mentre è in corso la replica iniziale, Hyper-V Replica Replication Tracker tiene traccia delle modifiche sotto forma di log di replica di Hyper-V (HRL). Questi file di log si trovano nella stessa cartella dei dischi. A ogni disco è associato un file HRL, che viene inviato alla risorsa di archiviazione secondaria. Si noti che lo snapshot e i file di log usano risorse del disco durante l'esecuzione della replica iniziale.
4. Al termine della replica iniziale, lo snapshot della macchina virtuale viene eliminato.
5. Nel log le modifiche differenziali al disco vengono sincronizzate e unite al disco padre.


### <a name="finalize-protection-process"></a>Finalizzare il processo di protezione

1. Al termine della replica iniziale, viene eseguito il processo **Finalizza la protezione nella macchina virtuale**. Questo processo configura impostazioni di rete e altre impostazioni successive alla replica in modo da proteggere la macchina virtuale.
2. In questa fase è possibile controllare le impostazioni della macchina virtuale per assicurarsi che sia pronta per il failover. È possibile eseguire un'analisi di ripristino di emergenza (failover di test) per la macchina virtuale, per verificare che il failover avvenga nel modo previsto. 


## <a name="delta-replication"></a>Replica dei dati

1. Dopo la replica iniziale, viene avviata la replica differenziale in base ai criteri di replica.
2. Hyper-V Replica Replication Tracker tiene traccia delle modifiche apportate a un disco rigido virtuale sotto forma di file HRL. A ogni disco configurato per la replica è associato un file con estensione hrl.
3. Il log viene inviato all'account di archiviazione del cliente. Quando un log è in transito verso Azure, le modifiche del disco primario vengono registrate in un altro file di log nella stessa cartella.
4. Durante la replica iniziale e quella differenziale, è possibile monitorare la macchina virtuale nel portale di Azure.

### <a name="resynchronization-process"></a>Processo di risincronizzazione

1. Se la replica differenziale non riesce e una replica completa risulta costosa a livello di larghezza di banda o di tempo richiesto, la macchina virtuale verrà contrassegnata per la risincronizzazione.
    - Ad esempio, se i file con estensione hrl raggiungono il 50% delle dimensioni del disco, la macchina virtuale verrà contrassegnata per la risincronizzazione.
    -  Per impostazione predefinita, la risincronizzazione è pianificata per l'esecuzione automatica al di fuori degli orari lavorativi.
1.  La risincronizzazione invia solo i dati differenziali.
    - Questo processo riduce al minimo la quantità di dati inviati tramite il calcolo dei checksum delle macchine virtuali di origine e di destinazione.
    - La risincronizzazione usa un algoritmo di suddivisione in blocchi che suddivide i file di origine e di destinazione in blocchi fissi.
    - Vengono generati i checksum per ogni blocco. I checksum vengono quindi confrontati per determinare quali blocchi dell'origine devono essere applicati alla destinazione.
2. Al termine della risincronizzazione, dovrebbe riprendere la normale replica differenziale.
3. Se non si vuole attendere la risincronizzazione predefinita al di fuori degli orari di lavoro, è possibile risincronizzare una macchina virtuale manualmente, ad esempio quando si verifica un'interruzione. A questo scopo, nel portale di Azure selezionare la macchina virtuale > **Risincronizza**.

    ![Risincronizzazione manuale](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Processo di esecuzione di nuovi tentativi

Se si verifica un errore di replica, per impostazione predefinita viene effettuato un nuovo tentativo. I nuovi tentativi sono classificati come indicato nella tabella.

**Categoria** | **Dettagli**
--- | ---
**Errori irreversibili** | Non viene eseguito alcun nuovo tentativo. Lo stato della macchina virtuale sarà **Critico** e sarà necessario l'intervento di un amministratore.<br/><br/> Esempi di questi errori includono una catena di dischi rigidi virtuali interrotta, uno stato non valido per la macchina virtuale di replica, errori di autenticazione di rete, errori di autorizzazione ed errori di macchina virtuale non trovata (per i server Hyper-V autonomi).
**Errori reversibili** | Vengono eseguiti nuovi tentativi durante ogni intervallo di replica usando un backoff esponenziale che permette di aumentare l'intervallo tra i tentativi dall'inizio del primo tentativo di 1, 2, 4, 8 e 10 minuti. Se l'errore persiste, viene eseguito un nuovo tentativo ogni 30 minuti. Alcuni esempi includono errori di rete, errori di spazio su disco insufficiente e condizioni di memoria insufficiente.



## <a name="failover-and-failback-process"></a>Processo di failover e failback

1. È possibile eseguire un failover pianificato o non pianificato dalle macchine virtuali Hyper-V locali ad Azure. Se si esegue un failover pianificato, le macchine virtuali di origine vengono arrestate per assicurare che non si verifichino perdite di dati. Eseguire un failover non pianificato se il sito primario non è accessibile.
2. È possibile eseguire il failover di un solo computer o creare piani di ripristino per orchestrare il failover di più computer.
3. Eseguire un failover. Al termine della prima fase, le macchine virtuali di replica create dovrebbero essere visualizzate in Azure. È possibile assegnare un indirizzo IP pubblico alla VM, se necessario.
4. È quindi necessario eseguire il commit del failover per iniziare ad accedere al carico di lavoro dalla macchina virtuale di Azure di replica.

Quando l'infrastruttura locale è di nuovo operativa, è possibile eseguire il failback. Il failback avviene in tre fasi:

1. Aviare un failover pianificato da Azure al sito primario:
    - **Ridurre al minimo il tempo di inattività**: se si usa questa opzione, Site Recovery sincronizza i dati prima del failover. Il servizio verifica la presenza di blocchi di dati modificati e li scarica nel sito locale, mentre la macchina virtuale di Azure resta in esecuzione, riducendo al minimo il tempo di inattività. Quando si specifica manualmente che è necessario completare il failover, la macchina virtuale di Azure viene arrestata, vengono copiate tutte le modifiche differenziali finali e viene avviato il failover.
    - **Download completo**: con questa opzione i dati vengono sincronizzati durante il failover. Questa opzione scarica l'intero disco. Questa opzione è più veloce, perché non viene calcolato alcun checksum, ma il tempo di inattività è maggiore. Usare questa opzione se le macchine virtuali di Azure di replica sono state eseguite per un certo tempo o se la macchina virtuale locale è stata eliminata.
    - **Crea macchina virtuale**: è possibile scegliere di eseguire il failback nella stessa macchina virtuale o in una alternativa. È possibile specificare che Site Recovery deve creare la macchina virtuale, se non esiste già.

2. Al termine della sincronizzazione iniziale, scegliere di completare il failover. Al termine, è possibile accedere alla macchina virtuale locale per verificare che funzioni tutto come previsto. Nel portale di Azure si noterà che le macchine virtuali di Azure sono state arrestate.
3.  Eseguire quindi il commit del failover per completare il processo e iniziare ad accedere di nuovo al carico di lavoro dalla macchina virtuale locale.
4. Al termine del failback dei carichi di lavoro, abilitare la replica inversa, in modo che le macchine virtuali locali vengano replicate di nuovo in Azure.



## <a name="next-steps"></a>Passaggi successivi


Seguire [questa esercitazione](tutorial-prepare-azure.md) per iniziare a usare la replica da Hyper-V ad Azure.


