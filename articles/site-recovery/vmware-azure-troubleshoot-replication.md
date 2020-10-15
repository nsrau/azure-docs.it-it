---
title: Risolvere i problemi di replica per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di replica comunemente riscontrati durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 8b44a1d6119cc658b9460e0a52fa0629f759964a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336206"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Risolvere i problemi di replica per macchine virtuali VMware e server fisici

Questo articolo descrive alcuni problemi comuni ed errori specifici che possono verificarsi durante l'esecuzione della replica di macchine virtuali VMware e server fisici locali in Azure usando [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Passaggio 1: Monitorare l'integrità del server di elaborazione

Site Recovery usa il [server di elaborazione](vmware-physical-azure-config-process-server-overview.md#process-server) per ricevere e ottimizzare i dati replicati e per inviarli ad Azure.

Si consiglia di monitorare l'integrità dei server di elaborazione nel portale per assicurarsi che siano connessi e funzionino correttamente e che sia in corso la replica per i computer di origine associati al server di elaborazione.

- [Informazioni sul](vmware-physical-azure-monitor-process-server.md) monitoraggio dei server di elaborazione.
- [Esaminare le procedure consigliate](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Risolvere i problemi](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) di integrità dei server di elaborazione.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passaggio 2: Risolvere i problemi di connettività e di replica

Spesso gli errori di replica iniziale e in corso sono causati da problemi di connettività tra il server di origine e il server di elaborazione oppure tra il server di elaborazione e Azure.

Per risolvere questi problemi, vedere come [risolvere i problemi di connettività e di replica](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passaggio 3: Risolvere i problemi dei computer di origine non disponibili per la replica

Quando si tenta di scegliere la macchina di origine per cui abilitare la replica tramite Site Recovery, è possibile che la macchina non sia disponibile per uno dei motivi seguenti:

* **Due macchine virtuali con lo stesso UUID di istanza**: Se In vCenter due macchine virtuali hanno lo stesso UUID di istanza, nel portale di Azure viene visualizzata solo la prima macchina virtuale individuata dal server di configurazione. Per risolvere questo problema, verificare che uno stesso UUID di istanza non sia assegnato a due macchine virtuali. Questo scenario è comune nelle istanze in cui una macchina virtuale di backup diventa attiva e viene registrata nei record di individuazione. Per la risoluzione, vedere [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) (Da VMware ad Azure con Azure Site Recovery: come eseguire la pulizia delle voci duplicate o non aggiornate).
* **Credenziali utente di vCenter non corrette**: Assicurarsi di aver aggiunto le credenziali di vCenter corrette durante la configurazione del server di configurazione usando il modello OVF o l'installazione unificata. Per verificare le credenziali aggiunte durante la configurazione, consultare [Modificare le credenziali per l'individuazione automatica](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Privilegi insufficienti per vCenter**: Se le autorizzazioni fornite per accedere a vCenter non dispongono dei privilegi necessari, potrebbero verificarsi errori nell'individuazione delle macchine virtuali. Assicurarsi che le autorizzazioni descritte in [Preparare un account per l'individuazione automatica](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) vengano aggiunte all'account utente di vCenter.
* **Server di gestione di Azure Site Recovery**: se la macchina virtuale viene usata come server di gestione in uno o più dei ruoli server di configurazione, server di elaborazione scale-out o server di destinazione master, non sarà possibile scegliere la macchina virtuale dal portale. I server di gestione non possono essere replicati.
* **Già protetta o sottoposta a failover tramite i servizi di Azure Site Recovery**: se la macchina virtuale è già protetta oppure ne è già stato eseguito il failover tramite Site Recovery, non risulterà disponibile per la protezione nel portale. Assicurarsi che la macchina virtuale che si sta cercando nel portale non sia già protetta da un altro utente o con altre sottoscrizioni.
* **vCenter non connesso**: controllare se vCenter è in stato connesso. Per verificare, passare all'insieme di credenziali di Servizi di ripristino > infrastruttura di Site Recovery > Server di configurazione > fare clic sul server di configurazione corrispondente > viene visualizzato un pannello a destra con i dettagli dei server associati. Controllare se vCenter è connesso. Se è in stato "Non connesso", risolvere il problema e quindi [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server) nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **ESXi spento**: Se l'host ESXi in cui risiede la macchina virtuale è spento, la macchina virtuale non verrà elencata o non sarà selezionabile nel portale di Azure. Accendere l'host ESXi e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server) nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **In attesa di riavvio**: se è presente un riavvio in sospeso nella macchina virtuale, non sarà possibile selezionare la macchina nel portale di Azure. Assicurarsi di completare le attività di riavvio in sospeso e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.
* **IP non trovato**: se alla macchina virtuale non è associato un indirizzo IP valido, non sarà possibile selezionare la macchina nel portale di Azure. Assicurarsi di assegnare un indirizzo IP valido alla macchina virtuale e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Risolvere i problemi relativi a macchine virtuali protette che risultano disattivate nel portale

Le macchine virtuali replicate in Site Recovery non sono disponibili nel portale di Azure se nel sistema sono presenti voci duplicate. Per informazioni su come eliminare le voci non aggiornate e risolvere il problema, vedere [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) (Da VMware ad Azure con Azure Site Recovery: come eseguire la pulizia delle voci duplicate o non aggiornate).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Nessun punto di recupero coerente con l'arresto anomalo disponibile per la macchina virtuale negli ultimi "XXX" minuti

Di seguito sono elencati alcuni dei problemi più comuni

### <a name="initial-replication-issues-error-78169"></a>Problemi di replica iniziale [errore 78169]

Oltre ad assicurarsi che non siano presenti problemi di connettività, larghezza di banda o sincronizzazione dell'ora, accertarsi che:

- Nessun software antivirus stia bloccando Azure Site Recovery. [Maggiori](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) informazioni sull'esclusione di cartelle necessaria per Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Computer di origine con varianza elevata [errore 78188]

Possibili cause:
- La frequenza di modifica dei dati (byte scrittura/sec) nei dischi elencati della macchina virtuale è superiore ai [limiti supportati da Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) per il tipo di account di archiviazione di destinazione di replica.
- Si è verificato un picco improvviso nella frequenza di varianza a seguito del quale una quantità elevata di dati è in attesa di caricamento.

Per risolvere il problema:
- Verificare che sia stato effettuato il provisioning del tipo di account di archiviazione di destinazione (Standard o Premium) in base al requisito della frequenza di varianza all'origine.
- Se è già in corso la replica in un disco gestito Premium (tipo asrseeddisk), assicurarsi che le dimensioni del disco supportino la varianza osservata in base ai limiti di Site Recovery. Se necessario, è possibile aumentare la dimensione del disco asrseeddisk. Seguire questa procedura:
    - Passare al pannello Dischi del computer replicato interessato e copiare il nome del disco di replica
    - Passare a questo disco gestito di replica
    - Nel pannello Panoramica è visibile un banner che informa che è stato generato un URL di firma di accesso condiviso. Fare clic su questo banner e annullare l'esportazione. Ignorare questo passaggio se il banner non viene visualizzato.
    - Non appena viene revocato l'URL di firma di accesso condiviso, passare al pannello configurazione del disco gestito e aumentare le dimensioni in modo che Azure Site Recovery supporti la varianza osservata sul disco di origine
- Se la varianza osservata è temporanea, attendere alcune ore prima che il caricamento dei dati in sospeso si aggiorni e crei punti di ripristino.
- Se il disco contiene dati non critici come log temporanei, dati di test e così via, provare a trasferire questi dati altrove o a escludere completamente il disco dalla replica
- Se il problema persiste, usare il [piano di distribuzione](site-recovery-deployment-planner.md#overview) di Site Recovery per pianificare la replica.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Nessun heartbeat del computer di origine [errore 78174]

Questo problema si verifica quando l'agente di mobilità di Azure Site Recovery nel computer di origine non comunica con il server di configurazione.

Per risolvere il problema, attenersi alla procedura seguente per verificare la connettività di rete dalla macchina virtuale di origine al server di configurazione:

1. Verificare che il computer di origine sia in esecuzione.
2. Accedere al computer di origine tramite un account con privilegi di amministratore.
3. Verificare che i seguenti servizi siano in esecuzione e, in caso contrario, riavviarli:
   - Svagents (InMage Scout VX Agent)
   - Servizio dell'applicazione InMage Scout
4. Nel computer di origine, esaminare i log nel percorso per rilevare i dettagli dell'errore:

    *C:\Programmi (x86) \Microsoft Azure site Recovery\agent\svagents \* . log*

### <a name="process-server-with-no-heartbeat-error-806"></a>Nessun heartbeat del server di elaborazione [errore 806]
Se non è presente alcun heartbeat dal server di elaborazione, verificare che:
1. La macchina virtuale del server di elaborazione sia attiva e in esecuzione
2. Controllare i log seguenti nel server di elaborazione per informazioni sull'errore:

    *C:\ProgramData\ASR\home\svsystems\eventmanager \* . log*\
    e
    *C:\ProgramData\ASR\home\svsystems\ monitor_protection \* . log*

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Nessun heartbeat del server di destinazione master [errore 78022]

Questo problema si verifica quando l'agente di mobilità di Azure Site Recovery nel server di destinazione master non comunica con il server di configurazione.

Per risolvere il problema, attenersi alla procedura seguente per verificare lo stato del servizio:

1. Verificare che la macchina virtuale di destinazione master sia in esecuzione.
2. Accedere alla macchina virtuale di destinazione master tramite un account con privilegi di amministratore.
    - Verificare che il servizio svagents sia in esecuzione. In tal caso, riavviarlo
    - Controllare i log nel percorso per i dettagli dell'errore:

        *C:\Programmi (x86) \Microsoft Azure site Recovery\agent\svagents \* . log*
3. Per registrare la destinazione master con il server di configurazione, passare alla cartella **%PROGRAMDATA%\ASR\Agent** ed eseguire il comando seguente sul prompt dei comandi:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>IR errore 78144 - Nessun punto di recupero coerente con l'app disponibile per la macchina virtuale negli ultimi "XXX" minuti

Sono stati apportati miglioramenti all'agente di mobilità versione [9.23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher) & [9.27](site-recovery-whats-new.md#update-rollup-39) per gestire i comportamenti degli errori di installazione del servizio Copia Shadow del volume. Assicurarsi di disporre delle versioni più recenti per ottenere il materiale sussidiario più pertinente relativo alla risoluzione degli errori del servizio Copia Shadow del volume.

Di seguito sono elencati alcuni dei problemi più comuni

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: Problema noto in SQL server 2008/2008 R2
**Come correggere**: Si verifica un problema noto con SQL Server 2008/2008 R2. Per i server che ospitano SQL Server 2008 R2, fare riferimento all'articolo della Knowledge Base [Backup dell'agente di Azure Site Recovery o di altri elementi esterni ai componenti del servizio Copia Shadow del volume](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: I processi di Azure Site Recovery hanno esito negativo nei server che ospitano qualsiasi versione di istanze di SQL Server con database AUTO_CLOSE
**Come correggere**: Fare riferimento a questo [articolo della Knowledge Base](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: Problema noto in SQL Server 2016 e 2017
**Come correggere**: Fare riferimento a questo [articolo della Knowledge Base](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)

#### <a name="cause-4-app-consistency-not-enabled-on-linux-servers"></a>Motivo 4: non App-Consistency abilitata nei server Linux
**Come risolvere il** problema: Azure Site Recovery per il sistema operativo Linux supporta gli script personalizzati dell'applicazione per la coerenza delle app. Lo script personalizzato con le opzioni pre e post verrà usato dall'agente di Azure Site Recovery Mobility per la coerenza delle app. [Ecco](./site-recovery-faq.md#replication) i passaggi per abilitarla.

### <a name="more-causes-due-to-vss-related-issues"></a>Altre cause derivanti da problemi correlati al servizio Copia Shadow del volume:

Per risolvere il problema, controllare i file nella macchina di origine per ottenere il codice di errore esatto per l'errore:

*C:\Programmi (x86) \Microsoft Azure site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log*

Individuare gli errori nel file
Cercare la stringa "vacpError" aprendo il file vacp.log in un editor

`Ex: `**`vacpError`**`:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|`

Nell'esempio precedente, **2147754994** è il codice di errore che indica l'errore, come illustrato di seguito

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS writer non è installato - Errore 2147221164

*Come correggere*: Per generare un tag di coerenza dell'applicazione, Azure Site Recovery usa il servizio Copia Shadow del volume Microsoft (servizio Copia Shadow del volume), che installa un provider del servizio Copia Shadow del volume per la relativa operazione per eseguire snapshot di coerenza dell'app. Questo provider del servizio Copia Shadow del volume viene installato as-a-service. Se il servizio provider VSS non è installato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo con ID errore 0x80040154 "classe non registrata". </br>
Vedere l'articolo relativo alla [risoluzione dei problemi di installazione di VSS writer](./vmware-azure-troubleshoot-push-install.md#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS writer è disabilitato - Errore 2147943458

**Come correggere**: Per generare un tag di coerenza dell'applicazione, Azure Site Recovery usa il servizio Copia Shadow del volume Microsoft (servizio Copia Shadow del volume), che installa un provider del servizio Copia Shadow del volume per la relativa operazione per eseguire snapshot di coerenza dell'app. Questo provider del servizio Copia Shadow del volume viene installato as-a-service. Se il servizio del provider VSS è disabilitato, la creazione dello snapshot di coerenza dell'applicazione ha esito negativo con ID errore "il servizio specificato è disabilitato e non può essere avviato (0x80070422)". </br>

- Se il servizio Copia Shadow del volume è disabilitato,
    - Verificare che il tipo di avvio del servizio provider del servizio Copia Shadow del volume sia impostato su **Automatico**.
    - Riavviare i seguenti servizi:
        - Servizio Copia Shadow del volume
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>PROVIDER DEL SERVIZIO COPIA SHADOW DEL VOLUME NOT_REGISTERED - Errore 2147754756

**Come correggere**: Per generare un tag di coerenza dell'applicazione, Azure Site Recovery usa il servizio Copia Shadow del volume Microsoft (servizio Copia Shadow del volume),
Controllare se il provider del servizio Copia Shadow del volume di Azure Site Recovery VSS è installato o meno. </br>

- Installare nuovamente il provider tramite i comandi seguenti:
- Disinstallare il provider esistente: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Reinstallare: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Verificare che il tipo di avvio del servizio provider del servizio Copia Shadow del volume sia impostato su **Automatico**.
    - Riavviare i seguenti servizi:
        - Servizio Copia Shadow del volume
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

## <a name="error-id-95001---insufficient-permissions-found"></a>ID errore 95001-sono state trovate autorizzazioni insufficienti

Questo errore si verifica quando si tenta di abilitare la replica e le cartelle dell'applicazione non dispongono di autorizzazioni sufficienti.

**Come correggere**: per risolvere questo problema, assicurarsi che l'utente IUSR abbia il ruolo di proprietario per tutte le cartelle indicate di seguito.

- *C\ProgramData\Microsoft sito di Azure Recovery\private*
- Directory di installazione. Se ad esempio la directory di installazione è unità F, fornire le autorizzazioni corrette per-
    - *File F:\Program (x86) \Microsoft Azure site Recovery\home\svsystems*
- La cartella *\pushinstallsvc* nella directory di installazione. Se ad esempio la directory di installazione è unità F, fornire le autorizzazioni corrette per-
    - *File F:\Program (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc*
- La cartella *etc* nella directory di installazione. Se ad esempio la directory di installazione è unità F, fornire le autorizzazioni corrette per-
    - *File F:\Program (x86) \Microsoft Azure site Recovery\home\svsystems\etc*
- *C:\Temp*
- *C:\thirdparty\php5nts*
- Tutti gli elementi nel percorso seguente:
    - *C:\thirdparty\rrdtool-1.2.15-win32-perl58\rrdtool\Release\**

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza, pubblicare una domanda sulla [pagina delle domande di Domande e risposte per Azure Site Recovery](/answers/topics/azure-site-recovery.html). La community è molto attiva e uno dei tecnici Microsoft potrà fornire l'assistenza richiesta.
