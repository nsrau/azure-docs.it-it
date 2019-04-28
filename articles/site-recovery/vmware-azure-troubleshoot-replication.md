---
title: Risolvere i problemi di replica per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di replica comunemente riscontrati durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 1aaf13f01c7e7197001f3099fabd4b8be8545f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565063"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Risolvere i problemi di replica per macchine virtuali VMware e server fisici

Potrebbe essere visualizzato un messaggio di errore specifico durante la protezione delle macchine virtuali VMware o dei server fisici con Azure Site Recovery. Questo articolo descrive alcuni problemi comuni che possono verificarsi durante l'esecuzione della replica di macchine virtuali VMware e server fisici locali in Azure usando [Site Recovery](site-recovery-overview.md).

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>Monitorare l'integrità del server di elaborazione per evitare problemi di replica

È consigliabile monitorare l'integrità del server di elaborazione (PS) nel portale per assicurarsi che la replica proceda normalmente per i computer di origine associati. Nell'insieme di credenziali passare a Gestisci > Infrastruttura di Site Recovery > Server di configurazione. Nel pannello Server di configurazione fare clic sul server di elaborazione in Server associati. Il pannello del server di elaborazione si apre con le statistiche di integrità. È possibile monitorare utilizzo della CPU, utilizzo della memoria, stato dei servizi del server di elaborazione richiesti per la replica, data di scadenza del certificato e spazio libero disponibile. Lo stato di tutte le statistiche dovrebbe essere verde. 

**È consigliabile avere un utilizzo di memoria e CPU inferiore al 70% e spazio libero superiore al 25%**. Per spazio libero si intende lo spazio nel disco della cache nel server di elaborazione, che viene usato per archiviare i dati di replica dai computer di origine prima di caricarli in Azure. Se riduce a meno del 20%, la replica verrà limitata per tutti i computer di origine associati. Seguire le [indicazioni sulla capacità](./site-recovery-plan-capacity-vmware.md#capacity-considerations) per informazioni sulla configurazione necessaria per la replica dei computer di origine.

Assicurarsi che i servizi seguenti siano in esecuzione nel computer del server di elaborazione. Avviare o riavviare qualsiasi servizio che non sia in esecuzione.

**Server di elaborazione predefinito**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Servizio di caricamento log (LogUpload)
* Servizio dell'applicazione InMage Scout
* Agente di Servizi di ripristino di Microsoft Azure (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc
* Servizio Pubblicazione sul Web (W3SVC)
* MySQL
* Servizio Microsoft Azure Site Recovery (dra)

**Server di elaborazione con scalabilità orizzontale**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Servizio di caricamento log (LogUpload)
* Servizio dell'applicazione InMage Scout
* Agente di Servizi di ripristino di Microsoft Azure (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc

**Server di elaborazione in Azure per il failback**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Servizio di caricamento log (LogUpload)

Verificare che il tipo di avvio di tutti i servizi sia impostato su **Automatico o Automatico (avvio ritardato)**. Il servizio Servizi di ripristino di Microsoft Azure (obengine) non deve avere il tipo di avvio impostato come indicato in precedenza.

## <a name="replication-issues"></a>Problemi di replica

Gli errori di replica iniziale e continuativa sono spesso causati da problemi di connettività tra il server di origine e il server di elaborazione oppure tra il server di elaborazione e Azure. Nella maggior parte dei casi è possibile risolvere questi problemi completando i passaggi elencati nelle sezioni seguenti.

>[!Note]
>Assicurarsi che:
>1. Il sistema data ora per l'elemento protetto è sincronizzato.
>2. Nessun software antivirus sta bloccando Azure Site Recovery. Scopri [altre](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) su esclusioni di cartelle necessarie per Azure Site Recovery.

### <a name="check-the-source-machine-for-connectivity-issues"></a>Controllare il computer di origine per i problemi di connettività

Elenco Mostra le seguenti è possibile controllare il computer di origine.

*  Nella riga di comando del server di origine usare Telnet per effettuare il ping del server di elaborazione tramite la porta HTTPS eseguendo il comando seguente. La porta HTTPS 9443 è quella predefinita usata dal server di elaborazione per inviare e ricevere il traffico di replica. È possibile modificare questa porta al momento della registrazione. Il comando seguente verifica la presenza di problemi di connettività di rete così come di problemi che blocchino la porta del firewall.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Usare Telnet per testare la connettività. Non usare `ping`. Se Telnet non è installato, completare i passaggi elencati in [Install Telnet Client](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) (Installare il client Telnet).

   Se Telnet è in grado di connettersi correttamente alla porta del server di elaborazione, viene visualizzata una schermata vuota.

   Se non è possibile connettersi al server di elaborazione, consentire la porta in ingresso 9443 nel server di elaborazione. Ad esempio potrebbe essere necessario consentire la porta in ingresso 9443 nel server di elaborazione se la rete dispone di una rete perimetrale (o screened subnet). Quindi verificare se il problema persiste.

*  Se Telnet ha esito positivo e il computer di origine continua a segnalare che il server di elaborazione non è raggiungibile, aprire il Web browser nel computer di origine e verificare se l'indirizzo https://<IP_PS>:<Porta_Dati_PS>/ è raggiungibile.

    Inserendo questo indirizzo è previsto un errore del certificato HTTPS. Ignorando l'errore del certificato e continuando si dovrebbe ricevere un errore 400 - Richiesta non valida, che indica il server non può gestire la richiesta del browser e che la connessione HTTPS standard al server funziona correttamente.

    Se il tentativo non riesce, i dettagli sul messaggio di errore nel browser forniranno indicazioni. Per ad esempio, se l'autenticazione proxy non è corretto, il server proxy restituisce l'errore 407 - Autenticazione proxy obbligatoria e le azioni necessarie nel messaggio di errore. 

*  Controllare i log seguenti nella macchina virtuale di origine per verificare la presenza di errori correlati al caricamento di rete:

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server-for-connectivity-issues"></a>Controllare il server di elaborazione per i problemi di connettività

L'elenco seguente mostra i modi in cui è possibile controllare il server di elaborazione:

> [!NOTE]
> Server di elaborazione deve avere un indirizzo IPv4 statico e su di esso non deve essere configurato l'IP NAT.

* **Controllare la connettività tra i computer di origine e il server di elaborazione**
* Se è possibile connettersi tramite Telnet dal computer di origine e il server di elaborazione continua a non essere raggiungibile dall'origine, controllare la connessione end-to-end con cxprocessserver dalla macchina virtuale di origine eseguendo lo strumento cxpsclient nella macchina virtuale di origine:

      <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

   Controllare i log generati sul server di elaborazione nelle directory seguenti per informazioni dettagliate sugli errori corrispondenti:

      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
      and
      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
* Controllare i log seguenti su PS nel caso in cui non vi è alcun heartbeat dal PS. Ciò è identificato da **il codice di errore da 806** nel portale.

      C:\ProgramData\ASR\home\svsystems\eventmanager*.log
      and
      C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

* **Controllare se il server di elaborazione esegue attivamente il push dei dati in Azure**.

  1. Nel server di elaborazione aprire Gestione attività (premere CTRL+MAIUSC+ESC).
  2. Selezionare la scheda **Prestazioni** e fare clic sul collegamento **Apri Monitoraggio risorse**. 
  3. Nella pagina di **Monitoraggio risorse** selezionare la scheda **Rete**. In **Processi con attività di rete** controllare se **cbengine.exe** sta inviando attivamente volumi di dati di grandi dimensioni.

       ![Screenshot che mostra i volumi in Processi con attività di rete](./media/vmware-azure-troubleshoot-replication/cbengine.png)

  Se cbengine.exe non invia una quantità notevole di dati, completare i passaggi illustrati nelle sezioni seguenti.

* **Verificare se il server di elaborazione riesce a connettersi ad Archiviazione BLOB di Azure**.

  Selezionare **cbengine.exe**. In **Connessioni TCP** verificare se vi è connettività tra il server di elaborazione e l'URL di Archiviazione BLOB di Azure.

  ![Screenshot che mostra la connettività tra cbengine.exe e l'URL di Archiviazione BLOB di Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

  Se non vi è connettività tra il server di elaborazione e l'URL di Archiviazione BLOB di Azure, selezionare **Servizi** in Pannello di controllo. Verificare se sono in esecuzione i servizi seguenti:

  *  cxprocessserver
  *  InMage Scout VX Agent – Sentinel/Outpost
  *  Agente di Servizi di ripristino di Microsoft Azure
  *  Servizio Microsoft Azure Site Recovery
  *  tmansvc

  Avviare o riavviare qualsiasi servizio che non sia in esecuzione. Quindi verificare se il problema persiste.

* **Verificare se il server di elaborazione riesce a connettersi all'indirizzo IP pubblico di Azure usando la porta 443**.

  In %programfiles%\Microsoft Azure Recovery Services Agent\Temp aprire il file CBEngineCurr.errlog più recente. Nel file cercare **443** o la stringa **connection attempt failed**.

  ![Screenshot che mostra il log degli errori nella cartella Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

  Se si visualizzano dei problemi, nella riga di comando del server di elaborazione usare Telnet per effettuare il ping dell'indirizzo IP pubblico di Azure (nell'immagine precedente l'indirizzo IP è mascherato). È possibile trovare l'indirizzo IP pubblico di Azure nel file CBEngineCurr.currLog usando la porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

  In caso non sia possibile connettersi, verificare se il problema di accesso è causato dalle impostazioni del firewall o del proxy come descritto nel passaggio successivo.

* **Controllare che il firewall basato su indirizzo IP nel server di elaborazione non blocchi l'accesso**.

  Se si utilizzano le regole del firewall basato su indirizzo IP nel server, scaricare l'elenco completo degli [intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Aggiungere gli intervalli di indirizzi IP alla configurazione del firewall per assicurarsi che il firewall consenta le comunicazioni con Azure (e con la porta HTTPS predefinita 443). Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (area usata per il controllo di accesso e la gestione delle identità).

* **Controllare se il firewall basato su URL nel server di elaborazione blocca l'accesso**.

  Se si usa una regola del firewall basato su URL nel server, aggiungere gli URL elencati nella tabella seguente per la configurazione del firewall:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Controllare se le impostazioni del proxy nel server di elaborazione bloccano l'accesso**.

   Se si usa un server proxy, assicurarsi che il nome del server proxy venga risolto dal server DNS. Per controllare il valore fornito quando si configura il server di configurazione, passare alla chiave del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.

   Quindi verificare che le stesse impostazioni siano usate dall'agente di Azure Site Recovery per l'invio dei dati: 
      
   1. Cercare **Backup di Microsoft Azure**. 
   2. Aprire **Backup di Microsoft Azure**, quindi selezionare **Azione** > **Modifica proprietà**. 
   3. Nella scheda **Configurazione proxy** si dovrebbe visualizzare l'indirizzo del proxy. L'indirizzo del proxy dovrebbe essere uguale all'indirizzo del proxy che viene visualizzato nelle impostazioni del Registro di sistema. In caso contrario, modificarlo per impostare lo stesso indirizzo.

*  **Controllare se la limitazione della larghezza di banda è vincolata nel server di elaborazione**.

   Aumentare la larghezza di banda e controllare se il problema persiste.


## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>La macchina di origine non è elencata nel portale di Azure

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

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Le macchine virtuali protette risultano disattivate nel portale

Le macchine virtuali replicate in Site Recovery non sono disponibili nel portale di Azure se nel sistema sono presenti voci duplicate. Per informazioni su come eliminare le voci non aggiornate e risolvere il problema, vedere [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) (Da VMware ad Azure con Azure Site Recovery: come eseguire la pulizia delle voci duplicate o non aggiornate).

## <a name="common-errors-and-recommended-steps-for-resolution"></a>I passaggi da errori comuni e consigliate per la risoluzione

### <a name="initial-replication-issues-error-78169"></a>[Errore 78169] problemi di replica iniziale

Su una precedente verifica che esistono alcuna connettività, larghezza di banda o l'ora di sincronizzazione sui problemi correlati, assicurarsi che:

- Nessun software antivirus sta bloccando Azure Site Recovery. Scopri [altre](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) su esclusioni di cartelle necessarie per Azure Site Recovery.

### <a name="application-consistency-recovery-point-missing-error-78144"></a>Punto di ripristino di coerenza dell'applicazione mancante [errore 78144]

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

### <a name="high-churn-on-source-machine-error-78188"></a>Ad alta varianza nella macchina di origine [errore 78188]

Possibili cause:
- La frequenza di modifica (byte scrittura/sec) di dati nei dischi elencati della macchina virtuale è superiore al [limiti supportati di Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) per il tipo di account archiviazione di destinazione di replica.
- Si verifica un picco improvviso della frequenza di varianza a causa di cui una quantità elevata di dati è in sospeso per il caricamento.

Per risolvere il problema:
- Assicurarsi che il tipo di account archiviazione di destinazione (Standard o Premium) è stato effettuato il provisioning in base al requisito di frequenza di varianza nell'origine.
- Se la varianza osservata è temporanea, attendere alcune ore per il caricamento dei dati in sospeso possono essere aggiornati e per creare i punti di ripristino.
- Se il problema persiste, usare ASR [deployment Planner distribuzioni](site-recovery-deployment-planner.md#overview) per facilitare la pianificazione della replica.

### <a name="no-heartbeat-from-source-machine-error-78174"></a>Nessun Heartbeat dal computer di origine [errore 78174]

Ciò si verifica quando l'agente di Azure Site Recovery Mobility nella macchina di origine non comunica con il Server di configurazione (CS).

Per risolvere il problema, usare la procedura seguente per verificare la connettività di rete dalla macchina virtuale di origine al Server di configurazione:

1. Verificare che la macchina di origine sia in esecuzione.
2. Accedere al computer di origine usando un account con privilegi di amministratore.
3. Verificare che i servizi seguenti siano in esecuzione e, se non riavviare i servizi:
   - Svagents (InMage Scout VX Agent)
   - Servizio dell'applicazione InMage Scout
4. Nella macchina di origine, esaminare i log in corrispondenza della posizione per i dettagli dell'errore:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="no-heartbeat-from-process-server-error-806"></a>Nessun heartbeat dal Server di elaborazione [errore 806]
Nel caso in cui non si verifica nessun heartbeat dal Server di elaborazione, verificare quanto segue:
1. PS VM sia in esecuzione
2. Controllare i log di seguito nel PS per i dettagli dell'errore:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="no-heartbeat-from-master-target-error-78022"></a>Nessun Heartbeat dal server di destinazione Master [errore 78022]

Ciò si verifica quando l'agente di Azure Site Recovery Mobility nel server di destinazione Master non comunica con il Server di configurazione.

Per risolvere il problema, usare la procedura seguente per verificare lo stato del servizio:

1. Verificare che la VM di destinazione Master sia in esecuzione.
2. Accedere alla VM di destinazione Master usando un account con privilegi di amministratore.
    - Verificare che il servizio svagents sia in esecuzione. Se è in esecuzione, riavviare il servizio
    - Controllare i log in corrispondenza della posizione per i dettagli dell'errore:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-is-not-reachable-from-the-source-machine-error-78186"></a>Server di elaborazione non è raggiungibile dalla macchina di origine [errore 78186]

Questo errore comporta punti coerenti con l'app e di arresto anomalo del sistema non vengono generati se non si viene risolto. Per risolvere il problema, seguire i collegamenti la risoluzione dei problemi:
1. Assicurarsi che [PS servizi siano in esecuzione](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Controllare i problemi di connettività macchina di origine](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Controllare i problemi di connettività del server processo](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) e seguire le indicazioni fornite per:
    - Verifica della connettività con origine
    - Problemi di proxy e firewall

### <a name="data-upload-blocked-from-source-machine-to-process-server-error-78028"></a>Caricamento dati bloccato dalla macchina di origine al Server di elaborazione [errore 78028]

Questo errore comporta punti coerenti con l'app e di arresto anomalo del sistema non vengono generati se non si viene risolto. Per risolvere il problema, seguire i collegamenti la risoluzione dei problemi:

1. Assicurarsi che [PS servizi siano in esecuzione](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Controllare i problemi di connettività macchina di origine](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Controllare i problemi di connettività del server processo](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) e seguire le indicazioni fornite per:
    - Verifica della connettività con origine
    - Problemi di proxy e firewall

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). La community è molto attiva e uno dei tecnici Microsoft potrà fornire l'assistenza richiesta.
