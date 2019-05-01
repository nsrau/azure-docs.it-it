---
title: Risolvere i problemi di replica per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di replica comunemente riscontrati durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 3221b2bd18b8b0756f280d88fffc6016d0498b8f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924816"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Risolvere i problemi di replica per macchine virtuali VMware e server fisici

Questo articolo descrive alcuni problemi comuni e specifici errori che possono verificarsi quando si replicano server fisici e macchine virtuali VMware locali in Azure usando [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Passaggio 1: Integrità server di monitoraggio processo

Usa il ripristino del sito di [server di elaborazione](vmware-physical-azure-config-process-server-overview.md#process-server) per ricevere e ottimizzare i dati replicati e inviarli ad Azure.

È consigliabile monitorare l'integrità del server di elaborazione nel portale, per assicurarsi che sono connessi e che funzioni correttamente e che la replica sta procedendo per le macchine di origine associate con il server di elaborazione.

- [Informazioni su](vmware-physical-azure-monitor-process-server.md) Monitoraggio server di elaborazione.
- [Esaminare le procedure consigliate](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Risolvere i problemi di](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) elaborare lo stato del server.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passaggio 2: Risolvere i problemi di connettività e della replica

Gli errori di replica iniziale e continuativa sono spesso causati da problemi di connettività tra il server di origine e il server di elaborazione oppure tra il server di elaborazione e Azure. 

Per risolvere questi problemi [risolvere i problemi di connettività e della replica](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passaggio 3: Risolvere i problemi delle macchine di origine che non sono disponibili per la replica

Quando si tenta di scegliere la macchina di origine per cui abilitare la replica tramite Site Recovery, è possibile che la macchina non sia disponibile per uno dei motivi seguenti:

* **Due macchine virtuali con lo stesso UUID di istanza**: Se In vCenter due macchine virtuali hanno lo stesso UUID di istanza, nel portale di Azure viene visualizzata solo la prima macchina virtuale individuata dal server di configurazione. Per risolvere questo problema, verificare che uno stesso UUID di istanza non sia assegnato a due macchine virtuali. Questo scenario è presente comunemente nelle istanze in cui una backup della macchina virtuale diventa attiva e viene registrata nel nostro record di individuazione. Per la risoluzione, vedere [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) (Da VMware ad Azure con Azure Site Recovery: come eseguire la pulizia delle voci duplicate o non aggiornate).
* **Credenziali utente di vCenter non corrette**: Assicurarsi di aver aggiunto le credenziali di vCenter corrette durante la configurazione del server di configurazione usando il modello OVF o l'installazione unificata. Per verificare le credenziali aggiunte durante la configurazione, consultare [Modificare le credenziali per l'individuazione automatica](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Privilegi insufficienti per vCenter**: Se le autorizzazioni fornite per accedere a vCenter non dispongono dei privilegi necessari, potrebbero verificarsi errori nell'individuazione delle macchine virtuali. Assicurarsi che le autorizzazioni descritte in [Preparare un account per l'individuazione automatica](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) vengano aggiunte all'account utente di vCenter.
* **Server di gestione di Azure Site Recovery**: se la macchina virtuale viene usata come server di gestione in uno o più dei ruoli server di configurazione, server di elaborazione scale-out o server di destinazione master, non sarà possibile scegliere la macchina virtuale dal portale. I server di gestione non possono essere replicati.
* **Già protetta o sottoposta a failover tramite i servizi di Azure Site Recovery**: se la macchina virtuale è già protetta oppure ne è già stato eseguito il failover tramite Site Recovery, non risulterà disponibile per la protezione nel portale. Assicurarsi che la macchina virtuale che si sta cercando nel portale non sia già protetta da un altro utente o con altre sottoscrizioni.
* **vCenter non connesso**: controllare se vCenter è in stato connesso. Per verificare, passare all'insieme di credenziali di Servizi di ripristino > infrastruttura di Site Recovery > Server di configurazione > fare clic sul server di configurazione corrispondente > viene visualizzato un pannello a destra con i dettagli dei server associati. Controllare se vCenter è connesso. Se è in uno stato "Non connesso", risolvere il problema e quindi [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server) nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **ESXi spento**: Se l'host ESXi in cui risiede la macchina virtuale è spento, la macchina virtuale non verrà elencata o non sarà selezionabile nel portale di Azure. Accendere l'host ESXi e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server) nel portale. Al termine, la macchina virtuale verrà elencata nel portale.
* **In attesa di riavvio**: se è presente un riavvio in sospeso nella macchina virtuale, non sarà possibile selezionare la macchina nel portale di Azure. Assicurarsi di completare le attività di riavvio in sospeso e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.
* **IP non trovato**: se alla macchina virtuale non è associato un indirizzo IP valido, non sarà possibile selezionare la macchina nel portale di Azure. Assicurarsi di assegnare un indirizzo IP valido alla macchina virtuale e [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Al termine, la macchina virtuale verrà elencata nel portale.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Risolvere i problemi delle macchine virtuali protette in grigio nel portale

Le macchine virtuali replicate in Site Recovery non sono disponibili nel portale di Azure se nel sistema sono presenti voci duplicate. Per informazioni su come eliminare le voci non aggiornate e risolvere il problema, vedere [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) (Da VMware ad Azure con Azure Site Recovery: come eseguire la pulizia delle voci duplicate o non aggiornate).

## <a name="common-errors-and-solutions"></a>Errori comuni e soluzioni

### <a name="initial-replication-issues-error-78169"></a>Problemi della replica iniziale [errore 78169]

Su una precedente verifica che esistono alcuna connettività, larghezza di banda o l'ora di sincronizzazione sui problemi correlati, assicurarsi che:

- Nessun software antivirus sta bloccando Azure Site Recovery. Scopri [altre](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) su esclusioni di cartelle necessarie per Azure Site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Punti di ripristino coerenti con l'app mancanti [errore 78144]

 Ciò si verifica a causa di problemi con la copia Shadow del Volume (VSS) del servizio. Per risolvere il problema: 
 
- Verificare che la versione installata dell'agente di Azure Site Recovery sia almeno 9.22.2. 
- Verificare che il Provider VSS sia installato come servizio in servizi di Windows e verificare anche la console MMC Servizi componenti per verificare che sia elencato il Provider VSS di Azure Site Recovery.
- Se non è installato il Provider VSS, vedere la [errore di installazione di risoluzione dei problemi di articolo](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Se è disabilitato VSS,
    - Verificare che il tipo di avvio del servizio Provider servizio Copia shadow è impostato su **automatica**.
    - Riavviare i servizi seguenti:
        - Servizio VSS
        - Provider VSS di Azure Site Recovery
        - Servizio VDS

### <a name="source-machines-with-high-churn-error-78188"></a>Macchine di origine con varianza elevata [errore 78188]

Possibili cause:
- La frequenza di modifica (byte scrittura/sec) di dati nei dischi elencati della macchina virtuale è superiore al [limiti supportati di Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) per il tipo di account archiviazione di destinazione di replica.
- Si verifica un picco improvviso della frequenza di varianza a causa di cui una quantità elevata di dati è in sospeso per il caricamento.

Per risolvere il problema:
- Assicurarsi che il tipo di account archiviazione di destinazione (Standard o Premium) è stato effettuato il provisioning in base al requisito di frequenza di varianza nell'origine.
- Se la varianza osservata è temporanea, attendere alcune ore per il caricamento dei dati in sospeso possono essere aggiornati e per creare i punti di ripristino.
- Se il problema persiste, usare il ripristino del sito [deployment Planner distribuzioni](site-recovery-deployment-planner.md#overview) per facilitare la pianificazione della replica.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Macchine di origine con nessun heartbeat [errore 78174]

Ciò si verifica quando l'agente di Azure Site Recovery Mobility nella macchina di origine non comunica con il Server di configurazione (CS).

Per risolvere il problema, usare la procedura seguente per verificare la connettività di rete dalla macchina virtuale di origine al Server di configurazione:

1. Verificare che la macchina di origine sia in esecuzione.
2. Accedere al computer di origine usando un account con privilegi di amministratore.
3. Verificare che i servizi seguenti siano in esecuzione e, se non riavviare i servizi:
   - Svagents (InMage Scout VX Agent)
   - Servizio dell'applicazione InMage Scout
4. Nella macchina di origine, esaminare i log in corrispondenza della posizione per i dettagli dell'errore:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Server di elaborazione con nessun heartbeat [errore 806]
Nel caso in cui non si verifica nessun heartbeat dal Server di elaborazione, verificare quanto segue:
1. PS VM sia in esecuzione
2. Controllare i log di seguito nel PS per i dettagli dell'errore:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Server di destinazione master con nessun heartbeat [errore 78022]

Ciò si verifica quando l'agente di Azure Site Recovery Mobility nel server di destinazione Master non comunica con il Server di configurazione.

Per risolvere il problema, usare la procedura seguente per verificare lo stato del servizio:

1. Verificare che la VM di destinazione Master sia in esecuzione.
2. Accedere alla VM di destinazione Master usando un account con privilegi di amministratore.
    - Verificare che il servizio svagents sia in esecuzione. Se è in esecuzione, riavviare il servizio
    - Controllare i log in corrispondenza della posizione per i dettagli dell'errore:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). La community è molto attiva e uno dei tecnici Microsoft potrà fornire l'assistenza richiesta.
