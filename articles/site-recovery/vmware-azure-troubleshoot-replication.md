---
title: Risolvere i problemi di replica per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di replica comunemente riscontrati durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 54686a96385532e17fe0ac6e59058b91b40c1342
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742557"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Risolvere i problemi di replica per macchine virtuali VMware e server fisici

Questo articolo descrive alcuni problemi comuni ed errori specifici che possono verificarsi quando si esegue la replica di VM VMware e server fisici locali in Azure usando [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Passaggio 1: Monitorare l'integrità del server di elaborazione

Site Recovery usa il [server di elaborazione](vmware-physical-azure-config-process-server-overview.md#process-server) per ricevere e ottimizzare i dati replicati e per inviarli ad Azure.

Si consiglia di monitorare l'integrità dei server di elaborazione nel portale per assicurarsi che siano connessi e funzionino correttamente e che la replica sia in corso per le macchine di origine associate al server di elaborazione.

- Informazioni [sul monitoraggio dei server di](vmware-physical-azure-monitor-process-server.md) elaborazione.
- [Esaminare le procedure consigliate](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Risolvere i problemi relativi](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) all'integrità del server di elaborazione.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passaggio 2: Risolvere i problemi di connettività e di replica

Gli errori di replica iniziali e in corso spesso sono causati da problemi di connettività tra il server di origine e il server di elaborazione oppure tra il server di elaborazione e Azure. 

Per risolvere questi problemi, [risolvere i problemi relativi alla connettività e alla replica](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passaggio 3: Risolvere i problemi dei computer di origine che non sono disponibili per la replica

Quando si tenta di scegliere la macchina di origine per cui abilitare la replica tramite Site Recovery, è possibile che la macchina non sia disponibile per uno dei motivi seguenti:

* **Due macchine virtuali con lo stesso UUID di istanza**: Se In vCenter due macchine virtuali hanno lo stesso UUID di istanza, nel portale di Azure viene visualizzata solo la prima macchina virtuale individuata dal server di configurazione. Per risolvere questo problema, verificare che uno stesso UUID di istanza non sia assegnato a due macchine virtuali. Questo scenario viene comunemente visualizzato in istanze in cui una macchina virtuale di backup diventa attiva e viene registrata nei record di individuazione. Per la risoluzione, vedere [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) (Da VMware ad Azure con Azure Site Recovery: come eseguire la pulizia delle voci duplicate o non aggiornate).
* **Credenziali utente di vCenter non corrette**: Assicurarsi di aver aggiunto le credenziali di vCenter corrette durante la configurazione del server di configurazione usando il modello OVF o l'installazione unificata. Per verificare le credenziali aggiunte durante la configurazione, consultare [Modificare le credenziali per l'individuazione automatica](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Privilegi insufficienti per vCenter**: Se le autorizzazioni fornite per accedere a vCenter non dispongono dei privilegi necessari, potrebbero verificarsi errori nell'individuazione delle macchine virtuali. Assicurarsi che le autorizzazioni descritte in [Preparare un account per l'individuazione automatica](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) vengano aggiunte all'account utente di vCenter.
* **Server di gestione di Azure Site Recovery**: se la macchina virtuale viene usata come server di gestione in uno o più dei ruoli server di configurazione, server di elaborazione scale-out o server di destinazione master, non sarà possibile scegliere la macchina virtuale dal portale. I server di gestione non possono essere replicati.
* **Già protetta o sottoposta a failover tramite i servizi di Azure Site Recovery**: se la macchina virtuale è già protetta oppure ne è già stato eseguito il failover tramite Site Recovery, non risulterà disponibile per la protezione nel portale. Assicurarsi che la macchina virtuale che si sta cercando nel portale non sia già protetta da un altro utente o con altre sottoscrizioni.
* **vCenter non connesso**: controllare se vCenter è in stato connesso. Per verificare, passare all'insieme di credenziali di Servizi di ripristino > infrastruttura di Site Recovery > Server di configurazione > fare clic sul server di configurazione corrispondente > viene visualizzato un pannello a destra con i dettagli dei server associati. Controllare se vCenter è connesso. Se lo stato è "non connesso", risolvere il problema e quindi [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server) nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **ESXi spento**: Se l'host ESXi in cui risiede la macchina virtuale è spento, la macchina virtuale non verrà elencata o non sarà selezionabile nel portale di Azure. Accendere l'host ESXi e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server) nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **In attesa di riavvio**: se è presente un riavvio in sospeso nella macchina virtuale, non sarà possibile selezionare la macchina nel portale di Azure. Assicurarsi di completare le attività di riavvio in sospeso e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.
* **IP non trovato**: se alla macchina virtuale non è associato un indirizzo IP valido, non sarà possibile selezionare la macchina nel portale di Azure. Assicurarsi di assegnare un indirizzo IP valido alla macchina virtuale e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Risolvere i problemi relativi alle macchine virtuali protette in grigio nel portale

Le macchine virtuali replicate in Site Recovery non sono disponibili nel portale di Azure se nel sistema sono presenti voci duplicate. Per informazioni su come eliminare le voci non aggiornate e risolvere il problema, vedere [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) (Da VMware ad Azure con Azure Site Recovery: come eseguire la pulizia delle voci duplicate o non aggiornate).

## <a name="common-errors-and-solutions"></a>Errori e soluzioni comuni

### <a name="initial-replication-issues-error-78169"></a>Problemi di replica iniziale [errore 78169]

In precedenza, assicurandosi che non siano presenti problemi di connettività, larghezza di banda o sincronizzazione dell'ora, assicurarsi che:

- Nessun software antivirus sta bloccando Azure Site Recovery. Per [altre](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) informazioni, vedere esclusioni di cartelle necessarie per Azure Site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Punti di ripristino coerenti con l'app mancanti [errore 78144]

 Questa situazione si verifica a causa di problemi relativi al servizio Copia Shadow del volume (VSS). Per risolvere il problema: 
 
- Verificare che la versione installata dell'agente di Azure Site Recovery sia almeno 9.22.2. 
- Verificare che il provider VSS sia installato come servizio nei servizi Windows e verificare anche il servizio componente MMC per verificare che sia elencato Azure Site Recovery provider VSS.
- Se il provider VSS non è installato, vedere l' [articolo sulla risoluzione dei problemi di installazione](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Se il servizio Copia Shadow del volume è disabilitato,
    - Verificare che il tipo di avvio del servizio provider VSS sia impostato su **automatico**.
    - Riavviare i servizi seguenti:
        - Servizio VSS
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

- Se si eseguono carichi di lavoro SQL o Exchange, controllare i log di questi writer di applicazioni per individuare eventuali errori. Negli articoli seguenti vengono acquisiti gli errori frequenti e la relativa risoluzione:
    -  [L'opzione di chiusura automatica di SQL Server database è impostata su TRUE](https://support.microsoft.com/help/4504104)
    - [SQL Server 2008 R2 che genera un errore non irreversibile](https://support.microsoft.com/help/4504103)
    - [Problema noto in SQL Server 2016 e 2017](https://support.microsoft.com/help/4493364)
    - [Problema comune con i server Exchange 2013 e 2016](https://support.microsoft.com/help/4037535)


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
   - Svagents (Inmage Scout VX Agent)
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

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID errore 78144-nessun punto di ripristino coerente con l'app disponibile per la macchina virtuale negli ultimi minuti ' XXX '

Di seguito sono elencati alcuni dei problemi più comuni.

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: Problema noto in SQL Server 2008/2008 R2 
**Come risolvere il** problema: Si è verificato un problema noto con SQL Server 2008/2008 R2. [Per un server che ospita SQL Server 2008 R2, fare riferimento a questo articolo della knowledge Azure Site Recovery dell'agente o di un altro backup VSS non componente.](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: Azure Site Recovery i processi hanno esito negativo nei server che ospitano qualsiasi versione di SQL Server istanze con database AUTO_CLOSE 
**Come risolvere il** problema: Vedere l' [articolo](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) della Knowledge Knowledge 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: Problema noto in SQL Server 2016 e 2017
**Come risolvere il** problema: Vedere l' [articolo](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) della Knowledge Knowledge 


### <a name="more-causes-due-to-vss-related-issues"></a>Altre cause a causa di problemi correlati a VSS:

Per risolvere il problema, controllare i file nella macchina di origine per ottenere il codice di errore esatto per l'errore:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Come individuare gli errori nel file
Per cercare la stringa "vacpError", aprire il file vacp. log in un editor
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Nell'esempio precedente **2147754994** è il codice di errore che indica l'errore, come illustrato di seguito.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS writer non è installato-errore 2147221164 

*Come risolvere il*problema: Per generare un tag di coerenza dell'applicazione, Azure Site Recovery utilizza il servizio Copia Shadow del volume di Microsoft (VSS). Viene installato un provider VSS per la relativa operazione per eseguire snapshot di coerenza delle app. Questo provider VSS viene installato come servizio. Se il servizio provider VSS non è installato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo con ID errore 0x80040154 "classe non registrata". </br>
Vedere l' [articolo per la risoluzione dei problemi di installazione VSS Writer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS writer è disabilitato-errore 2147943458

**Come risolvere il**problema: Per generare un tag di coerenza dell'applicazione, Azure Site Recovery utilizza il servizio Copia Shadow del volume di Microsoft (VSS). Viene installato un provider VSS per la relativa operazione per eseguire snapshot di coerenza delle app. Questo provider VSS viene installato come servizio. Se il servizio del provider VSS è disabilitato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo con ID errore "il servizio specificato è disabilitato e non può essere avviato (0x80070422)". </br>

- Se il servizio Copia Shadow del volume è disabilitato,
    - Verificare che il tipo di avvio del servizio provider VSS sia impostato su **automatico**.
    - Riavviare i servizi seguenti:
        - Servizio VSS
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>PROVIDER VSS NOT_REGISTERED-errore 2147754756

**Come risolvere il**problema: Per generare un tag di coerenza dell'applicazione, Azure Site Recovery utilizza il servizio Copia Shadow del volume di Microsoft (VSS). Controllare se il servizio provider di Azure Site Recovery VSS è installato o meno. </br>

- Riprovare l'installazione del provider utilizzando i comandi seguenti:
- Disinstalla provider esistente: C:\Programmi (x86) \Microsoft Azure site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Reinstallare C:\Programmi (x86) \Microsoft Azure site Recovery\agent\InMageVSSProvider_Install.cmd
 
Verificare che il tipo di avvio del servizio provider VSS sia impostato su **automatico**.
    - Riavviare i servizi seguenti:
        - Servizio VSS
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). La community è molto attiva e uno dei tecnici Microsoft potrà fornire l'assistenza richiesta.
