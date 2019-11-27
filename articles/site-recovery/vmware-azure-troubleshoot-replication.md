---
title: Risolvere i problemi di replica per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di replica comunemente riscontrati durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 7237bb7e0538ba1a9b6333ccb6589efe657a247d
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423958"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Risolvere i problemi di replica per macchine virtuali VMware e server fisici

Questo articolo descrive alcuni problemi comuni ed errori specifici che possono verificarsi quando si esegue la replica di VM VMware e server fisici locali in Azure usando [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Passaggio 1: monitorare l'integrità del server di elaborazione

Site Recovery usa il [server di elaborazione](vmware-physical-azure-config-process-server-overview.md#process-server) per ricevere e ottimizzare i dati replicati e per inviarli ad Azure.

Si consiglia di monitorare l'integrità dei server di elaborazione nel portale per assicurarsi che siano connessi e funzionino correttamente e che la replica sia in corso per le macchine di origine associate al server di elaborazione.

- Informazioni [sul monitoraggio dei server di](vmware-physical-azure-monitor-process-server.md) elaborazione.
- [Esaminare le procedure consigliate](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Risolvere i problemi relativi](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) all'integrità del server di elaborazione.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passaggio 2: risolvere i problemi di connettività e di replica

Gli errori di replica iniziali e in corso spesso sono causati da problemi di connettività tra il server di origine e il server di elaborazione oppure tra il server di elaborazione e Azure. 

Per risolvere questi problemi, [risolvere i problemi relativi alla connettività e alla replica](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passaggio 3: risolvere i problemi relativi ai computer di origine che non sono disponibili per la replica

Quando si tenta di scegliere la macchina di origine per cui abilitare la replica tramite Site Recovery, è possibile che la macchina non sia disponibile per uno dei motivi seguenti:

* **Due macchine virtuali con lo stesso UUID di istanza**: se due macchine virtuali in vCenter hanno lo stesso UUID di istanza, nella portale di Azure viene visualizzata la prima macchina virtuale individuata dal server di configurazione. Per risolvere questo problema, verificare che uno stesso UUID di istanza non sia assegnato a due macchine virtuali. Questo scenario viene comunemente visualizzato in istanze in cui una macchina virtuale di backup diventa attiva e viene registrata nei record di individuazione. Vedere [Azure Site Recovery da VMware ad Azure: come pulire le voci duplicate o non aggiornate](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) da risolvere.
* **Credenziali utente vCenter non corrette**: assicurarsi di aver aggiunto le credenziali vCenter corrette quando si configura il server di configurazione tramite il modello OVF o la configurazione unificata. Per verificare le credenziali aggiunte durante la configurazione, consultare [Modificare le credenziali per l'individuazione automatica](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **privilegi insufficienti per vCenter**: se le autorizzazioni fornite per accedere a vCenter non hanno le autorizzazioni necessarie, potrebbe verificarsi un errore di individuazione delle macchine virtuali. Assicurarsi che le autorizzazioni descritte in [Preparare un account per l'individuazione automatica](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) vengano aggiunte all'account utente di vCenter.
* **Server di gestione di Azure Site Recovery**: se la macchina virtuale viene usata come server di gestione in uno o più dei ruoli seguenti: server di configurazione/scale-out del server di elaborazione/server di destinazione master, non sarà possibile scegliere la macchina virtuale dal portale. I server di gestione non possono essere replicati.
* **Già protetto/** sottoposta a failover tramite i servizi Azure Site Recovery: se la macchina virtuale è già protetta o è stata sottoposta a failover tramite Site Recovery, la macchina virtuale non è disponibile per la selezione per la protezione nel portale. Assicurarsi che la macchina virtuale che si sta cercando nel portale non sia già protetta da un altro utente o con altre sottoscrizioni.
* **vCenter non connesso**: verificare se vCenter si trova in stato connesso. Per verificare, passare all'insieme di credenziali di Servizi di ripristino > infrastruttura di Site Recovery > Server di configurazione > fare clic sul server di configurazione corrispondente > viene visualizzato un pannello a destra con i dettagli dei server associati. Controllare se vCenter è connesso. Se lo stato è "non connesso", risolvere il problema e quindi [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server) nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **ESXi**spento: se l'host ESXi in cui risiede la macchina virtuale si trova nello stato spento, la macchina virtuale non verrà elencata o non sarà selezionabile nella portale di Azure. Accendere l'host ESXi e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server) nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **Riavvio in sospeso**: se è presente un riavvio in sospeso nella macchina virtuale, non sarà possibile selezionare il computer in portale di Azure. Assicurarsi di completare le attività di riavvio in sospeso e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.
* **IP non trovato**: se alla macchina virtuale non è associato un indirizzo IP valido, non sarà possibile selezionare il computer in portale di Azure. Assicurarsi di assegnare un indirizzo IP valido alla macchina virtuale e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Risolvere i problemi relativi alle macchine virtuali protette in grigio nel portale

Le macchine virtuali replicate in Site Recovery non sono disponibili nel portale di Azure se nel sistema sono presenti voci duplicate. Per informazioni su come eliminare le voci obsolete e risolvere il problema, vedere [Azure Site Recovery da VMware ad Azure: come pulire le voci duplicate o non aggiornate](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Nessun punto di ripristino coerente con l'arresto anomalo del sistema disponibile per la macchina virtuale negli ultimi minuti ' XXX '

Di seguito sono elencati alcuni dei problemi più comuni.

### <a name="initial-replication-issues-error-78169"></a>Problemi di replica iniziale [errore 78169]

In precedenza, assicurandosi che non siano presenti problemi di connettività, larghezza di banda o sincronizzazione dell'ora, assicurarsi che:

- Nessun software antivirus sta bloccando Azure Site Recovery. Per [altre](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) informazioni, vedere esclusioni di cartelle necessarie per Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Computer di origine con varianza elevata [errore 78188]

Possibili cause:
- La frequenza di modifica dei dati (byte scritti/sec) sui dischi elencati della macchina virtuale è superiore ai [limiti Azure Site Recovery supportati](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) per il tipo di account di archiviazione di destinazione della replica.
- Si è verificato un picco improvviso nella varianza a causa di una quantità elevata di dati in sospeso per il caricamento.

Per risolvere il problema:
- Verificare che sia stato effettuato il provisioning del tipo di account di archiviazione di destinazione (standard o Premium) in base al requisito della velocità di varianza all'origine.
- Se è già in corso la replica in un disco gestito Premium (tipo asrseeddisk), assicurarsi che le dimensioni del disco supportino la varianza osservata in base ai limiti di Site Recovery. Se necessario, è possibile aumentare la dimensione di asrseeddisk. Seguire questa procedura:
    - Passare al pannello dischi del computer replicato interessato e copiare il nome del disco di replica
    - Passa a questo disco gestito di replica
    - Nel pannello panoramica potrebbe essere visualizzato un banner che informa che è stato generato un URL di firma di accesso condiviso. Fare clic su questo banner e annullare l'esportazione. Ignorare questo passaggio se il banner non viene visualizzato.
    - Non appena viene revocato l'URL di firma di accesso condiviso, passare al pannello configurazione del disco gestito e aumentare le dimensioni in modo che ASR supporti la varianza osservata sul disco di origine
- Se la varianza osservata è temporanea, attendere alcune ore prima che il caricamento dei dati in sospeso si aggiorni e crei punti di ripristino.
- Se il disco contiene dati non critici come log temporanei, dati di test e così via, provare a trasferire questi dati altrove o a escludere completamente il disco dalla replica
- Se il problema persiste, utilizzare Site Recovery [Deployment Planner](site-recovery-deployment-planner.md#overview) per pianificare la replica.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Computer di origine senza heartbeat [errore 78174]

Questo problema si verifica quando Azure Site Recovery agente di mobilità nel computer di origine non comunica con il server di configurazione (CS).

Per risolvere il problema, attenersi alla procedura seguente per verificare la connettività di rete dalla macchina virtuale di origine al server di configurazione:

1. Verificare che il computer di origine sia in esecuzione.
2. Accedere al computer di origine usando un account con privilegi di amministratore.
3. Verificare che i servizi seguenti siano in esecuzione e, in caso contrario, riavviare i servizi:
   - Svagents (InMage Scout VX Agent)
   - Servizio dell'applicazione InMage Scout
4. Nel computer di origine esaminare i log nel percorso per i dettagli dell'errore:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Server di elaborazione senza heartbeat [errore 806]
Se non è presente alcun heartbeat dal server di elaborazione (PS), verificare che:
1. La macchina virtuale PS è attiva e in esecuzione
2. Controllare i log seguenti in PS per informazioni sull'errore:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Server di destinazione master senza heartbeat [errore 78022]

Questo problema si verifica quando Azure Site Recovery agente di mobilità nella destinazione master non comunica con il server di configurazione.

Per risolvere il problema, attenersi alla procedura seguente per verificare lo stato del servizio:

1. Verificare che la macchina virtuale di destinazione master sia in esecuzione.
2. Accedere alla macchina virtuale di destinazione master usando un account con privilegi di amministratore.
    - Verificare che il servizio svagents sia in esecuzione. Se è in esecuzione, riavviare il servizio
    - Controllare i log nel percorso per i dettagli dell'errore:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. Per registrare la destinazione master con il server di configurazione, passare alla cartella **%ProgramData%\ASR\Agent**ed eseguire il comando seguente nel prompt dei comandi:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID errore 78144-nessun punto di ripristino coerente con l'app disponibile per la macchina virtuale negli ultimi minuti ' XXX '

Sono stati apportati miglioramenti nell'agente di mobilità [9,23](vmware-physical-mobility-service-overview.md#from-923-version-onwards) & versioni [9,27](site-recovery-whats-new.md#update-rollup-39) per gestire i comportamenti degli errori di installazione VSS. Assicurarsi di avere le versioni più recenti per informazioni aggiuntive sulla risoluzione dei problemi relativi agli errori del servizio Copia Shadow del volume.

Di seguito sono elencati alcuni dei problemi più comuni.

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: problema noto in SQL Server 2008/2008 R2 
**Come risolvere** : si è verificato un problema noto con SQL Server 2008/2008 R2. [Per un server che ospita SQL Server 2008 R2, fare riferimento a questo articolo della knowledge Azure Site Recovery dell'agente o di un altro backup VSS non componente.](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Motivo 2: i processi Azure Site Recovery hanno esito negativo nei server che ospitano qualsiasi versione di SQL Server istanze con AUTO_CLOSE database 
**Come correggere** : vedere l' [articolo](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) della Knowledge Knowledge 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: problema noto in SQL Server 2016 e 2017
**Come correggere** : vedere l' [articolo](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) della Knowledge Knowledge 


### <a name="more-causes-due-to-vss-related-issues"></a>Altre cause a causa di problemi correlati a VSS:

Per risolvere il problema, controllare i file nella macchina di origine per ottenere il codice di errore esatto per l'errore:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Come individuare gli errori nel file
Per cercare la stringa "vacpError", aprire il file vacp. log in un editor
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Nell'esempio precedente **2147754994** è il codice di errore che indica l'errore, come illustrato di seguito.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS writer non è installato-errore 2147221164 

*Procedura*: per generare un tag di coerenza dell'applicazione, Azure Site Recovery utilizza il servizio Copia Shadow del volume (VSS) di Microsoft. Viene installato un provider VSS per la relativa operazione per eseguire snapshot di coerenza delle app. Questo provider VSS viene installato come servizio. Se il servizio provider VSS non è installato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo con ID errore 0x80040154 "classe non registrata". </br>
Vedere l' [articolo per la risoluzione dei problemi di installazione VSS Writer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS writer è disabilitato-errore 2147943458

**Procedura**: per generare un tag di coerenza dell'applicazione, Azure Site Recovery utilizza il servizio Copia Shadow del volume (VSS) di Microsoft. Viene installato un provider VSS per la relativa operazione per eseguire snapshot di coerenza delle app. Questo provider VSS viene installato come servizio. Se il servizio del provider VSS è disabilitato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo con ID errore "il servizio specificato è disabilitato e non può essere avviato (0x80070422)". </br>

- Se il servizio Copia Shadow del volume è disabilitato,
    - Verificare che il tipo di avvio del servizio provider VSS sia impostato su **automatico**.
    - Riavviare i servizi seguenti:
        - Servizio VSS
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED del PROVIDER VSS-errore 2147754756

**Procedura**: per generare un tag di coerenza dell'applicazione, Azure Site Recovery utilizza il servizio Copia Shadow del volume (VSS) di Microsoft. Controllare se il servizio provider di Azure Site Recovery VSS è installato o meno. </br>

- Riprovare l'installazione del provider utilizzando i comandi seguenti:
- Disinstalla provider esistente: C:\Programmi (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
- Reinstallare: C:\Programmi (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Install. cmd
 
Verificare che il tipo di avvio del servizio provider VSS sia impostato su **automatico**.
    - Riavviare i servizi seguenti:
        - Servizio VSS
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). La community è molto attiva e uno dei tecnici Microsoft potrà fornire l'assistenza richiesta.
