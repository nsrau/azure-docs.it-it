---
title: Informazioni su failover e failback in Azure Site Recovery
description: Informazioni sul failover e sulla riuscita in Azure Site Recovery.Learn about failover and failable in Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281808"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Informazioni sul failover/failback del ripristino di emergenza locale

Questo articolo offre una panoramica del failover e del failback durante il ripristino di emergenza delle macchine locali in Azure con [Azure Site Recovery.](site-recovery-overview.md)

## <a name="recovery-stages"></a>Fasi di recupero

Il failover e il failback in Site Recovery prevede quattro fasi:

- **Fase 1: eseguire il failover dall'ambiente locale:** dopo aver impostato la replica in Azure per i computer locali, quando il sito locale si arresta, si esegue il failover di tali computer in Azure.Stage 1: Fail over from on-premises: After setting up replication to Azure for on-premises machines, when your on-premises site goes un unicin and Azure. Dopo il failover, le macchine virtuali di Azure vengono create da dati replicati.
- **Fase 2: riproteggere**le macchine virtuali di Azure: in Azure, si riproteggono le macchine virtuali di Azure in modo che inizino a replicare nel sito locale. La macchina virtuale locale (se disponibile) viene disattivata durante la riprotezione, per garantire la coerenza dei dati.
- **Fase 3: Fail over da Azure:** quando il sito locale è di nuovo in esecuzione come di consueto, si esegue un altro failover, questa volta per eseguire il failback delle macchine virtuali di Azure al sito locale. È possibile eseguire il failback nel percorso originale da cui è stato eseguito il failover o in un percorso alternativo.
- Fase 4: Riproteggere i computer locali: dopo il failback, abilitare nuovamente la replica dei computer locali in **Azure.Stage 4: Reprotect on-premises machines:** After failingback, again enable replication of the on-premises machines to Azure.

## <a name="failover"></a>Failover

Eseguire un failover come parte della strategia di continuità aziendale e ripristino di emergenza (BCDR).

- Come primo passaggio nella strategia BCDR, replicare le macchine locali in Azure in modo continuativo. Gli utenti accedono ai carichi di lavoro e alle app in esecuzione nei computer di origine locali.
- Se si presenta la necessità, ad esempio se si verifica un'interruzione in locale, si esito negativo le macchine di replica su Azure.If the need as rises, for example if there's an outage on-premises, you fail the replicating machines over to Azure. Le macchine virtuali di Azure vengono create usando i dati replicati.
- Per la continuità aziendale, gli utenti possono continuare ad accedere alle app nelle macchine virtuali di Azure.For business continuity, users can continue accessing apps on the Azure VMs.

Il failover è un'attività in due fasi:Failover is a two-phase activity:

- **Failover:** failover che crea e visualizza una macchina virtuale di Azure usando il punto di ripristino selezionato.
- **Commit:** dopo il failover si verifica la macchina virtuale in Azure:Commit : After failover you verify the VM in Azure:
    - È quindi possibile eseguire il commit del failover nel punto di ripristino selezionato oppure selezionare un punto diverso per il commit.
    - Dopo aver eseguito il commit del failover, il punto di ripristino non può essere modificato.


## <a name="connect-to-azure-after-failover"></a>Connettersi ad Azure dopo il failoverConnect to Azure after failover

Per connettersi alle macchine virtuali di Azure create dopo il failover tramite RDP/SSH, sono previsti diversi requisiti.

**Failover** | **Percorso** | **Azioni**
--- | --- | ---
**VM di Azure (Windows(** | Nel computer locale prima del failover | **Accesso tramite Internet**: Abilita RDP. Assicurarsi che le regole TCP e UDP vengano aggiunte per **Public**e che RDP sia consentito per tutti i profili in **Windows Firewall** > **Allowed Apps**.<br/><br/> **Accesso tramite VPN**da sito a sito: abilita RDP nel computer. Verificare che RDP sia consentito nelle app e nelle**funzionalità consentite** **di Windows Firewall** -> per le reti **di dominio e private.**<br/><br/>  Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Scopri di più](https://support.microsoft.com/kb/3031135).<br/><br/> Quando si attiva un failover, verificare che nella macchina virtuale non siano in sospeso aggiornamenti di Windows. Windows Update potrebbe avviarsi quando si esegue il failover e non sarà possibile accedere alla macchina virtuale fino a quando non vengono completati gli aggiornamenti.
**VM di Azure che esegue Windows** | Nella macchina virtuale di Azure dopo il failoverOn the Azure VM after failover |  [Aggiungere un indirizzo IP pubblico](https://aka.ms/addpublicip) per la macchina virtuale.<br/><br/> Le regole del gruppo di sicurezza di rete nella macchina virtuale con failover (e nella subnet di Azure a cui è connessa) devono consentire le connessioni in ingresso alla porta RDP.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale. Se non è possibile connettersi, verificare che la macchina virtuale sia in esecuzione ed esaminare i suggerimenti per [la risoluzione dei problemi.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**VM di Azure che esegue Linux** | Nel computer locale prima del failover | Assicurarsi che il servizio Secure Shell nella macchina virtuale sia impostato per l'avvio automatico all'avvio del sistema.<br/><br/> Verificare che le regole firewall accettino la connessione SSH.
**VM di Azure che esegue Linux** | Nella macchina virtuale di Azure dopo il failoverOn the Azure VM after failover | Le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta SSH.<br/><br/> [Aggiungere un indirizzo IP pubblico](https://aka.ms/addpublicip) per la macchina virtuale.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.<br/><br/>

## <a name="types-of-failover"></a>Tipi di failover

Site Recovery offre diverse opzioni di failover.

**Failover** | **Dettagli** | **Recupero** | **Flusso di lavoro**
--- | --- | --- | ---
**Failover di test** | Utilizzato per eseguire un'esercitazione che convalida la strategia BCDR, senza perdite di dati o tempi di inattività.| Crea una copia della macchina virtuale in Azure, senza alcun impatto sulla replica in corso o sull'ambiente di produzione. | 1. Eseguire un failover di test in una singola macchina virtuale o in più macchine virtuali in un piano di ripristino.<br/><br/> 2. Selezionare un punto di ripristino da utilizzare per il failover di test.<br/><br/> 3. Selezionare una rete di Azure in cui si troverà la macchina virtuale di Azure quando viene creata dopo il failover. La rete viene utilizzata solo per il failover di test.<br/><br/> 4. Verificare che il trapano abbia funzionato come previsto. Site Recovery pulisce automaticamente le macchine virtuali create in Azure durante il drill.
**Failover pianificato Hyper-V**  | Di solito utilizzato per i tempi di inattività pianificati.<br/><br/> Le macchine virtuali di origine vengono arrestate. I dati più recenti vengono sincronizzati prima dell'inizio del failover. | Nessuna perdita di dati per il flusso di lavoro pianificato. | 1. Pianificare una finestra di manutenzione dei tempi di inattività e informare gli utenti.<br/><br/> 2. Portare offline le app rivolte agli utenti.<br/><br/> 3. Avviare un failover pianificato con il punto di ripristino più recente. Il failover non viene eseguito se il computer non è arrestato o se si verificano errori.<br/><br/> 4. Dopo il failover, verificare che la macchina virtuale di Azure della replica sia attiva in Azure.4. After the failover, check that the replica Azure VM is active in Azure.<br/><br/> 5. Eseguire il failover per completare l'operazione. L'azione di commit elimina tutti i punti di ripristino.
**Failover-Hyper-V** | In genere viene eseguito se si verifica un'interruzione non pianificata o se il sito primario non è disponibile.<br/><br/> Facoltativamente, arrestare la macchina virtuale e sincronizzare le modifiche finali prima di iniziare il failover.  | Perdita minima di dati per le app. | 1. Avviare il piano BCDR. <br/><br/> 2. Avviare un failover. Specificare se Site Recovery deve arrestare la macchina virtuale e sincronizzare/replicare le modifiche più recenti prima di attivare il failover.<br/><br/> 3. È possibile eseguire il failover a una serie di opzioni del punto di ripristino, riepilogate nella tabella seguente.<br/><br/> Se non si abilita l'opzione per arrestare la macchina virtuale o se Site Recovery non è in grado di arrestarla, viene usato il punto di ripristino più recente.<br/>Il failover viene eseguito anche se non è possibile arrestare il computer.<br/><br/> 4. Dopo il failover, verificare che la macchina virtuale di Azure della replica sia attiva in Azure.4. After failover, you check that the replica Azure VM is active in Azure.<br/> Se necessario, è possibile selezionare un punto di ripristino diverso dalla finestra di conservazione di 24 ore.<br/><br/> 5. Eseguire il failover per completare l'operazione. L'azione di commit elimina tutti i punti di ripristino disponibili.
**Failover-VMware** | In genere viene eseguito se si verifica un'interruzione non pianificata o se il sito primario non è disponibile.<br/><br/> Facoltativamente, specificare che Site Recovery deve tentare di attivare un arresto della macchina virtuale e di sincronizzare e replicare le modifiche finali prima di iniziare il failover.  | Perdita minima di dati per le app. | 1. Avviare il piano BCDR. <br/><br/> 2. Avviare un failover da Site Recovery. Specificare se Site Recovery deve tentare di attivare l'arresto e la sincronizzazione della macchina virtuale prima di eseguire il failover.<br/> Il failover viene eseguito anche se non è possibile arrestare i computer.<br/><br/> 3. Dopo il failover, verificare che la macchina virtuale di Azure della replica sia attiva in Azure.3. After the failover, check that the replica Azure VM is active in Azure. <br/>Se necessario, è possibile selezionare un punto di ripristino diverso dalla finestra di conservazione di 72 ore.<br/><br/> 5. Eseguire il failover per completare l'operazione. L'azione di commit elimina tutti i punti di ripristino.<br/> Per le macchine virtuali Windows, Site Recovery disabilita gli strumenti VMware durante il failover.

## <a name="failover-processing"></a>Elaborazione del failover

In alcuni scenari il failover richiede un'altra elaborazione il cui completamento richiede da 8 a 10 minuti. Si possono notare tempi di failover di test più lunghi per:

* VM VMware che eseguono una versione del servizio Mobility precedente alla 9.8.
* Server fisici.
* Macchine virtuali Linux VMware.
* VM Hyper-V protette come i server fisici.
* VM VMware che non hanno il servizio DHCP abilitato.
* VM VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Opzioni punto di ripristino

Durante il failover, è possibile selezionare una serie di opzioni per i punti di ripristino.

**Opzione** | **Dettagli**
--- | ---
**Ultimo (RPO più basso)** | Questa opzione fornisce l'obiettivo del punto di ripristino (RPO) più basso. Elabora innanzitutto tutti i dati inviati al servizio Site Recovery, per creare un punto di ripristino per ogni macchina virtuale, prima di eseguire il failover. Questo punto di ripristino include tutti i dati replicati in Site Recovery quando è stato attivato il failover.
**Ultimi elaborazione**  | Questa opzione esegue il failover delle macchine virtuali nel punto di ripristino più recente elaborato da Site Recovery. Per visualizzare il punto di ripristino più recente per una macchina virtuale specifica, controllare Punti di **ripristino più recenti** nelle impostazioni della macchina virtuale. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
**Più recente app coerente** |  Questa opzione esegue il failover delle macchine virtuali nel punto di ripristino coerente con l'applicazione più recente elaborato da Site Recovery, se sono abilitati i punti di ripristino coerenti con l'app. Controllare il punto di ripristino più recente nelle impostazioni della macchina virtuale.
**Ultima elaborazione multi-VM** | questa opzione è disponibile per i piani di ripristino con una o più macchine virtuali in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali in cui è abilitata l'impostazione eseguono il failover nel punto di ripristino coerente tra più macchine comune più recente. Tutte le altre macchine virtuali nel piano eseguono il failover all'ultimo punto di ripristino elaborato.
**L'app multi-VM più recente coerente** |  questa opzione è disponibile per i piani di ripristino con una o più macchine virtuali in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali che fanno parte di un gruppo di replica eseguono il failover nel punto di ripristino coerente a livello applicazione tra più macchine comune più recente. Le altre macchine virtuali eseguono il failover nel relativo punto di ripristino più recente coerente con l'applicazione.
**Personalizzato** | Usare questa opzione per eseguire il failover di una macchina virtuale specifica in un determinato punto di ripristino nel tempo. Questa opzione non è disponibile per i piani di ripristino.

> [!NOTE]
> Non è possibile eseguire la migrazione dei punti di ripristino in un altro insieme di credenziali di Servizi di ripristino.

## <a name="reprotectionfailback"></a>Riprotezione/failback

Dopo il failover in Azure, le macchine virtuali di Azure replicate sono in uno stato non protetto.

- Come primo passaggio per eseguire il failback al sito locale, è necessario avviare la replica delle macchine virtuali di Azure in locale. Il processo di riprotezione dipende dal tipo di computer di cui è stato eseguito il failover.
- Dopo la replica delle macchine da Azure in locale, è possibile eseguire un failover da Azure al sito locale.
- Dopo che i computer vengono nuovamente eseguiti in locale, è possibile abilitare la replica in modo che vengano replicati in Azure per il ripristino di emergenza.

Il failback funziona come segue:

- Per eseguire il failback, una macchina virtuale richiede almeno un punto di ripristino per poter eseguire il failback. In un piano di ripristino, tutte le macchine virtuali nel piano richiedono almeno un punto di ripristino.
- È consigliabile usare il punto di ripristino **più recente** per eseguire il failback (si tratta di un punto coerente con l'arresto anomalo del sistema).
    - È disponibile un'opzione del punto di ripristino coerente con l'app. In questo caso, una singola macchina virtuale esegue il ripristino fino al punto di ripristino coerente con l'app disponibile più recente. Per un piano di ripristino con un gruppo di replica, ogni gruppo di replica esegue il ripristino nel punto di ripristino disponibile comune.
    - Punti di ripristino coerenti con le app possono essere in ritardo nel tempo e potrebbe verificarsi una perdita di dati.
- Durante il failover da Azure al sito locale, Site Recovery arresta le macchine virtuali di Azure.During failover from Azure to the on-premises site, Site Recovery shuts down the Azure VMs. Quando si esegue il commit del failover, Site Recovery rimuove le macchine virtuali di Azure di cui è stato eseguito il rollback in Azure.When you commit the failover, Site Recovery removes the failed back Azure VMs in Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/riprotezione fisica/failback

Per riproteggere ed eseguire il failback dei computer VMware e dei server fisici da Azure in locale, è necessaria un'infrastruttura di failback e sono previsti diversi requisiti.

- **Server di elaborazione temporaneo in Azure:** per eseguire il failback da Azure, è possibile configurare una macchina virtuale di Azure in modo che funga da server di elaborazione per gestire la replica da Azure.Temporary process server in Azure : To fail back from Azure, you set up an Azure VM to act as a process server to handle replication from Azure. Questa VM può essere eliminata al termine del failback.
- **Connessione VPN**: per eseguire il failback, è necessaria una connessione VPN (o ExpressRoute) dalla rete di Azure al sito locale.
- **Server di destinazione master separato:** per impostazione predefinita, il server di destinazione master installato con il server di configurazione nella macchina virtuale VMware locale gestisce il failback. Se è necessario eseguire il failback di grandi volumi di traffico, configurare un server di destinazione master locale separato a questo scopo.
- **Criteri di failback**: per eseguire la replica nel sito locale, è necessario un criterio di failback, che viene creato automaticamente quando si creano i criteri di replica dal sito locale ad Azure.
    - Questo criterio viene associato automaticamente al server di configurazione.
    - Non è possibile modificare questo criterio.
    - Valori dei criteri: soglia RPO - 15 minuti; Conservazione del punto di ripristino - 24 ore; Frequenza snapshot coerente con l'app - 60 minuti.

Ulteriori informazioni su VMware/riprotezione fisica e failback:
- [Esaminare](vmware-azure-reprotect.md#before-you-begin) i requisiti aggiuntivi per la riprotezione e il failback.
- [Distribuire](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) un server di elaborazione in Azure.Deploy a process server in Azure.
- [Distribuire](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) un server di destinazione master separato.

Quando si riprotegge le macchine virtuali di Azure in locale, è possibile specificare che si vuole eseguire il failback al percorso originale o a un percorso alternativo.

- **Ripristino della posizione originale:** viene eseguito il failover da Azure alla stessa macchina locale di origine, se esistente. In questo scenario, solo le modifiche vengono replicate in locale.
- **Ripristino percorso alternativo:** se il computer locale non esiste, è possibile eseguire il failback da Azure in un percorso alternativo. Quando si riprotegge la macchina virtuale di Azure in locale, viene creato il computer locale. La replica completa dei dati viene eseguita da Azure a locale. - [Esaminare](concepts-types-of-failback.md) i requisiti e le limitazioni per il failback della posizione.- Review the requirements and limitations for location failback.



## <a name="hyper-v-reprotectionfailback"></a>Riprotezione/failback Hyper-V

Per riproteggere ed eseguire il failback delle macchine virtuali Hyper-V da Azure a locale:

- È possibile eseguire solo il failback delle macchine virtuali Hyper-V replicando usando un account di archiviazione. Il failback delle macchine virtuali Hyper-V che eseguono la replica utilizzando dischi gestiti non è supportato.
- Gli host Hyper-V locali (o System Center VMM se usati) devono essere connessi ad Azure.On-premises Hyper-V hosts (or System Center VMM if used) should be connected to Azure.
- Si esegue un failback pianificato da Azure in locale.
- Non è necessario configurare componenti specifici per il failback della macchina virtuale Hyper-V.No specific components need to be set up for Hyper-V VM failback.
- Durante il failover pianificato, è possibile selezionare le opzioni per sincronizzare i dati prima del failback:During planned failover, you can select options to synchronize data before failback:
    - **Sincronizza dati prima del failover:** questa opzione riduce al minimo i tempi di inattività per le macchine virtuali durante la sincronizzazione delle macchine senza arrestarle.
        - Fase 1: crea uno snapshot della macchina virtuale di Azure e lo copia nell'host Hyper-V locale. La macchina continua l'esecuzione in Azure.
        - Fase 2: arresta la macchina virtuale di Azure in modo che non si verifichino nuove modifiche. Il set finale di modifiche delta viene trasferito al server locale e la macchina virtuale locale viene avviata.
    - **Sincronizza dati solo durante**il failover: questa opzione è più veloce perché ci aspettiamo che la maggior parte del disco sia cambiata e pertanto non esegua calcoli checksum. Esegue il download del disco. È consigliabile usare questa opzione se la macchina virtuale è stata eseguita in Azure per un certo periodo di tempo (un mese o più) o se la macchina virtuale locale è stata eliminata.

[Altre informazioni](hyper-v-azure-failback.md) sulla riprotezione e il failback di Hyper-V.

Quando si riprotegge le macchine virtuali di Azure in locale, è possibile specificare che si vuole eseguire il failback al percorso originale o a un percorso alternativo.

- **Ripristino della posizione originale:** viene eseguito il failover da Azure alla stessa macchina locale di origine, se esistente. In questo scenario, si seleziona una delle opzioni di sincronizzazione descritte nella procedura precedente.
- **Ripristino percorso alternativo:** se il computer locale non esiste, è possibile eseguire il failback da Azure in un percorso alternativo. Quando si riprotegge la macchina virtuale di Azure in locale, viene creato il computer locale. Con questa opzione, è consigliabile selezionare l'opzione per sincronizzare i dati prima del failover
- [Esaminare](hyper-v-azure-failback.md) i requisiti e le limitazioni per il failback della posizione.


Dopo il failback al sito locale, si abilita **Replica inversa** per avviare la replica della macchina virtuale in Azure, completando il ciclo.




## <a name="next-steps"></a>Passaggi successivi
- Eseguire il failover di [macchine virtuali VMware specifiche](vmware-azure-tutorial-failover-failback.md)
- Eseguire il failover di [macchine virtuali Hyper-V specifiche.](hyper-v-azure-failover-failback-tutorial.md)
- [Creare](site-recovery-create-recovery-plans.md) un piano di ripristino.
- Eseguire il failover delle macchine virtuali in un piano di [ripristino.](site-recovery-failover.md)
- [Prepararsi per](vmware-azure-failback.md) Riprotezione e failback di VMware.
- Eseguire il failback delle [macchine virtuali Hyper-V](hyper-v-azure-failback.md).

