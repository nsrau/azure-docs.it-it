---
title: Funzionamento della replica Hyper-V in Azure in Site Recovery | Documentazione Microsoft
description: Questo articolo offre una panoramica del funzionamento della replica Hyper-V in Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 9adf266c6a2ac00c3aaa34e2a29aefe34abe2871
ms.lasthandoff: 04/18/2017


---
# <a name="how-does-hyper-v-replication-to-azure-work"></a>Funzionamento della replica Hyper-V in Azure

Leggere questo articolo per capire l'architettura e i flussi di lavoro per la replica Hyper-V in Azure tramite il servizio [Azure Site Recovery](site-recovery-overview.md).

Per inviare commenti è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

È possibile replicare gli elementi seguenti in Azure:
- **Hyper-V con VMM**: macchine virtuali presenti negli host Hyper-V locali gestiti nei cloud System Center Virtual Machine Manager (VMM). Gli host possono eseguire qualsiasi [sistema operativo supportato](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). È possibile replicare macchine virtuali Hyper-V che eseguono qualsiasi sistema operativo guest [supportato da Hyper-V e Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- **Hyper-V senza VMM**: macchine virtuali locali presenti negli host Hyper-V che non sono gestiti nei cloud VMM. Gli host possono eseguire qualsiasi [sistema operativo supportato](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). È possibile replicare macchine virtuali Hyper-V che eseguono qualsiasi sistema operativo guest [supportato da Hyper-V e Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="architectural-components"></a>Componenti dell'architettura

**Area** | **Componente** | **Dettagli**
--- | --- | ---
**Azzurro** | In Azure sono necessari un account Microsoft Azure, un account di archiviazione di Azure e una rete di Azure. | La risorsa di archiviazione e la rete possono essere account basati su Resource Manager o classici.<br/><br/> I dati replicati vengono salvati nell'account di archiviazione e,quando si verifica un failover dal sito locale, vengono create VM di Azure con i dati replicati.<br/><br/> Le VM di Azure si connettono alla rete virtuale di Azure quando vengono create.
**Server VMM** | Host Hyper-V situati in cloud VMM | Se gli host Hyper-V sono gestiti in cloud VMM, il server VMM viene registrato nell'insieme di credenziali di Servizi di ripristino.<br/><br/> Nel server VMM è necessario installare il provider di Site Recovery per orchestrare la replica con Azure.<br/><br/> È necessaria la configurazione di reti logiche e VM per configurare il mapping di rete. È necessario che una rete VM sia collegata a una rete logica associata al cloud.
**Host Hyper-V** | I server Hyper-V possono essere distribuiti con o senza server VMM. | In assenza di un server VMM, il provider di Site Recovery è installato nell'host per orchestrare la replica con Site Recovery tramite Internet. Se è presente un server VMM, il provider è installato in esso e non nell'host.<br/><br/> L'agente di Servizi di ripristino viene installato nell'host per gestire la replica dei dati.<br/><br/> Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure vengono anche crittografati.
**VM Hyper-V** | È necessaria una o più macchine virtuali nel server host Hyper-V. | Non è necessario installare esplicitamente alcun componente nelle macchine virtuali.

## <a name="deployment-steps"></a>Passaggi di distribuzione

1. **Azure**: si configurano i componenti di Azure. È consigliabile configurare gli account di archiviazione e di rete prima di iniziare la distribuzione di Site Recovery.
2. **Insieme di credenziali**: si crea un insieme di credenziali di Servizi di ripristino per Site Recovery e si configurano le impostazioni dell'insieme di credenziali, incluse la configurazione delle impostazioni di origine e di destinazione, l'impostazione di un criterio di replica e l'abilitazione della replica.
3. **Origine e destinazione**:
    - **Host Hyper-V in cloud VMM**: nell'ambito della definizione delle impostazioni di origine, si scarica e si installa il provider di Azure Site Recovery nel server VMM e l'agente di Servizi di ripristino di Azure in ogni host Hyper-V. L'origine sarà il server VMM. La destinazione è Azure.
    - Host Hyper-V senza VMM: quando si specificano le impostazioni di origine, si scaricano e si installano il provider e l'agente in ogni host Hyper-V. Durante la distribuzione si raccolgono gli host in un sito Hyper-V e si specifica tale sito come origine. La destinazione è Azure.

    ![Replica Hyper-V/VMM in Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)
    ![Replica del sito Hyper-V in Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


4. **Criterio di replica**: si crea un criterio di replica per il sito Hyper-V o per il cloud VMM. Il criterio viene applicato a tutte le VM negli host del sito o del cloud.
5. **Abilitazione della replica**: si abilita la replica per le macchine virtuali Hyper-V. La replica iniziale viene eseguita in base alle impostazioni del criterio di replica. Le modifiche ai dati vengono rilevate e la replica delle modifiche differenziali in Azure viene avviata al termine della replica iniziale. Le modifiche rilevate per un elemento vengono salvate in un file HRL.
6. **Failover di test**: si esegue un failover di test per verificare che tutto funzioni come previsto.

Altre informazioni sulla distribuzione:
- [Introduzione alla replica in Azure di VM Hyper-V con VMM](site-recovery-vmm-to-azure.md)
- [Introduzione alla replica in Azure di VM Hyper-V senza VMM](site-recovery-hyper-v-site-to-azure.md)

## <a name="hyper-v-replication-workflow"></a>Flusso di lavoro di replica Hyper-V

### <a name="enable-protection"></a>Abilitare la protezione

1. Dopo aver abilitato la protezione per una macchina virtuale Hyper-V, nel portale di Azure o in locale, viene avviato **Abilita protezione**.
2. Il processo controlla se il computer è conforme ai prerequisiti, prima di richiamare [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), per impostare la replica con le impostazioni configurate.
3. Il processo avvia la replica iniziale richiamando il metodo [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), per inizializzare una replica della macchina virtuale completa e inviare i dischi virtuali della VM ad Azure.
4. Il processo può essere monitorato nella scheda **Processi**.
        ![Elenco dei processi](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![Abilita protezione in dettaglio](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="initial-replication"></a>Replica iniziale

1. Quando viene attivata la replica iniziale, viene acquisito uno [snapshot della VM Hyper-V](https://technet.microsoft.com/library/dd560637.aspx).
2. I dischi rigidi virtuali vengono replicati uno alla volta fino a quando non vengono copiati tutti in Azure. L'operazione potrebbe richiedere alcuni minuti, a seconda delle dimensioni della macchina virtuale e della larghezza di banda di rete. Per informazioni su come ottimizzare l'utilizzo della rete, vedere [How to manage on-premises to Azure protection network bandwidth usage](https://support.microsoft.com/kb/3056159)(Come gestire l'utilizzo della larghezza di banda di rete per la protezione da ambiente locale ad Azure).
3. Se vengono apportate modifiche al disco mentre è in corso la replica iniziale, la gestione delle repliche di Hyper-V tiene traccia delle modifiche sotto forma di log di replica di Hyper-V (con estensione hrl). Questi file si trovano nella stessa cartella dei dischi. A ogni disco è associato un file con estensione hrl, che verrà inviato alla risorsa di archiviazione secondaria.
4. Si noti che lo snapshot e i file di log usano risorse del disco durante l'esecuzione della replica iniziale.
5. Al termine della replica iniziale, lo snapshot della macchina virtuale viene eliminato. Nel log le modifiche differenziali al disco vengono sincronizzate e unite al disco padre.


### <a name="finalize-protection"></a>Finalizzare la protezione

1. Al termine della replica iniziale, il processo **Finalizza la protezione nella macchina virtuale** configura la rete e altre impostazioni successive alla replica, in modo che la macchina virtuale sia protetta.
    ![Processo Finalizza la protezione](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Se si esegue la replica in Azure, potrebbe essere necessario modificare le impostazioni della macchina virtuale in modo che sia pronta per il failover. A questo punto è possibile eseguire un failover di test per controllare che tutto funzioni come previsto.

### <a name="delta-replication"></a>Replica dei dati

1. Dopo la replica iniziale, viene avviata la sincronizzazione differenziale in base alle impostazioni della replica.
2. La gestione delle repliche di Hyper-V tiene traccia delle modifiche in un disco rigido virtuale sotto forma di file con estensione hrl. A ogni disco configurato per la replica è associato un file con estensione hrl. I log vengono inviati all'account di archiviazione del cliente al termine della replica iniziale. Quando un log è in transito verso Azure, le modifiche nel disco primario vengono registrate in un altro file di log nella stessa directory.
3. Durante la replica iniziale e differenziale, è possibile monitorare la macchina virtuale nella relativa visualizzazione. [Altre informazioni](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="replication-synchronization"></a>Sincronizzazione della replica

1. Se la replica differenziale non riesce e una replica completa risulta costosa a livello di larghezza di banda o di tempo richiesto, la macchina virtuale verrà contrassegnata per la risincronizzazione. Ad esempio, se i file con estensione hrl raggiungono il 50% delle dimensioni del disco, la macchina virtuale verrà contrassegnata per la risincronizzazione.
2.  La risincronizzazione riduce al minimo la quantità di dati inviati calcolando i checksum delle macchine virtuali di origine e di destinazione e inviando solo i dati relativi al differenziale. La risincronizzazione usa un algoritmo di suddivisione in blocchi a blocchi fissi che suddivide appunto i file di origine e di destinazione in blocchi fissi. I checksum per ogni blocco vengono generati e messi a confronto per determinare quali blocchi dell'origine devono essere applicati alla destinazione.
3. Al termine della risincronizzazione, dovrebbe riprendere la normale replica differenziale. Per impostazione predefinita, la risincronizzazione è pianificata per l'esecuzione automatica negli orari non lavorativi, ma è possibile risincronizzare manualmente una macchina virtuale. Ad esempio, se si verifica un'interruzione di rete o un'altra interruzione, è possibile riprendere la risincronizzazione. A tale scopo, selezionare la macchina virtuale nel portale > **Risincronizza**.

    ![Risincronizzazione manuale](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retries"></a>Tentativi

Se si verifica un errore di replica, per impostazione predefinita viene effettuato un nuovo tentativo. Tale logica può essere classificata nelle due categorie seguenti:

**Categoria** | **Dettagli**
--- | ---
**Errori irreversibili** | Non viene eseguito alcun nuovo tentativo. Lo stato della macchina virtuale sarà **Critico** e sarà necessario l'intervento di un amministratore. Esempi di questi errori includono: catena VHD interrotta, stato non valido per la macchina virtuale di replica, errori di autenticazione di rete: errori di autorizzazione, errori di macchina virtuale non trovata (per i server Hyper-V autonomi).
**Errori reversibili** | Vengono eseguiti nuovi tentativi durante ogni intervallo di replica usando un backoff esponenziale che permette di aumentare l'intervallo tra i tentativi dall'inizio del primo tentativo di 1, 2, 4, 8 e 10 minuti. Se l'errore persiste, viene eseguito un nuovo tentativo ogni 30 minuti. Alcuni esempi: errori di rete, errori di spazio su disco insufficiente, condizioni di memoria insufficiente. |

## <a name="protection-and-recovery-lifecycle"></a>Ciclo di vita di protezione e ripristino

![flusso di lavoro](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Passaggi successivi

- [Verificare i prerequisiti di distribuzione](site-recovery-prereq.md)
- Risolvere gli eventuali problemi tramite:
    - [Monitorare e risolvere i problemi di protezione](site-recovery-monitoring-and-troubleshooting.md)
    - [Contattare il supporto Microsoft](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
    - [Errori comuni e soluzioni](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)

