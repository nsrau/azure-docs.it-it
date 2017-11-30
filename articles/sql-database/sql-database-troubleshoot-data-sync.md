---
title: Risolvere i problemi di sincronizzazione dati SQL (anteprima) | Microsoft Docs
description: Informazioni su come risolvere i problemi comuni di sincronizzazione dati SQL di Azure (anteprima).
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 50cabbaa584671e52c1ea7efbd2ad990b8438272
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2017
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Risolvere i problemi della sincronizzazione dati SQL (anteprima)

Questo articolo descrive come risolvere i problemi noti di sincronizzazione dati SQL di Azure (anteprima). Le possibili risoluzioni a un problema vengono fornite qui.

Per una panoramica della sincronizzazione dati SQL (anteprima), vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure (anteprima)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problemi di sincronizzazione

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>La sincronizzazione non viene eseguita correttamente nell'interfaccia utente del portale per i database locali associati all'agente client.

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

La sincronizzazione non viene eseguita correttamente nell'interfaccia utente del portale di sincronizzazione dati SQL (anteprima) per i database locali associati all'agente. Nel computer locale che esegue l'agente vengono visualizzati errori System.IO.IOException nel log eventi. Gli errori indicano che lo spazio su disco non è sufficiente.

#### <a name="resolution"></a>Risoluzione

Creare più spazio nell'unità in cui si trova la directory %TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Il gruppo di sincronizzazione è bloccato nello stato di elaborazione

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Un gruppo di sincronizzazione in sincronizzazione dati SQL (anteprima) è rimasto nello stato di elaborazione per un lungo periodo. Non risponde al comando di **arresto** e il log non indica voci nuove.

#### <a name="cause"></a>Causa

Una delle condizioni seguenti può causare il blocco di un gruppo di sincronizzazione nello stato di elaborazione:

-   **L'agente client è offline**. Assicurarsi che l'agente client sia online, quindi riprovare.

-   **L'agente client è stato disinstallato o non è presente**. Se l'agente client è disinstallato o comunque mancante:

    1. Se esiste, rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL (anteprima).
    2. Installare l'agente in un computer locale che può essere lo stesso o un altro computer. Inviare quindi la chiave dell'agente generata nel portale per l'agente che viene visualizzato come offline.

-   **Il servizio di sincronizzazione dati SQL è stato arrestato**.

    1. Nel menu **Start** cercare **Servizi**.
    2. Nei risultati della ricerca fare clic su **Servizi**.
    3. Trovare il servizio **SQL Data Sync (Preview)** (Sincronizzazione dati SQL - Anteprima).
    4. Se lo stato del servizio è **Arrestato**, fare clic con il pulsante destro del mouse sul nome del servizio e scegliere **Avvia**.

#### <a name="resolution"></a>Risoluzione

Se queste informazioni non hanno consentito di cambiare lo stato di elaborazione del gruppo di sincronizzazione, il supporto tecnico Microsoft può reimpostare lo stato del gruppo di sincronizzazione. Per fare in modo che lo stato del gruppo di sincronizzazione venga reimpostato, creare un post nel [forum del database SQL di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted). Nel post includere l'ID sottoscrizione e l'ID gruppo di sincronizzazione per il gruppo da reimpostare. Un tecnico del supporto tecnico Microsoft risponderà al post e comunicherà quando lo stato sarà stato reimpostato.

### <a name="i-see-erroneous-data-in-my-tables"></a>Vengono visualizzati dati non corretti nelle tabelle

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Se in una sincronizzazione vengono incluse tabelle con lo stesso nome, ma che provengono da schemi di database diversi, dopo la sincronizzazione sono presenti dati errati nelle tabelle.

#### <a name="cause"></a>Causa

Il processo di provisioning di sincronizzazione dati SQL (anteprima) usa le stesse tabelle di rilevamento per le tabelle con lo stesso nome, ma in schemi diversi. Per questo motivo, le modifiche in entrambe le tabelle vengono applicate nella stessa tabella di rilevamento. Questa condizione causa errate modifiche ai dati durante la sincronizzazione.

#### <a name="resolution"></a>Risoluzione

Assicurarsi che i nomi delle tabelle interessate dalla sincronizzazione siano diversi, anche se appartengono a schemi diversi di un database.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>Vengono visualizzati dati della chiave primaria incoerenti dopo una sincronizzazione riuscita

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Dopo una sincronizzazione segnalata come riuscita e nonostante il log non indichi righe non riuscite o ignorate, si osserva tuttavia che i dati della chiave primaria sono incoerenti tra i database del gruppo di sincronizzazione.

#### <a name="cause"></a>Causa

Si tratta di un comportamento previsto da progettazione. Le modifiche apportate a una colonna chiave primaria restituiscono dati incoerenti nelle righe in cui la chiave primaria è stata modificata.

#### <a name="resolution"></a>Risoluzione

Per evitare questo problema, assicurarsi che non vengano modificati dati in una colonna chiave primaria.

Per risolvere questo problema dopo che si è verificato, eliminare la riga contenente i dati incoerenti da tutti gli endpoint del gruppo di sincronizzazione. Reinserire quindi la riga.

### <a name="i-see-a-significant-degradation-in-performance"></a>Si osserva una riduzione delle prestazioni considerevole

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Le prestazioni vengono considerevolmente ridotte, anche al punto da non poter aprire l'interfaccia utente di sincronizzazione dati.

#### <a name="cause"></a>Causa

La causa più probabile è un ciclo di sincronizzazione. Un ciclo di sincronizzazione si verifica quando una sincronizzazione per il gruppo di sincronizzazione A attiva una sincronizzazione per il gruppo di sincronizzazione B, che attiva quindi una sincronizzazione per il gruppo di sincronizzazione A. La situazione effettiva potrebbe essere più complessa e coinvolgere più di due gruppi di sincronizzazione nel ciclo. Il problema è che si è verificata un'attivazione circolare della sincronizzazione dovuta a gruppi di sincronizzazione in sovrapposizione.

#### <a name="resolution"></a>Risoluzione

La correzione migliore è prevenire questa situazione. Verificare che non siano presenti riferimenti circolari nei gruppi di sincronizzazione. Le righe sincronizzate da un gruppo di sincronizzazione non possono essere sincronizzate da un altro gruppo di sincronizzazione.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>Viene visualizzato un messaggio di errore: "Impossibile inserire il valore NULL nella colonna \<colonna\>. La colonna non ammette valori Null." Cosa significa e come è possibile correggere l'errore? 
Questo messaggio di errore indica che si è verificato uno dei due problemi seguenti:
-  Una tabella non dispone di una chiave primaria. Per risolvere il problema, aggiungere una chiave primaria a tutte le tabelle da sincronizzare.
-  Potrebbe essere presente una clausola WHERE nell'istruzione CREATE INDEX. La sincronizzazione dati (anteprima) non gestisce questa condizione. Per risolvere il problema, rimuovere la clausola WHERE o apportare manualmente le modifiche a tutti i database. 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>In che modo la sincronizzazione dati (anteprima) gestisce i riferimenti circolari? In altre parole, quando vengono sincronizzati gli stessi dati in più gruppi di sincronizzazione e pertanto continuano a cambiare?
La sincronizzazione dati (anteprima) non gestisce i riferimenti circolari. Si consiglia di evitarli. 

## <a name="client-agent-issues"></a>Problemi relativi all'agente client

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>L'installazione, la disinstallazione o il ripristino dell'agente non riesce

#### <a name="cause"></a>Causa

Molti scenari potrebbero causare questo errore. Per determinare la causa specifica di questo errore, analizzare i log.

#### <a name="resolution"></a>Risoluzione

Per trovare la causa specifica dell'errore, generare ed esaminare i log di Windows Installer. È possibile attivare la registrazione al prompt dei comandi. Ad esempio, se il file AgentServiceSetup.msi scaricato è LocalAgentHost.msi, generare e analizzare i file di log usando le righe di comando seguenti:

-   Per le installazioni: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Per le disinstallazioni: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

È anche possibile attivare la registrazione per tutte le installazioni eseguite da Windows Installer. L'articolo della Microsoft Knowledge Base [Come abilitare la registrazione di Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornisce una soluzione con un clic per attivare la registrazione per Windows Installer e il percorso dei log.

### <a name="my-client-agent-doesnt-work"></a>L'agente client non funziona

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Vengono visualizzati messaggi analoghi ai seguenti quando si prova a usare l'agente client:

"Sincronizzazione non riuscita con eccezione. Si è verificato un errore durante il tentativo di deserializzare il parametro www.microsoft.com/.../05:GetBatchInfoResult. Per maggiori dettagli, vedere InnerException".

"Messaggio di eccezione interna: Il tipo 'Microsoft.Synchronization.ChangeBatch' è un tipo di raccolta non valido perché non contiene un costruttore predefinito."

#### <a name="cause"></a>Causa

Si tratta di un problema noto relativo all'installazione di sincronizzazione dati SQL (anteprima). La causa più probabile di questo messaggio è una delle seguenti:

-   Si esegue Windows 8 Developer Preview.
-   È installato .NET Framework 4.5.

#### <a name="resolution"></a>Risoluzione

Assicurarsi di installare l'agente client in un computer che non esegue Windows 8 Developer Preview e che .NET Framework 4.5 non sia installato.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>L'agente client non funziona dopo l'annullamento della disinstallazione

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

L'agente client non funziona, anche dopo che è stata annullata la disinstallazione.

#### <a name="cause"></a>Causa

Questo problema si verifica perché l'agente client di sincronizzazione dati SQL (anteprima) non archivia le credenziali.

#### <a name="resolution"></a>Risoluzione

È possibile provare queste due soluzioni:

-   Usare services.msc per immettere di nuovo le credenziali per l'agente client.
-   Disinstallare questo agente client e installarne uno nuovo. Scaricare e installare l'agente client più recente dall'[Area download](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-list"></a>Il database non è presente nell'elenco dell'agente

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Quando si prova ad aggiungere un database di SQL Server a un gruppo di sincronizzazione, il database non è presente nell'elenco di agenti.

#### <a name="cause"></a>Causa

Molti scenari potrebbero causare questo errore.

-   L'agente client e il gruppo di sincronizzazione si trovano in data center diversi.
-   L'elenco di database dell'agente client non è aggiornato.

#### <a name="resolution"></a>Risoluzione

La risoluzione dipende dalla causa.

- **L'agente client e il gruppo di sincronizzazione si trovano in data center diversi**

    L'agente client e il gruppo di sincronizzazione devono trovarsi nello stesso data center. A tale scopo sono disponibili due opzioni:

    -   Creare un nuovo agente nel data center in cui si trova il gruppo di sincronizzazione. Registrare quindi il database con tale agente.
    -   Eliminare il gruppo di sincronizzazione corrente. Ricreare quindi il gruppo di sincronizzazione nel data center in cui si trova l'agente.

- **L'elenco di database dell'agente client non è aggiornato**

    Arrestare e quindi riavviare il servizio agente client.

    L'agente locale scarica l'elenco di database associati solo al primo invio della chiave dell'agente. Non scarica l'elenco di database associati agli invii successivi della chiave dell'agente. I database registrati durante uno spostamento dell'agente non vengono quindi visualizzati nell'istanza originale dell'agente.

### <a name="client-agent-doesnt-start-error-1069"></a>L'agente client non viene avviato (errore 1069)

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Si scopre che l'agente è in esecuzione in un computer che ospita SQL Server. Quando si prova ad avviare l'agente manualmente, viene visualizzata una finestra di dialogo con il messaggio "Errore 1069: Il servizio non è stato avviato a causa di un errore in fase di accesso".

![Finestra di dialogo dell'errore 1069 di sincronizzazione dei dati](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Causa

Una probabile causa di questo errore è che la password nel server locale è stata modificata dopo la creazione dell'agente e della password dell'agente.

#### <a name="resolution"></a>Risoluzione

Aggiornare la password dell'agente alla password del server corrente:

1. Individuare il servizio di anteprima dell'agente client di sincronizzazione dati SQL (anteprima).  
    a. Selezionare **Avvia**.  
    b. Immettere **services.msc** nella casella di ricerca.  
    c. Nei risultati della ricerca fare clic su **Servizi**.  
    d. Nella finestra **Servizi** scorrere fino alla voce **SQL Data Sync (Preview) Agent Preview** (Anteprima dell'agente di sincronizzazione dati SQL - Anteprima).  
2. Fare clic con il pulsante destro del mouse su **SQL Data Sync (Preview) Agent Preview)** (Anteprima dell'agente di sincronizzazione dati SQL - Anteprima), quindi selezionare **Arresta**.
3. Fare clic con il pulsante destro del mouse su **SQL Data Sync (Preview) Agent Preview** (Anteprima dell'agente di sincronizzazione dati SQL - Anteprima), quindi selezionare **Proprietà**.
4. In **SQL Data Sync (Preview) Agent Preview Properties** (Proprietà anteprima dell'agente di sincronizzazione dati SQL - Anteprima) selezionare la scheda **Accedi**.
5. Immettere la password nella casella di testo **Password**.
6. Reimmettere la password nella casella di testo **Conferma password**.
7. Selezionare **Apply** (Applica) e quindi **OK**.
8. Nella finestra **Servizi** fare clic con il pulsante destro del mouse sul servizio di **SQL Data Sync (Preview) Agent Preview** (Anteprima dell'agente di sincronizzazione dati SQL - Anteprima), quindi scegliere **Avvia**.
9. Chiudere la finestra **Servizi**.

### <a name="i-cant-submit-the-agent-key"></a>Non è possibile inviare la chiave dell'agente

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Dopo avere creato o ricreato una chiave per un agente, si prova a inviarla tramite l'applicazione SqlAzureDataSyncAgent. Non è tuttavia possibile completare l'invio.

![Finestra di dialogo di errore di sincronizzazione: non è possibile inviare una chiave per un agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Prima di procedere, verificare le condizioni seguenti:

-   Il servizio di Windows di sincronizzazione dati SQL (anteprima) è in esecuzione.  
-   L'account del servizio per il servizio di Windows di anteprima di sincronizzazione dati SQL (anteprima) ha accesso alla rete.    
-   L'agente client può contattare il servizio localizzatore. Controllare il valore della chiave del Registro di sistema seguente sia "https://locator.sync.azure.com/LocatorServiceApi.svc":  
    -   In un computer x86: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   In un computer x64: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>Causa

La chiave dell'agente identifica in modo univoco ogni agente locale. La chiave deve soddisfare due condizioni:

-   La chiave dell'agente client nel server di sincronizzazione dati SQL (anteprima) e nel computer locale deve essere identica.
-   La chiave dell'agente client può essere usata una sola volta.

#### <a name="resolution"></a>Risoluzione

L'agente non funziona perché una o entrambe queste condizioni non sono soddisfatte. Per fare in modo che l'agente funzioni nuovamente:

1. Generare una nuova chiave.
2. Applicare la nuova chiave all'agente.

Per applicare la nuova chiave all'agente:

1. In Esplora File passare alla directory di installazione dell'agente. La directory di installazione predefinita è C:\\Program Files (x86)\\Microsoft SQL Data Sync.
2. Fare doppio clic sulla sottodirectory bin.
3. Aprire l'app SqlAzureDataSyncAgent.
4. Selezionare **Submit Agent Key** (Invia la chiave dell'agente).
5. Incollare la chiave dagli Appunti nello spazio disponibile.
6. Selezionare **OK**.
7. Chiudere il programma.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>L'agente client non può essere eliminato dal portale se il database locale associato non è raggiungibile

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Se un endpoint locale, ovvero un database, registrato con un agente client di sincronizzazione dati SQL (anteprima) non è raggiungibile, l'agente client non può essere eliminato.

#### <a name="cause"></a>Causa

L'agente locale non può essere eliminato perché il database non raggiungibile è ancora registrato con l'agente. Quando si prova a eliminare l'agente, il processo di eliminazione prova a raggiungere il database, ma non riesce.

#### <a name="resolution"></a>Risoluzione

Usare l'eliminazione forzata per eliminare il database non raggiungibile.

> [!NOTE]
> Se dopo una eliminazione forzata le tabelle di metadati di sincronizzazione sono ancora presenti, usare deprovisioningutil.exe per pulirle.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>L'app dell'agente di sincronizzazione locale non riesce a connettersi al servizio di sincronizzazione locale

#### <a name="resolution"></a>Risoluzione

Attenersi alla procedura seguente:

1. Uscire dall'app.  
2. Aprire il pannello Servizi componenti.  
    a. Nella casella di ricerca della barra delle applicazioni immettere **services.msc**.  
    b. Fare doppio clic su **Servizi** nei risultati della ricerca.  
3. Arrestare il servizio **SQL Data Sync (Preview) Preview** (Anteprima di sincronizzazione dati SQL - Anteprima).
4. Riavviare il servizio **SQL Data Sync (Preview) Preview** (Anteprima di sincronizzazione dati SQL - Anteprima).  
5. Riaprire l'app.

## <a name="setup-and-maintenance-issues"></a>Problemi relativi all'installazione e alla manutenzione

### <a name="i-get-a-disk-out-of-space-message"></a>Viene visualizzato un messaggio di spazio su disco non sufficiente

#### <a name="cause"></a>Causa

Il messaggio di spazio su disco non sufficiente può essere visualizzato quando rimangono ancora file da eliminare. Questa condizione può verificarsi a causa del software antivirus o perché i file sono aperti quando si prova a eseguire le operazioni di eliminazione.

#### <a name="resolution"></a>Risoluzione

Eliminare manualmente i file di sincronizzazione nella cartella %temp% (`del \*sync\* /s`). Eliminare quindi le sottodirectory nella cartella %temp%.

> [!IMPORTANT]
> Attendere che la sincronizzazione venga completata prima di eliminare qualsiasi file.

### <a name="i-cant-delete-my-sync-group"></a>Non è possibile eliminare il gruppo di sincronizzazione

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Non si riesce a eliminare un gruppo di sincronizzazione.

#### <a name="causes"></a>Cause

Gli scenari seguenti possono impedire l'eliminazione di un gruppo di sincronizzazione.

-   L'agente client è offline.
-   L'agente client è disinstallato o mancante. 
-   Un database è offline. 
-   È in corso il provisioning o la sincronizzazione del gruppo di sincronizzazione. 

#### <a name="resolution"></a>Risoluzione

Per risolvere l'errore di eliminazione di un gruppo di sincronizzazione:

-   Assicurarsi che l'agente client sia online, quindi riprovare.
-   Se l'agente client è disinstallato o comunque mancante:  
    a. Se esiste, rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL (anteprima).  
    b. Installare l'agente in un computer locale che può essere lo stesso o un altro computer. Inviare quindi la chiave dell'agente generata nel portale per l'agente che viene visualizzato come offline.
-   Assicurarsi che il servizio di sincronizzazione dati SQL (anteprima) sia in esecuzione:  
    a. Nel menu **Start** cercare **Servizi**.  
    b. Nei risultati della ricerca fare clic su **Servizi**.  
    c. Trovare il servizio **SQL Data Sync (Preview) Preview** (Anteprima di sincronizzazione dati SQL - Anteprima).  
    d. Se lo stato del servizio è **Arrestato**, fare clic con il pulsante destro del mouse sul nome del servizio e scegliere **Avvia**.
-   Assicurarsi che i database SQL e i database SQL Server siano tutti online.
-   Attendere il termine del processo di provisioning o di sincronizzazione, quindi riprovare a eliminare il gruppo di sincronizzazione.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Non è possibile annullare la registrazione di un database SQL Server locale

#### <a name="cause"></a>Causa

Molto probabilmente si sta provando ad annullare la registrazione di un database che è già stato eliminato.

#### <a name="resolution"></a>Risoluzione

Per annullare la registrazione di un database SQL Server locale, selezionare il database e quindi **Forza eliminazione**.

Se questa operazione non riesce a rimuovere il database dal gruppo di sincronizzazione:

1. Arrestare e quindi riavviare il servizio host dell'agente client.  
    a. Selezionare il menu **Start**.  
    b. Immettere **services.msc** nella casella di ricerca.  
    c. Nella sezione **Programmi** del riquadro dei risultati della ricerca fare doppio clic su **Servizi**.  
    d. Fare clic con il pulsante destro del mouse sul servizio **SQL Data Sync (Preview)** (Sincronizzazione dati SQL - Anteprima).  
    e. Se il servizio è in esecuzione, arrestarlo.  
    f. Fare clic con il pulsante destro del mouse sulservizio e quindi selezionare **Avvia**.  
    g. Controllare se il database è ancora registrato. Se non è più registrato, l'operazione è stata completata. In caso contrario, procedere al passaggio successivo.
2. Aprire l'app dell'agente client (SqlAzureDataSyncAgent).
3. Selezionare **Modifica credenziali**, quindi immettere le credenziali per il database.
4. Procedere con l'annullamento della registrazione.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>Non si hanno privilegi sufficienti per avviare i servizi di sistema

#### <a name="cause"></a>Causa

Questo errore si verifica in due situazioni:
-   Il nome utente e/o la password non è corretta.
-   L'account utente specificato non ha privilegi sufficienti per accedere come servizio.

#### <a name="resolution"></a>Risoluzione

Concedere all'account utente le credenziali di accesso come servizio.

1. Passare a **Start** > **Pannello di controllo** > **Strumenti di amministrazione** > **Criteri di sicurezza locali**  >  **Criteri locali** > **Assegnazione diritti utente**.
2. Selezionare **Accesso come servizio**.
3. Nella finestra di dialogo **Proprietà** aggiungere l'account utente.
4. Selezionare **Apply** (Applica) e quindi **OK**.
5. Chiudere tutte le finestre.

### <a name="a-database-has-an-out-of-date-status"></a>Un database ha lo stato "Non aggiornato"

#### <a name="cause"></a>Causa

La sincronizzazione dati SQL (anteprima) rimuove dal servizio i database che sono stati offline per 45 o più giorni, calcolati dal momento in cui il database è stato portato offline. Se un database è offline per 45 o più giorni e quindi torna online, lo stato viene è **Non aggiornato**.

#### <a name="resolution"></a>Risoluzione

Per evitare lo stato **Non aggiornato**, assicurarsi che nessuno dei database rimanga offline per 45 o più giorni.

Se lo stato del database è **Non aggiornato**:

1. Rimuovere il database con lo stato **Non aggiornato** dal gruppo di sincronizzazione.
2. Aggiungere di nuovo il database al gruppo di sincronizzazione.

> [!WARNING]
> Si perdono tutte le modifiche apportate al database mentre era offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a>Un gruppo di sincronizzazione ha lo stato "Non aggiornato"

#### <a name="cause"></a>Causa

Se non è possibile applicare una o più modifiche per l'intero periodo di conservazione di 45 giorni, un gruppo di sincronizzazione può diventare obsoleto.

#### <a name="resolution"></a>Risoluzione

Per evitare lo stato **Non aggiornato** per un gruppo di sincronizzazione, esaminare i risultati dei processi di sincronizzazione nel visualizzatore cronologia con regolarità. Analizzare e risolvere eventuali modifiche non applicate.

Se lo stato di un gruppo di sincronizzazione è **Non aggiornato**, è necessario eliminare il gruppo di sincronizzazione e quindi ricrearlo.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>Un gruppo di sincronizzazione non può essere eliminato entro tre minuti dalla disinstallazione o dall'arresto dell'agente

#### <a name="description-and-symptoms"></a>Descrizione e sintomi

Non è possibile eliminare un gruppo di sincronizzazione entro tre minuti dalla disinstallazione o dall'arresto dell'agente client di sincronizzazione dati SQL (anteprima) associato.

#### <a name="resolution"></a>Risoluzione

1. Rimuovere un gruppo di sincronizzazione mentre gli agenti di sincronizzazione associati sono online (consigliato).
2. Se l'agente è offline, ma è installato, portarlo online nel computer locale. Attendere che lo stato dell'agente venga visualizzato come **online** nel portale di sincronizzazione dati SQL (anteprima). Rimuovere quindi il gruppo di sincronizzazione.
3. Se l'agente è offline perché è stato disinstallato:  
    a.  Se esiste, rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL (anteprima).  
    b.  Installare l'agente in un computer locale che può essere lo stesso o un altro computer. Inviare quindi la chiave dell'agente generata nel portale per l'agente che viene visualizzato come offline.  
    c. Provare a eliminare il gruppo di sincronizzazione.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>Cosa accade quando si ripristina un database perso o danneggiato?

Se si ripristina un database perso o danneggiato da un backup, potrebbe verificarsi la non convergenza dei dati nei gruppi di sincronizzazione a cui appartiene il database.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sincronizzazione dati SQL (anteprima), vedere:

-   [Sincronizzare i dati tra più database cloud e locali con l'anteprima di sincronizzazione dati SQL di Azure](sql-database-sync-data.md)  
-   [Impostare la sincronizzazione dati SQL di Azure (anteprima)](sql-database-get-started-sql-data-sync.md)  
-   [Procedure consigliate per la sincronizzazione dati SQL di Azure (anteprima)](sql-database-best-practices-data-sync.md)  
-   [Monitorare sincronizzazione dati SQL di Azure (anteprima) con Log Analytics di OMS](sql-database-sync-monitor-oms.md)  
-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL (anteprima):  
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL (anteprima)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
