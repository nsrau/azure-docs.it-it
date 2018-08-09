---
title: Risolvere i problemi di sincronizzazione dati SQL di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi comuni di sincronizzazione dati SQL di Azure.
services: sql-database
ms.date: 07/16/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 8ba4b32f45dd978439b08650e498c3030c618aab
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618710"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Risolvere i problemi della sincronizzazione dati SQL

Questo articolo descrive come risolvere i problemi noti di sincronizzazione dati SQL di Azure. Le possibili risoluzioni a un problema vengono fornite qui.

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problemi di sincronizzazione

- [La sincronizzazione non viene eseguita correttamente nell'interfaccia utente del portale per i database locali associati all'agente client.](#sync-fails)

- [Il gruppo di sincronizzazione è bloccato nello stato di elaborazione](#sync-stuck)

- [Vengono visualizzati dati non corretti nelle tabelle](#sync-baddata)

- [Vengono visualizzati dati della chiave primaria incoerenti dopo una sincronizzazione riuscita](#sync-pkdata)

- [Si osserva una riduzione delle prestazioni considerevole](#sync-perf)

- [Viene visualizzato un messaggio di errore: "Impossibile inserire il valore NULL nella colonna<column>. La colonna non ammette valori Null." Cosa significa e come è possibile correggere l'errore?](#sync-nulls)

- [In che modo la sincronizzazione dati gestisce i riferimenti circolari? In altre parole, quando vengono sincronizzati gli stessi dati in più gruppi di sincronizzazione e pertanto continuano a cambiare?](#sync-circ)

### <a name="sync-fails"></a>La sincronizzazione non viene eseguita correttamente nell'interfaccia utente del portale per i database locali associati all'agente client

La sincronizzazione non viene eseguita correttamente nell'interfaccia utente del portale di sincronizzazione dati SQL per i database locali associati all'agente client. Nel computer locale che esegue l'agente vengono visualizzati errori System.IO.IOException nel log eventi. Gli errori indicano che lo spazio su disco non è sufficiente.

- **Causa**. L'unità dispone di spazio sufficiente.

- **Risoluzione**. Creare più spazio nell'unità in cui si trova la directory %TEMP%.

### <a name="sync-stuck"></a> Il gruppo di sincronizzazione è bloccato nello stato di elaborazione

Un gruppo di sincronizzazione in sincronizzazione dati SQL è rimasto nello stato di elaborazione per un lungo periodo. Non risponde al comando di **arresto** e il log non indica voci nuove.

Una delle condizioni seguenti può causare il blocco di un gruppo di sincronizzazione nello stato di elaborazione:

- **Causa**. L'agente client è offline

- **Risoluzione**. Assicurarsi che l'agente client sia online, quindi riprovare.

- **Causa**. L'agente client è disinstallato o mancante.

- **Risoluzione**. Se l'agente client è disinstallato o comunque mancante:

    1. Se esiste, rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL.
    1. Installare l'agente in un computer locale che può essere lo stesso o un altro computer. Inviare quindi la chiave dell'agente generata nel portale per l'agente che viene visualizzato come offline.

- **Causa**. Il servizio di sincronizzazione dati SQL è stato arrestato.

- **Risoluzione**. Riavviare il servizio di sincronizzazione dati SQL.

    1. Nel menu **Start** cercare **Servizi**.
    1. Nei risultati della ricerca fare clic su **Servizi**.
    1. Trovare il servizio di **sincronizzazione dati SQL**.
    1. Se lo stato del servizio è **Arrestato**, fare clic con il pulsante destro del mouse sul nome del servizio e scegliere **Avvia**.

> [!NOTE]
> Se queste informazioni non hanno consentito di cambiare lo stato di elaborazione del gruppo di sincronizzazione, il supporto tecnico Microsoft può reimpostare lo stato del gruppo di sincronizzazione. Per fare in modo che lo stato del gruppo di sincronizzazione venga reimpostato, creare un post nel [forum del database SQL di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted). Nel post includere l'ID sottoscrizione e l'ID gruppo di sincronizzazione per il gruppo da reimpostare. Un tecnico del supporto tecnico Microsoft risponderà al post e comunicherà quando lo stato sarà stato reimpostato.

### <a name="sync-baddata"></a> Vengono visualizzati dati non corretti nelle tabelle

Se in una sincronizzazione vengono incluse tabelle con lo stesso nome, ma che provengono da schemi di database diversi, dopo la sincronizzazione sono presenti dati errati nelle tabelle.

- **Causa**. Il processo di provisioning di sincronizzazione dati SQL usa le stesse tabelle di rilevamento per le tabelle con lo stesso nome, ma in schemi diversi. Per questo motivo, le modifiche in entrambe le tabelle vengono applicate nella stessa tabella di rilevamento. Questa condizione causa errate modifiche ai dati durante la sincronizzazione.

- **Risoluzione**. Assicurarsi che i nomi delle tabelle interessate dalla sincronizzazione siano diversi, anche se appartengono a schemi diversi di un database.

### <a name="sync-pkdata"></a> Vengono visualizzati dati della chiave primaria incoerenti dopo una sincronizzazione riuscita

Dopo una sincronizzazione segnalata come riuscita e nonostante il log non indichi righe non riuscite o ignorate, si osserva tuttavia che i dati della chiave primaria sono incoerenti tra i database del gruppo di sincronizzazione.

- **Causa**. Si tratta di un comportamento previsto da progettazione. Le modifiche apportate a una colonna chiave primaria restituiscono dati incoerenti nelle righe in cui la chiave primaria è stata modificata.

- **Risoluzione**. Per evitare questo problema, assicurarsi che non vengano modificati dati in una colonna chiave primaria. Per risolvere questo problema dopo che si è verificato, eliminare la riga contenente i dati incoerenti da tutti gli endpoint del gruppo di sincronizzazione. Reinserire quindi la riga.

### <a name="sync-perf"></a> Si osserva una riduzione delle prestazioni considerevole

Le prestazioni vengono considerevolmente ridotte, anche al punto da non poter aprire l'interfaccia utente di sincronizzazione dati.

- **Causa**. La causa più probabile è un ciclo di sincronizzazione. Un ciclo di sincronizzazione si verifica quando una sincronizzazione per il gruppo di sincronizzazione A attiva una sincronizzazione per il gruppo di sincronizzazione B, che attiva quindi una sincronizzazione per il gruppo di sincronizzazione A. La situazione effettiva potrebbe essere più complessa e coinvolgere più di due gruppi di sincronizzazione nel ciclo. Il problema è che si è verificata un'attivazione circolare della sincronizzazione dovuta a gruppi di sincronizzazione in sovrapposizione.

- **Risoluzione**. La correzione migliore è prevenire questa situazione. Verificare che non siano presenti riferimenti circolari nei gruppi di sincronizzazione. Le righe sincronizzate da un gruppo di sincronizzazione non possono essere sincronizzate da un altro gruppo di sincronizzazione.

### <a name="sync-nulls"></a> Viene visualizzato un messaggio di errore: "Impossibile inserire il valore NULL nella colonna <column>. La colonna non ammette valori Null." Cosa significa e come è possibile correggere l'errore? 
Questo messaggio di errore indica che si è verificato uno dei due problemi seguenti:
-  Una tabella non dispone di una chiave primaria. Per risolvere il problema, aggiungere una chiave primaria a tutte le tabelle da sincronizzare.
-  Potrebbe essere presente una clausola WHERE nell'istruzione CREATE INDEX. La sincronizzazione dati non gestisce questa condizione. Per risolvere il problema, rimuovere la clausola WHERE o apportare manualmente le modifiche a tutti i database. 
 
### <a name="sync-circ"></a> In che modo la sincronizzazione dati gestisce i riferimenti circolari? In altre parole, quando vengono sincronizzati gli stessi dati in più gruppi di sincronizzazione e pertanto continuano a cambiare?
La sincronizzazione dati non gestisce i riferimenti circolari. Si consiglia di evitarli. 

## <a name="client-agent-issues"></a>Problemi relativi all'agente client

- [L'installazione, la disinstallazione o il ripristino dell'agente non riesce](#agent-install)

- [L'agente client non funziona dopo l'annullamento della disinstallazione](#agent-uninstall)

- [Il database non è presente nell'elenco dell'agente](#agent-list)

- [L'agente client non viene avviato (errore 1069)](#agent-start)

- [Non è possibile inviare la chiave dell'agente](#agent-key)

- [L'agente client non può essere eliminato dal portale se il database locale associato non è raggiungibile](#agent-delete)

- [L'app dell'agente di sincronizzazione locale non riesce a connettersi al servizio di sincronizzazione locale](#agent-connect)

### <a name="agent-install"></a> L'installazione, la disinstallazione o il ripristino dell'agente non riesce

- **Causa**. Molti scenari potrebbero causare questo errore. Per determinare la causa specifica di questo errore, analizzare i log.

- **Risoluzione**. Per trovare la causa specifica dell'errore, generare ed esaminare i log di Windows Installer. È possibile attivare la registrazione al prompt dei comandi. Ad esempio, se il file AgentServiceSetup.msi scaricato è LocalAgentHost.msi, generare e analizzare i file di log usando le righe di comando seguenti:

    -   Per le installazioni: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
    -   Per le disinstallazioni: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

    È anche possibile attivare la registrazione per tutte le installazioni eseguite da Windows Installer. L'articolo della Microsoft Knowledge Base [Come abilitare la registrazione di Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornisce una soluzione con un clic per attivare la registrazione per Windows Installer e il percorso dei log.

### <a name="agent-uninstall"></a> L'agente client non funziona dopo l'annullamento della disinstallazione

L'agente client non funziona, anche dopo che è stata annullata la disinstallazione.

- **Causa**. Questo problema si verifica perché l'agente client di sincronizzazione dati SQL non archivia le credenziali.

- **Risoluzione**. È possibile provare queste due soluzioni:

    -   Usare services.msc per immettere di nuovo le credenziali per l'agente client.
    -   Disinstallare questo agente client e installarne uno nuovo. Scaricare e installare l'agente client più recente dall'[Area download](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Il database non è presente nell'elenco dell'agente

Quando si prova ad aggiungere un database di SQL Server a un gruppo di sincronizzazione, il database non è presente nell'elenco di agenti.

Molti scenari potrebbero causare questo errore.

- **Causa**. L'agente client e il gruppo di sincronizzazione si trovano in data center diversi.

- **Risoluzione**. L'agente client e il gruppo di sincronizzazione devono trovarsi nello stesso data center. A tale scopo sono disponibili due opzioni:

    -   Creare un nuovo agente nel data center in cui si trova il gruppo di sincronizzazione. Registrare quindi il database con tale agente.
    -   Eliminare il gruppo di sincronizzazione corrente. Ricreare quindi il gruppo di sincronizzazione nel data center in cui si trova l'agente.

- **Causa**. L'elenco di database dell'agente client non è aggiornato.

- **Risoluzione**. Arrestare e quindi riavviare il servizio agente client.

    L'agente locale scarica l'elenco di database associati solo al primo invio della chiave dell'agente. Non scarica l'elenco di database associati agli invii successivi della chiave dell'agente. I database registrati durante uno spostamento dell'agente non vengono quindi visualizzati nell'istanza originale dell'agente.

### <a name="agent-start"></a> L'agente client non viene avviato (errore 1069)

Si scopre che l'agente è in esecuzione in un computer che ospita SQL Server. Quando si prova ad avviare l'agente manualmente, viene visualizzata una finestra di dialogo con il messaggio "Errore 1069: Il servizio non è stato avviato a causa di un errore in fase di accesso".

![Finestra di dialogo dell'errore 1069 di sincronizzazione dei dati](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Causa**. Una probabile causa di questo errore è che la password nel server locale è stata modificata dopo la creazione dell'agente e della password dell'agente.

- **Risoluzione**. Aggiornare la password dell'agente alla password del server corrente:

  1. Individuare il servizio agente client di sincronizzazione dati SQL.  
    a. Selezionare **Avvia**.  
    b. Immettere **services.msc** nella casella di ricerca.  
    c. Nei risultati della ricerca fare clic su **Servizi**.  
    d. Nella finestra **Servizi** scorrere fino alla voce **SQL Data Sync Agent** (Agente di sincronizzazione dati SQL).  
  1. Fare clic con il pulsante destro del mouse su **SQL Data Sync Agent** (Agente di sincronizzazione dati SQL), quindi scegliere **Arresta**.
  1. Fare clic con il pulsante destro del mouse su **SQL Data Sync Agent** (Agente di sincronizzazione dati SQL), quindi scegliere **Proprietà**.
  1. In **SQL Data Sync Agent Properties** (Proprietà dell'agente di sincronizzazione dati SQL) selezionare la scheda **Accedi**.
  1. Immettere la password nella casella di testo **Password**.
  1. Reimmettere la password nella casella di testo **Conferma password**.
  1. Selezionare **Apply** (Applica) e quindi **OK**.
  1. Nella finestra **Servizi** fare clic con il pulsante destro del mouse sul servizio **SQL Data Sync Agent** (Agente di sincronizzazione dati SQL), quindi scegliere **Avvia**.
  1. Chiudere la finestra **Servizi**.

### <a name="agent-key"></a> Non è possibile inviare la chiave dell'agente

Dopo avere creato o ricreato una chiave per un agente, si prova a inviarla tramite l'applicazione SqlAzureDataSyncAgent. Non è tuttavia possibile completare l'invio.

![Finestra di dialogo di errore di sincronizzazione: non è possibile inviare una chiave per un agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Prerequisiti**. Prima di procedere, verificare i prerequisiti seguenti:

  - Il servizio di Windows di sincronizzazione dati SQL è in esecuzione.

  - L'account del servizio per il servizio di Windows di sincronizzazione dati SQL ha accesso alla rete.

  - La porta in uscita 1433 nella regola del firewall locale è aperta.

  - L'indirizzo IP locale viene aggiunto alla regola del firewall del server o del database per il database dei metadati di sincronizzazione.

- **Causa**. La chiave dell'agente identifica in modo univoco ogni agente locale. La chiave deve soddisfare due condizioni:

  -   La chiave dell'agente client nel server di sincronizzazione dati SQL e nel computer locale deve essere identica.
  -   La chiave dell'agente client può essere usata una sola volta.

- **Risoluzione**. L'agente non funziona perché una o entrambe queste condizioni non sono soddisfatte. Per fare in modo che l'agente funzioni nuovamente:

  1. Generare una nuova chiave.
  1. Applicare la nuova chiave all'agente.

  Per applicare la nuova chiave all'agente:

  1. In Esplora File passare alla directory di installazione dell'agente. La directory di installazione predefinita è C:\\Program Files (x86)\\Microsoft SQL Data Sync.
  1. Fare doppio clic sulla sottodirectory bin.
  1. Aprire l'app SqlAzureDataSyncAgent.
  1. Selezionare **Submit Agent Key** (Invia la chiave dell'agente).
  1. Incollare la chiave dagli Appunti nello spazio disponibile.
  1. Selezionare **OK**.
  1. Chiudere il programma.

### <a name="agent-delete"></a> L'agente client non può essere eliminato dal portale se il database locale associato non è raggiungibile

Se un endpoint locale, ovvero un database, registrato con un agente client di sincronizzazione dati SQL non è raggiungibile, l'agente client non può essere eliminato.

- **Causa**. L'agente locale non può essere eliminato perché il database non raggiungibile è ancora registrato con l'agente. Quando si prova a eliminare l'agente, il processo di eliminazione prova a raggiungere il database, ma non riesce.

- **Risoluzione**. Usare l'eliminazione forzata per eliminare il database non raggiungibile.

> [!NOTE]
> Se dopo una eliminazione forzata le tabelle di metadati di sincronizzazione sono ancora presenti, usare `deprovisioningutil.exe` per pulirle.

### <a name="agent-connect"></a> L'app dell'agente di sincronizzazione locale non riesce a connettersi al servizio di sincronizzazione locale

- **Risoluzione**. Attenersi alla procedura seguente:

  1. Uscire dall'app.  
  1. Aprire il pannello Servizi componenti.  
    a. Nella casella di ricerca della barra delle applicazioni immettere **services.msc**.  
    b. Fare doppio clic su **Servizi** nei risultati della ricerca.  
  1. Arrestare il servizio di **sincronizzazione dati SQL**.
  1. Riavviare il servizio di **sincronizzazione dati SQL**.  
  1. Riaprire l'app.

## <a name="setup-and-maintenance-issues"></a>Problemi relativi all'installazione e alla manutenzione

- [Viene visualizzato un messaggio di spazio su disco non sufficiente](#setup-space)

- [Non è possibile eliminare il gruppo di sincronizzazione](#setup-delete)

- [Non è possibile annullare la registrazione di un database SQL Server locale](#setup-unreg)

- [Non si hanno privilegi sufficienti per avviare i servizi di sistema](#setup-perms)

- [Un database ha lo stato "Non aggiornato"](#setup-date)

- [Un gruppo di sincronizzazione ha lo stato "Non aggiornato"](#setup-date2)

- [Un gruppo di sincronizzazione non può essere eliminato entro tre minuti dalla disinstallazione o dall'arresto dell'agente](#setup-delete2)

- [Cosa accade quando si ripristina un database perso o danneggiato?](#setup-restore)

### <a name="setup-space"></a> Viene visualizzato un messaggio di spazio su disco non sufficiente

- **Causa**. Il messaggio di spazio su disco non sufficiente può essere visualizzato quando rimangono ancora file da eliminare. Questa condizione può verificarsi a causa del software antivirus o perché i file sono aperti quando si prova a eseguire le operazioni di eliminazione.

- **Risoluzione**. Eliminare manualmente i file di sincronizzazione nella cartella %temp% (`del \*sync\* /s`). Eliminare quindi le sottodirectory nella cartella %temp%.

> [!IMPORTANT]
> Attendere che la sincronizzazione venga completata prima di eliminare qualsiasi file.

### <a name="setup-delete"></a> Non è possibile eliminare il gruppo di sincronizzazione

Non si riesce a eliminare un gruppo di sincronizzazione. Gli scenari seguenti possono impedire l'eliminazione di un gruppo di sincronizzazione.

- **Causa**. L'agente client è offline.

- **Risoluzione**. Assicurarsi che l'agente client sia online, quindi riprovare.

- **Causa**. L'agente client è disinstallato o mancante.

- **Risoluzione**. Se l'agente client è disinstallato o comunque mancante:  
    a. Se esiste, rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL.  
    b. Installare l'agente in un computer locale che può essere lo stesso o un altro computer. Inviare quindi la chiave dell'agente generata nel portale per l'agente che viene visualizzato come offline.

- **Causa**. Un database è offline.

- **Risoluzione**. Assicurarsi che i database SQL e i database SQL Server siano tutti online.

- **Causa**. È in corso il provisioning o la sincronizzazione del gruppo di sincronizzazione.

- **Risoluzione**. Attendere il termine del processo di provisioning o di sincronizzazione, quindi riprovare a eliminare il gruppo di sincronizzazione.

### <a name="setup-unreg"></a> Non è possibile annullare la registrazione di un database SQL Server locale

- **Causa**. Molto probabilmente si sta provando ad annullare la registrazione di un database che è già stato eliminato.

- **Risoluzione**. Per annullare la registrazione di un database SQL Server locale, selezionare il database e quindi **Forza eliminazione**.

  Se questa operazione non riesce a rimuovere il database dal gruppo di sincronizzazione:

  1. Arrestare e quindi riavviare il servizio host dell'agente client.  
    a. Selezionare il menu **Start**.  
    b. Immettere **services.msc** nella casella di ricerca.  
    c. Nella sezione **Programmi** del riquadro dei risultati della ricerca fare doppio clic su **Servizi**.  
    d. Fare clic con il pulsante destro del mouse sul servizio di **sincronizzazione dati SQL**.  
    e. Se il servizio è in esecuzione, arrestarlo.  
    f. Fare clic con il pulsante destro del mouse sulservizio e quindi selezionare **Avvia**.  
    g. Controllare se il database è ancora registrato. Se non è più registrato, l'operazione è stata completata. In caso contrario, procedere al passaggio successivo.
  1. Aprire l'app dell'agente client (SqlAzureDataSyncAgent).
  1. Selezionare **Modifica credenziali**, quindi immettere le credenziali per il database.
  1. Procedere con l'annullamento della registrazione.

### <a name="setup-perms"></a> Non si hanno privilegi sufficienti per avviare i servizi di sistema

- **Causa**. Questo errore si verifica in due situazioni:
  -   Il nome utente e/o la password non è corretta.
  -   L'account utente specificato non ha privilegi sufficienti per accedere come servizio.

- **Risoluzione**. Concedere all'account utente le credenziali di accesso come servizio.

  1. Passare a **Start** > **Pannello di controllo** > **Strumenti di amministrazione** > **Criteri di sicurezza locali**  >  **Criteri locali** > **Assegnazione diritti utente**.
  1. Selezionare **Accesso come servizio**.
  1. Nella finestra di dialogo **Proprietà** aggiungere l'account utente.
  1. Selezionare **Apply** (Applica) e quindi **OK**.
  1. Chiudere tutte le finestre.

### <a name="setup-date"></a> Un database ha lo stato "Non aggiornato"

- **Causa**. La sincronizzazione dati SQL rimuove dal servizio i database che sono stati offline per 45 o più giorni, calcolati dal momento in cui il database è stato portato offline. Se un database è offline per 45 o più giorni e quindi torna online, lo stato viene è **Non aggiornato**.

- **Risoluzione**. Per evitare lo stato **Non aggiornato**, assicurarsi che nessuno dei database rimanga offline per 45 o più giorni.

  Se lo stato del database è **Non aggiornato**:

  1. Rimuovere il database con lo stato **Non aggiornato** dal gruppo di sincronizzazione.
  1. Aggiungere di nuovo il database al gruppo di sincronizzazione.

  > [!WARNING]
  > Si perdono tutte le modifiche apportate al database mentre era offline.

### <a name="setup-date2"></a> Un gruppo di sincronizzazione ha lo stato "Non aggiornato"

- **Causa**. Se non è possibile applicare una o più modifiche per l'intero periodo di conservazione di 45 giorni, un gruppo di sincronizzazione può diventare obsoleto.

- **Risoluzione**. Per evitare lo stato **Non aggiornato** per un gruppo di sincronizzazione, esaminare i risultati dei processi di sincronizzazione nel visualizzatore cronologia con regolarità. Analizzare e risolvere eventuali modifiche non applicate.

  Se lo stato di un gruppo di sincronizzazione è **Non aggiornato**, è necessario eliminare il gruppo di sincronizzazione e quindi ricrearlo.

### <a name="setup-delete2"></a> Un gruppo di sincronizzazione non può essere eliminato entro tre minuti dalla disinstallazione o dall'arresto dell'agente

Non è possibile eliminare un gruppo di sincronizzazione entro tre minuti dalla disinstallazione o dall'arresto dell'agente client di sincronizzazione dati SQL associato.

- **Risoluzione**.

  1. Rimuovere un gruppo di sincronizzazione mentre gli agenti di sincronizzazione associati sono online (consigliato).
  1. Se l'agente è offline, ma è installato, portarlo online nel computer locale. Attendere che lo stato dell'agente venga visualizzato come **online** nel portale di sincronizzazione dati SQL. Rimuovere quindi il gruppo di sincronizzazione.
  1. Se l'agente è offline perché è stato disinstallato:  
    a.  Se esiste, rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL.  
    b.  Installare l'agente in un computer locale che può essere lo stesso o un altro computer. Inviare quindi la chiave dell'agente generata nel portale per l'agente che viene visualizzato come offline.  
    c. Provare a eliminare il gruppo di sincronizzazione.

### <a name="setup-restore"></a> Cosa accade quando si ripristina un database perso o danneggiato?

Se si ripristina un database perso o danneggiato da un backup, potrebbe verificarsi la non convergenza dei dati nei gruppi di sincronizzazione a cui appartiene il database.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)  
-   [Configurare la sincronizzazione dati SQL](sql-database-get-started-sql-data-sync.md)  
-   [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)  
-   [Monitorare la sincronizzazione dati SQL con Log Analytics](sql-database-sync-monitor-oms.md)  
-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL:  
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)  

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
