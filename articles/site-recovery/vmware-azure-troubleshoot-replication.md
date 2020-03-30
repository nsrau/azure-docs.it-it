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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74423958"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Risolvere i problemi di replica per macchine virtuali VMware e server fisici

In questo articolo vengono descritti alcuni problemi comuni e errori specifici che possono verificarsi durante la replica di macchine virtuali VMware locali e server fisici in Azure tramite [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Passaggio 1: Monitorare l'integrità del server di elaborazioneStep 1: Monitor process server health

Site Recovery usa il server di [elaborazione](vmware-physical-azure-config-process-server-overview.md#process-server) per ricevere e ottimizzare i dati replicati e inviarli ad Azure.Site Recovery uses the process server to receive and optimize replicated data, and send it to Azure.

È consigliabile monitorare l'integrità dei server di elaborazione nel portale, assicurarsi che siano connessi e funzionino correttamente e che la replica proceda per i computer di origine associati al server di elaborazione.

- [Informazioni sul](vmware-physical-azure-monitor-process-server.md) monitoraggio dei server di elaborazione.
- [Esaminare le procedure consigliate](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Risolvere i problemi relativi all'integrità del](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) server di elaborazione.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passaggio 2: Risolvere i problemi di connettività e replicaStep 2: Troubleshoot connectivity and replication issues

Gli errori di replica iniziale e in corso sono spesso causati da problemi di connettività tra il server di origine e il server di elaborazione o tra il server di elaborazione e Azure.Initial and ongoing replication failures often are caused by connectivity issues between the source server and the process server or between the process server and Azure. 

Per risolvere questi problemi, [risolvere i problemi di connettività e replica](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passaggio 3: Risolvere i problemi relativi ai computer di origine non disponibili per la replicaStep 3: Troubleshoot source machines that aren't available for replication

Quando si tenta di scegliere la macchina di origine per cui abilitare la replica tramite Site Recovery, è possibile che la macchina non sia disponibile per uno dei motivi seguenti:

* **Due macchine virtuali con la stessa istanza UUID:** se due macchine virtuali nel vCenter hanno la stessa istanza UUID, la prima macchina virtuale individuata dal server di configurazione viene visualizzata nel portale di Azure.Two virtual machines with same instance UUID : If two virtual machines under the vCenter have the same instance UUID, the first virtual machine discovered by the configuration server is shown in the Azure portal. Per risolvere questo problema, verificare che uno stesso UUID di istanza non sia assegnato a due macchine virtuali. Questo scenario è comunemente visibile nei casi in cui una macchina virtuale di backup diventa attiva e viene registrata nei record di individuazione. Fare riferimento a [Azure Site Recovery VMware-to-Azure: Come pulire le voci duplicate o non aggiornate](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) da risolvere.
* **Credenziali utente vCenter non corrette**: Assicurarsi di aver aggiunto le credenziali vCenter corrette quando si configura il server di configurazione utilizzando il modello OVF o l'installazione unificata. Per verificare le credenziali aggiunte durante la configurazione, consultare [Modificare le credenziali per l'individuazione automatica](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **privilegi insufficienti per vCenter:** se le autorizzazioni fornite per accedere a vCenter non dispongono delle autorizzazioni necessarie, potrebbe verificarsi un errore di individuazione delle macchine virtuali. Assicurarsi che le autorizzazioni descritte in [Preparare un account per l'individuazione automatica](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) vengano aggiunte all'account utente di vCenter.
* Server di **gestione di Azure Site Recovery:** se la macchina virtuale viene usata come server di gestione in uno o più dei ruoli seguenti: Server di configurazione /server di processo di scalabilità orizzontale/Server di destinazione master, non sarà possibile scegliere la macchina virtuale dal portale. I server di gestione non possono essere replicati.
* **Già protetto o sottoposto**a failover tramite i servizi di Azure Site Recovery: se la macchina virtuale è già protetta o sottoposta a failover tramite Site Recovery, la macchina virtuale non è disponibile per la selezione per la protezione nel portale. Assicurarsi che la macchina virtuale che si sta cercando nel portale non sia già protetta da un altro utente o con altre sottoscrizioni.
* **vCenter non connesso**: Controlla se vCenter è connesso. Per verificare, passare all'insieme di credenziali di Servizi di ripristino > infrastruttura di Site Recovery > Server di configurazione > fare clic sul server di configurazione corrispondente > viene visualizzato un pannello a destra con i dettagli dei server associati. Controllare se vCenter è connesso. Se si tratta in uno stato "Non connesso", risolvere il problema e quindi [aggiornare il server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) di configurazione nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **ESXi spento:** se l'host ESXi in cui si trova la macchina virtuale è spento, la macchina virtuale non verrà elencata o non sarà selezionabile nel portale di Azure. Accendere l'host ESXi e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server) nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **Riavvio in sospeso:** se è presente un riavvio in sospeso nella macchina virtuale, non sarà possibile selezionare il computer nel portale di Azure.Pending reboot : If there is a pending reboot on the virtual machine, then you will not be able to select the machine on Azure portal. Assicurarsi di completare le attività di riavvio in sospeso e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.
* **IP non trovato:** se alla macchina virtuale non è associato un indirizzo IP valido, non sarà possibile selezionare il computer nel portale di Azure.IP not found : If the virtual machine doesn't have a valid IP address associated with it, then you will not be able to select the machine on Azure portal. Assicurarsi di assegnare un indirizzo IP valido alla macchina virtuale e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Risolvere i problemi relativi alle macchine virtuali protette in grigio nel portaleTroubleshoot protected virtual machines greyed out in the portal

Le macchine virtuali replicate in Site Recovery non sono disponibili nel portale di Azure se nel sistema sono presenti voci duplicate. Per informazioni su come eliminare le voci non aggiornate e risolvere il problema, vedere [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or non tale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Nessun punto di ripristino coerente con l'arresto anomalo del sistema disponibile per la macchina virtuale negli ultimi minuti 'XXX'

Alcuni dei problemi più comuni sono elencati di seguito

### <a name="initial-replication-issues-error-78169"></a>Problemi di replica iniziale [errore 78169]

Oltre a quanto sopra, assicurati che non ci siano problemi correlati alla connettività, alla larghezza di banda o alla sincronizzazione del tempo, assicurati che:

- Nessun software antivirus blocca Azure Site Recovery. Altre informazioni sulle esclusioni di cartelle necessarie per Azure Site Recovery.Learn [more](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) on folder exclusions required for Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Macchine di origine con varianza elevata [errore 78188]

Possibili cause:
- La frequenza di modifica dei dati (byte scritti/sec) nei dischi elencati della macchina virtuale è maggiore dei limiti supportati da [Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) per il tipo di account di archiviazione di destinazione della replica.
- C'è un improvviso picco nella frequenza di varianza a causa del quale è in sospeso un'elevata quantità di dati per il caricamento.

Per risolvere il problema:
- Verificare che venga eseguito il provisioning del tipo di account di archiviazione di destinazione (Standard o Premium) in base al requisito della frequenza di varianza all'origine.
- Se si esegue già la replica in un disco gestito Premium (tipo asrseeddisk), assicurarsi che la dimensione del disco supporti la frequenza di varianza osservata in base ai limiti di Site Recovery. Se necessario, è possibile aumentare le dimensioni dello spazio asrseed. Seguire questa procedura:
    - Passare al pannello Dischi del computer replicato interessato e copiare il nome del disco di replica
    - Passare a questo disco gestito di replicaNavigate to this replica managed disk
    - È possibile che nel pannello Panoramica venga visualizzato un banner che indica che è stato generato un URL di accesso condiviso. Clicca su questo banner e annulla l'esportazione. Ignorare questo passaggio se il banner non viene visualizzato.
    - Non appena l'URL di accesso condiviso viene revocato, passare al pannello Configurazione del disco gestito e aumentare le dimensioni in modo che ASR supporti la frequenza di varianza osservata sul disco di origine
- Se la varianza osservata è temporanea, attendere alcune ore affinché il caricamento dei dati in sospeso venga aggiornato e crei punti di ripristino.
- Se il disco contiene dati non critici come log temporanei, dati di test e così via, è consigliabile spostare questi dati altrove o escludere completamente il disco dalla replica
- Se il problema persiste, utilizzare Site Recovery [Deployment Planner](site-recovery-deployment-planner.md#overview) per pianificare la replica.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Macchine di origine senza heartbeat [errore 78174]

Ciò si verifica quando l'agente di Azure Site Recovery per la mobilità nel computer di origine non comunica con il server di configurazione (CS).

Per risolvere il problema, utilizzare la procedura seguente per verificare la connettività di rete dalla macchina virtuale di origine al server di configurazione:

1. Verificare che il computer di origine sia in esecuzione.
2. Accedere al computer di origine utilizzando un account con privilegi di amministratore.
3. Verificare che i servizi seguenti siano in esecuzione e, se non riavviarli:
   - Svagents (InMage Scout VX Agent)
   - Servizio dell'applicazione InMage Scout
4. Nel computer di origine esaminare i registri nel percorso per i dettagli dell'errore:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Server di elaborazione senza heartbeat [errore 806]
Nel caso in cui non vi sia alcun heartbeat dal server di elaborazione (PS), verificare che:
1. PS VM è in funzione
2. Controllare i seguenti registri su PS per i dettagli dell'errore:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Server di destinazione master senza heartbeat [errore 78022]

Ciò si verifica quando l'agente di protezione mobili di Azure Site Recovery nella destinazione master non comunica con il server di configurazione.

Per risolvere il problema, attenersi alla seguente procedura per verificare lo stato del servizio:

1. Verificare che la macchina virtuale di destinazione del server master sia in esecuzione.
2. Accedere alla macchina virtuale di destinazione del server master usando un account con privilegi di amministratore.
    - Verificare che il servizio svagent sia in esecuzione. Se è in esecuzione, riavviare il servizio
    - Controllare i registri nel percorso per i dettagli dell'errore:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. Per registrare la destinazione master con il server di configurazione, passare alla cartella **%PROGRAMDATA%**
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID errore 78144 - nessun punto di ripristino coerente dell'app disponibile per la macchina virtuale negli ultimi minuti 'XXX'

Sono stati apportati miglioramenti nelle versioni [9.23](vmware-physical-mobility-service-overview.md#from-923-version-onwards) & [9.27](site-recovery-whats-new.md#update-rollup-39) dell'agente per dispositivi mobili per gestire i comportamenti di errore dell'installazione VSS. Assicurarsi di utilizzare le versioni più recenti per indicazioni consigliate per la risoluzione dei problemi di VSS.

Alcuni dei problemi più comuni sono elencati di seguito

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: problema noto in SQL Server 2008/2008 R2 
**Come risolvere:** C'è un problema noto con SQL Server 2008/2008 R2. Fare riferimento a questo articolo della Knowledge Base [Azure Site Recovery Agent o altro backup VSS non componente ha esito negativo per un server che ospita SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: I processi di Azure Site Recovery hanno esito negativo nei server che ospitano qualsiasi versione di istanze di SQL Server con database di AUTO_CLOSE 
**Come risolvere** : Fare riferimento [articolo](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) Kb 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: Problema noto in SQL Server 2016 e 2017
**Come risolvere** : Fare riferimento [articolo](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) Kb 


### <a name="more-causes-due-to-vss-related-issues"></a>Altre cause a causa di problemi correlati a VSS:

Per risolvere ulteriormente, controllare i file nel computer di origine per ottenere il codice di errore esatto per l'errore:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Come individuare gli errori nel file?
Cercare la stringa "vacpError" aprendo il file vacp.log in un editor
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Nell'esempio precedente **2147754994** è il codice di errore che indica l'errore, come illustrato di seguito

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Il writer VSS non è installato - Errore 2147221164 

*Come risolvere:* per generare tag di coerenza dell'applicazione, Azure Site Recovery usa il servizio Copia Shadow del volume Microsoft (VSS). Installa un provider VSS per il suo funzionamento per acquisire snapshot di coerenza dell'app. Questo provider VSS viene installato come servizio. Nel caso in cui il servizio provider VSS non è installato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo con l'ID di errore 0x80040154 "Classe non registrata". </br>
Fare riferimento [all'articolo per](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) la risoluzione dei problemi di installazione di VSS Writer 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Il writer VSS è disabilitato - Errore 2147943458

**Come risolvere:** per generare tag di coerenza dell'applicazione, Azure Site Recovery usa il servizio Copia Shadow del volume Microsoft (VSS). Installa un provider VSS per il suo funzionamento per acquisire snapshot di coerenza dell'app. Questo provider VSS viene installato come servizio. Nel caso in cui il servizio provider VSS è disabilitato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo con l'ID errore "il servizio specificato è disabilitato e non può essere avviato(0x80070422)". </br>

- Se VSS è disabilitato,
    - Verificare che il tipo di avvio del servizio provider VSS sia impostato su **Automatico**.
    - Riavviare i seguenti servizi:
        - Servizio VSS
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>NOT_REGISTERED PROVIDER VSS - Errore 2147754756

**Come risolvere:** per generare tag di coerenza dell'applicazione, Azure Site Recovery usa il servizio Copia Shadow del volume Microsoft (VSS). Verificare se il servizio provider VSS di Azure Site Recovery è installato o meno. </br>

- Ripetere l'installazione del provider utilizzando i seguenti comandi:
- Disinstallare il provider esistente: C: (x86) Programmi (x86) Microsoft Azure Site Recovery agent InMageVSSProvider_Uninstall.cmd
- Reinstallazione: C: (x86) file di programma (x86) Microsoft Azure Site Recovery agent InMageVSSProvider_Install.cmd
 
Verificare che il tipo di avvio del servizio provider VSS sia impostato su **Automatico**.
    - Riavviare i seguenti servizi:
        - Servizio VSS
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). La community è molto attiva e uno dei tecnici Microsoft potrà fornire l'assistenza richiesta.
