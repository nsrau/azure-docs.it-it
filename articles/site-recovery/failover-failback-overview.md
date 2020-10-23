---
title: Informazioni sul failover e il failback in Azure Site Recovery
description: Informazioni sul failover e sui problemi in Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 3617683200aa3ffba08061b70993613fd0cc7241
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369880"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Informazioni sul failover/failback del ripristino di emergenza locale

Questo articolo fornisce una panoramica del failover e del failback durante il ripristino di emergenza di computer locali in Azure con [Azure Site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Fasi di ripristino

Il failover e il failback in Site Recovery hanno quattro fasi:

- **Fase 1: eseguire il failover dall'ambiente locale**: dopo aver configurato la replica in Azure per i computer locali, quando il sito locale diventa inattivo, si verificano errori di tali computer in Azure. Dopo il failover, le macchine virtuali di Azure vengono create da dati replicati.
- **Fase 2: riproteggere le macchine virtuali**di Azure: in Azure è possibile riproteggere le macchine virtuali di Azure in modo che inizino la replica nel sito locale. La macchina virtuale locale (se disponibile) viene spenta durante la riprotezione, per garantire la coerenza dei dati.
- **Fase 3:** effettuare il failover da Azure: quando il sito locale è di nuovo in esecuzione normalmente, viene eseguito un altro failover, questa volta per eseguire il failback delle macchine virtuali di Azure nel sito locale. È possibile eseguire il failback nel percorso originale dal quale è stato effettuato il failover o in un percorso alternativo.
- **Fase 4: riproteggere i computer locali**: dopo il failback, abilitare di nuovo la replica dei computer locali in Azure.

## <a name="failover"></a>Failover

Si esegue un failover come parte della strategia di continuità aziendale e ripristino di emergenza (BCDR).

- Come primo passaggio della strategia BCDR, è possibile replicare i computer locali in Azure con cadenza continuativa. Gli utenti accedono ai carichi di lavoro e alle app in esecuzione nei computer di origine locali.
- Se la necessità si verifica, ad esempio in caso di interruzione in locale, si verificherà un errore di replica delle macchine virtuali in Azure. Le macchine virtuali di Azure vengono create usando i dati replicati.
- Per la continuità aziendale, gli utenti possono continuare ad accedere alle app nelle macchine virtuali di Azure.

Il failover è un'attività a due fasi:

- **Failover**: il failover che crea e visualizza una macchina virtuale di Azure usando il punto di ripristino selezionato.
- **Commit**: dopo il failover, la macchina virtuale viene verificata in Azure:
    - È quindi possibile eseguire il commit del failover nel punto di ripristino selezionato oppure selezionare un punto diverso per il commit.
    - Dopo aver eseguito il commit del failover, non è possibile modificare il punto di ripristino.


## <a name="connect-to-azure-after-failover"></a>Connettersi ad Azure dopo il failover

Per connettersi alle macchine virtuali di Azure create dopo il failover tramite RDP/SSH, esistono diversi requisiti.

**Failover** | **Posizione** | **Actions**
--- | --- | ---
**VM di Azure (Windows (** | Nel computer locale prima del failover | **Accesso tramite Internet**: abilitare RDP. Assicurarsi che vengano aggiunte regole TCP e UDP per **pubblico**e che il protocollo RDP sia consentito per tutti i profili in **Windows Firewall**  >  **app consentite**.<br/><br/> **Accesso tramite VPN da sito a sito**: abilitare RDP nel computer. Verificare che il protocollo RDP sia consentito nella **Windows Firewall**  ->  **app e funzionalità consentite**per le reti di **dominio e private** .<br/><br/>  Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/kb/3031135)<br/><br/> Quando si attiva un failover, verificare che nella macchina virtuale non siano in sospeso aggiornamenti di Windows. Windows Update potrebbe iniziare quando si esegue il failover e non sarà possibile accedere alla macchina virtuale fino a quando non vengono eseguiti gli aggiornamenti.
**VM di Azure che esegue Windows** | Nella macchina virtuale di Azure dopo il failover |  [Aggiungere un indirizzo IP pubblico](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) per la macchina virtuale.<br/><br/> Le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta RDP.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale. Se non è possibile connettersi, verificare che la macchina virtuale sia in esecuzione ed esaminare i suggerimenti per la [risoluzione dei problemi](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM di Azure che esegue Linux** | Nel computer locale prima del failover | Assicurarsi che il servizio Secure Shell nella macchina virtuale sia impostato per l'avvio automatico all'avvio del sistema.<br/><br/> Verificare che le regole firewall accettino la connessione SSH.
**VM di Azure che esegue Linux** | Nella macchina virtuale di Azure dopo il failover | Le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e nella subnet di Azure a cui è connessa devono consentire le connessioni in ingresso alla porta SSH.<br/><br/> [Aggiungere un indirizzo IP pubblico](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) per la macchina virtuale.<br/><br/> Selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.<br/><br/>

## <a name="types-of-failover"></a>Tipi di failover

Site Recovery offre diverse opzioni di failover.

**Failover** | **Dettagli** | **Ripristino** | **Workflow**.
--- | --- | --- | ---
**Failover di test** | Usato per eseguire un'esercitazione che convalida la strategia di BCDR, senza perdita di dati o tempi di inattività.| Crea una copia della macchina virtuale in Azure, senza alcun impatto sulla replica in corso o sull'ambiente di produzione. | 1. eseguire un failover di test in una singola macchina virtuale o in più macchine virtuali in un piano di ripristino.<br/><br/> 2. Selezionare un punto di ripristino da usare per il failover di test.<br/><br/> 3. Selezionare una rete di Azure in cui verrà posizionata la macchina virtuale di Azure quando viene creata dopo il failover. La rete viene utilizzata solo per il failover di test.<br/><br/> 4. Verificare che il drill abbia funzionato come previsto. Site Recovery pulisce automaticamente le macchine virtuali create in Azure durante il drill-through.
**Failover pianificato-Hyper-V**  | Usato in genere per i tempi di inattività pianificati.<br/><br/> Le macchine virtuali di origine vengono arrestate. I dati più recenti vengono sincronizzati prima di avviare il failover. | Nessuna perdita di dati per il flusso di lavoro pianificato. | 1. pianificare una finestra di manutenzione del tempo di inattività e inviare notifiche agli utenti.<br/><br/> 2. portare offline le app per gli utenti.<br/><br/> 3. avviare un failover pianificato con il punto di ripristino più recente. Il failover non viene eseguito se il computer non viene arrestato o se vengono rilevati errori.<br/><br/> 4. dopo il failover, verificare che la macchina virtuale di Azure di replica sia attiva in Azure.<br/><br/> 5. eseguire il commit del failover per completare la configurazione. L'azione di commit Elimina tutti i punti di ripristino.
**Failover-Hyper-V** | In genere viene eseguito se si verifica un'interruzione non pianificata o se il sito primario non è disponibile.<br/><br/> Facoltativamente, arrestare la VM e sincronizzare le modifiche finali prima di avviare il failover.  | Perdita di dati minima per le app. | 1. avviare il piano BCDR. <br/><br/> 2. avviare un failover. Specificare se Site Recovery deve arrestare la macchina virtuale e sincronizzare/replicare le modifiche più recenti prima di attivare il failover.<br/><br/> 3. è possibile effettuare il failover a una serie di opzioni di punti di ripristino, riepilogate nella tabella seguente.<br/><br/> Se non si Abilita l'opzione per arrestare la macchina virtuale o se non Site Recovery possibile arrestarla, viene usato il punto di ripristino più recente.<br/>Il failover viene eseguito anche se non è possibile arrestare il computer.<br/><br/> 4. dopo il failover, verificare che la macchina virtuale di Azure di replica sia attiva in Azure.<br/> Se necessario, è possibile selezionare un punto di ripristino diverso dall'intervallo di conservazione di 24 ore.<br/><br/> 5. eseguire il commit del failover per completare la configurazione. L'azione di commit Elimina tutti i punti di ripristino disponibili.
**Failover-VMware** | In genere viene eseguito se si verifica un'interruzione non pianificata o se il sito primario non è disponibile.<br/><br/> Facoltativamente, specificare che Site Recovery dovrebbe provare ad attivare un arresto della macchina virtuale e per sincronizzare e replicare le modifiche finali prima di avviare il failover.  | Perdita di dati minima per le app. | 1. avviare il piano BCDR. <br/><br/> 2. avviare un failover da Site Recovery. Specificare se Site Recovery deve provare ad attivare l'arresto e la sincronizzazione della macchina virtuale prima di eseguire il failover.<br/> Il failover viene eseguito anche se non è possibile arrestare i computer.<br/><br/> 3. dopo il failover, verificare che la macchina virtuale di Azure di replica sia attiva in Azure. <br/>Se necessario, è possibile selezionare un punto di ripristino diverso dall'intervallo di conservazione di 72 ore.<br/><br/> 5. eseguire il commit del failover per completare la configurazione. L'azione di commit Elimina tutti i punti di ripristino.<br/> Per le macchine virtuali Windows Site Recovery disabilita gli strumenti VMware durante il failover.

## <a name="failover-processing"></a>Elaborazione del failover

In alcuni scenari il failover richiede un'altra elaborazione il cui completamento richiede da 8 a 10 minuti. Si possono notare tempi di failover di test più lunghi per:

* VM VMware che eseguono una versione del servizio Mobility precedente alla 9.8.
* Server fisici.
* Macchine virtuali Linux VMware.
* VM Hyper-V protette come i server fisici.
* VM VMware che non hanno il servizio DHCP abilitato.
* VM VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Opzioni del punto di ripristino

Durante il failover, è possibile selezionare diverse opzioni per i punti di ripristino.

**Opzione** | **Dettagli**
--- | ---
**Più recente (RPO più basso)** | Questa opzione assicura l'obiettivo del punto di ripristino (RPO) più basso. Per prima cosa elabora tutti i dati inviati al servizio Site Recovery, per creare un punto di ripristino per ogni macchina virtuale, prima di eseguire il failover. Questo punto di ripristino include tutti i dati replicati per Site Recovery quando è stato attivato il failover.
**Elaborato più recente**  | Questa opzione esegue il failover delle macchine virtuali nel punto di ripristino più recente elaborato da Site Recovery. Per vedere il punto di ripristino più recente per una macchina virtuale specifica, selezionare **Punti di ripristino più recenti** nelle impostazioni della macchina virtuale. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
**Coerente con l'app più recente** |  Questa opzione esegue il failover delle macchine virtuali nel punto di ripristino coerente con l'applicazione più recente elaborato da Site Recovery, se sono abilitati punti di ripristino coerenti con l'app. Controllare il punto di ripristino più recente nelle impostazioni della macchina virtuale.
**Elaborato più recente per più macchine virtuali** | questa opzione è disponibile per i piani di ripristino con una o più macchine virtuali in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali in cui è abilitata l'impostazione eseguono il failover nel punto di ripristino coerente tra più macchine comune più recente. Tutte le altre macchine virtuali nel piano vengono sottoposti a failover al punto di ripristino elaborato più recente.
**Coerente con l'app più recente per più macchine virtuali** |  questa opzione è disponibile per i piani di ripristino con una o più macchine virtuali in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali che fanno parte di un gruppo di replica eseguono il failover nel punto di ripristino coerente a livello applicazione tra più macchine comune più recente. Le altre macchine virtuali eseguono il failover nel relativo punto di ripristino più recente coerente con l'applicazione.
**Impostazione personalizzata** | Usare questa opzione per eseguire il failover di una macchina virtuale specifica in un determinato punto di ripristino nel tempo. Questa opzione non è disponibile per i piani di ripristino.

> [!NOTE]
> Non è possibile migrare i punti di ripristino in un altro insieme di credenziali di servizi di ripristino

## <a name="reprotectionfailback"></a>Riprotezione/failback

Dopo il failover in Azure, le macchine virtuali di Azure replicate sono in uno stato non protetto.

- Come primo passaggio per eseguire il failback al sito locale, è necessario avviare le VM di Azure che eseguono la replica in locale. Il processo di riprotezione dipende dal tipo di computer di cui è stato eseguito il failover.
- Dopo la replica dei computer da Azure in locale, è possibile eseguire un failover da Azure al sito locale.
- Dopo che i computer sono ancora in esecuzione in locale, è possibile abilitare la replica in modo che vengano replicati in Azure per il ripristino di emergenza.

Il failback funziona nel modo seguente:

- Per eseguire il failback, una macchina virtuale richiede almeno un punto di ripristino per eseguire il failback. In un piano di ripristino, per tutte le macchine virtuali nel piano è necessario almeno un punto di ripristino.
- Si consiglia di usare il punto di ripristino **più recente** per eseguire il failback (si tratta di un punto coerente con l'arresto anomalo del sistema).
    - È disponibile un'opzione per il punto di ripristino coerente con l'app. In questo caso, una singola macchina virtuale viene ripristinata nell'ultimo punto di ripristino coerente con l'app disponibile. Per un piano di ripristino con un gruppo di replica, ogni gruppo di replica viene ripristinato nel relativo punto di ripristino disponibile comune.
    - I punti di ripristino coerenti con l'app possono essere indietro nel tempo e potrebbero verificarsi perdite di dati.
- Durante il failover da Azure al sito locale, Site Recovery arresta le macchine virtuali di Azure. Quando si esegue il commit del failover, Site Recovery rimuove le VM di Azure di cui è stato eseguito il failback in Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/riprotezione fisica/failback

Per la riprotezione e il failback di computer e server fisici VMware da Azure in locale, è necessaria un'infrastruttura di failback e sono previsti diversi requisiti.

- **Server di elaborazione temporaneo in Azure**: per eseguire il failback da Azure, è necessario configurare una macchina virtuale di Azure che funga da server di elaborazione per gestire la replica da Azure. Questa VM può essere eliminata al termine del failback.
- **Connessione VPN**: per eseguire il failback, è necessaria una connessione VPN (o ExpressRoute) dalla rete di Azure al sito locale.
- **Server di destinazione master separato**: per impostazione predefinita, il server di destinazione master installato con il server di configurazione nella macchina virtuale VMware locale gestisce il failback. Se è necessario eseguire il failback di grandi volumi di traffico, configurare un server di destinazione master locale separato a questo scopo.
- **Criteri di failback**: per eseguire la replica nel sito locale, è necessario un criterio di failback, che viene creato automaticamente quando si creano i criteri di replica dal sito locale ad Azure.
    - Questo criterio viene associato automaticamente al server di configurazione.
    - Non è possibile modificare questo criterio.
    - Valori dei criteri: soglia RPO-15 minuti; Conservazione dei punti di ripristino-24 ore; Frequenza snapshot coerenti con l'app-60 minuti.

Altre informazioni su VMware/riprotezione fisica e failback:
- [Esaminare](vmware-azure-reprotect.md#before-you-begin) i requisiti aggiuntivi per la riprotezione e il failback.
- [Distribuire](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) un server di elaborazione in Azure.
- [Distribuire](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) un server di destinazione master separato.

Quando si riproteggono le macchine virtuali di Azure in locale, è possibile specificare che si vuole eseguire il failback nel percorso originale o in un percorso alternativo.

- **Ripristino del percorso originale**: viene eseguito il failback da Azure allo stesso computer locale di origine, se esistente. In questo scenario, solo le modifiche vengono replicate in locale.
- **Ripristino in un percorso alternativo**: se il computer locale non esiste, è possibile eseguire il failback da Azure in un percorso alternativo. Quando si esegue la riprotezione della macchina virtuale di Azure in locale, viene creato il computer locale. La replica dei dati completa viene eseguita da Azure a locale. -- [Verificare](concepts-types-of-failback.md) i requisiti e le limitazioni per il failback della posizione.



## <a name="hyper-v-reprotectionfailback"></a>Riprotezione o failback di Hyper-V

Per eseguire la riprotezione e il failback di macchine virtuali Hyper-V da Azure a locale:

- È possibile eseguire il failback solo delle macchine virtuali Hyper-V che eseguono la replica usando un account di archiviazione. Il failback di macchine virtuali Hyper-V che si replicano con Managed Disks non è supportato.
- Gli host Hyper-V locali (o System Center VMM se usati) devono essere connessi ad Azure.
- Viene eseguito un failback pianificato da Azure a locale.
- Non è necessario configurare componenti specifici per il failback della macchina virtuale Hyper-V.
- Durante il failover pianificato, è possibile selezionare le opzioni per sincronizzare i dati prima del failback:
    - **Sincronizza i dati prima del failover**: questa opzione riduce al minimo i tempi di inattività per le macchine virtuali durante la sincronizzazione dei computer senza chiuderli.
        - Fase 1: acquisisce uno snapshot della macchina virtuale di Azure e lo copia nell'host Hyper-V locale. La macchina continua l'esecuzione in Azure.
        - Fase 2: arresta la macchina virtuale di Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche delta viene trasferito al server locale e viene avviata la macchina virtuale locale.
    - **Sincronizza i dati solo durante il failover**: questa opzione è più veloce perché si prevede che la maggior parte del disco sia cambiata e pertanto non esegue calcoli di checksum. Esegue il download del disco. Si consiglia di usare questa opzione se la macchina virtuale è stata eseguita in Azure per un periodo di tempo (un mese o più) o se la macchina virtuale locale è stata eliminata.

[Altre](hyper-v-azure-failback.md) informazioni sulla riprotezione e il failback di Hyper-V.

Quando si riproteggono le macchine virtuali di Azure in locale, è possibile specificare che si vuole eseguire il failback nel percorso originale o in un percorso alternativo.

- **Ripristino del percorso originale**: viene eseguito il failback da Azure allo stesso computer locale di origine, se esistente. In questo scenario si seleziona una delle opzioni di sincronizzazione descritte nella procedura precedente.
- **Ripristino in un percorso alternativo**: se il computer locale non esiste, è possibile eseguire il failback da Azure in un percorso alternativo. Quando si esegue la riprotezione della macchina virtuale di Azure in locale, viene creato il computer locale. Con questa opzione, è consigliabile selezionare l'opzione per sincronizzare i dati prima del failover.
- [Esaminare](hyper-v-azure-failback.md) i requisiti e le limitazioni per il failback della posizione.


Dopo aver eseguito il failback al sito locale, è possibile abilitare la **replica inversa** per avviare la replica della macchina virtuale in Azure, completando il ciclo.




## <a name="next-steps"></a>Passaggi successivi
- Eseguire il failover di [VM VMware specifiche](vmware-azure-tutorial-failover-failback.md)
- Eseguire il failover [di VM Hyper-V specifiche](hyper-v-azure-failover-failback-tutorial.md).
- [Creare](site-recovery-create-recovery-plans.md) un piano di ripristino.
- Eseguire [il failover delle macchine virtuali in un piano di ripristino](site-recovery-failover.md).
- [Prepara per](vmware-azure-failback.md) Riprotezione e failback di VMware.
- Eseguire il failback [di macchine virtuali Hyper-V](hyper-v-azure-failback.md).