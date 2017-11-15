---
title: Risolvere i problemi di sincronizzazione dati SQL di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi comuni di sincronizzazione dati SQL di Azure
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: bbfcac5a54f04f20dbdeeecef7c06b91128b8c6a
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="troubleshoot-issues-with-azure-sql-data-sync-preview"></a>Risolvere i problemi della sincronizzazione dati SQL di Azure (anteprima)

Questo articolo illustra come risolvere i problemi correnti noti al team di sincronizzazione dati SQL (anteprima). Le eventuali soluzioni alternative per un problema vengono fornite qui.

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure (anteprima)](sql-database-sync-data.md).
                                                           
## <a name="my-client-agent-doesnt-work"></a>L'agente client non funziona

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Vengono visualizzati messaggi di errore analoghi ai seguenti quando si prova a usare l'agente client.

"Sincronizzazione non riuscita con eccezione. Si è verificato un errore durante il tentativo di deserializzare il parametro www.microsoft.com/.../05:GetBatchInfoResult. Per maggiori dettagli, vedere InnerException.

"Messaggio di eccezione interna: Il tipo 'Microsoft.Synchronization.ChangeBatch' è un tipo di raccolta non valido perché non contiene un costruttore predefinito."


### <a name="cause"></a>Causa

Questo errore è un problema relativo alla sincronizzazione dati SQL (anteprima).

La causa più probabile di questo problema è:

-   Si esegue Windows 8 Developer Preview.

-   È installato .NET 4.5.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Assicurarsi di installare l'agente client in un computer che non esegue Windows 8 Developer Preview e che .NET Framework 4.5 non sia installato.

## <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>L'agente client non funziona dopo l'annullamento della disinstallazione

### <a name="description-and-symptoms"></a>Descrizione e sintomi

L'agente client non funziona anche se è stata annullata la disinstallazione.

### <a name="cause"></a>Causa

Questo problema si verifica perché l'agente client di sincronizzazione dati SQL (anteprima) non archivia le credenziali.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Sono disponibili due soluzioni:

-   Usare services.msc per immettere di nuovo le credenziali per l'agente client.

-   Disinstallare questo agente client e installarne uno nuovo. Scaricare e installare l'agente client più recente dall'[Area download](http://go.microsoft.com/fwlink/?linkid=221479).

## <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>Il database non è presente nell'elenco a discesa dell'agente

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Quando si prova ad aggiungere un database di SQL Server a un gruppo di sincronizzazione, il database non è presente nell'elenco a discesa.

### <a name="cause"></a>Causa

Le cause del problema possono essere diverse:

-   L'agente client e il gruppo di sincronizzazione sono in data center diversi.

-   L'elenco di database dell'agente client non è aggiornato.

### <a name="solution"></a>Soluzione

La soluzione dipende dalla causa.

#### <a name="the-client-agent-and-sync-group-are-in-different-data-centers"></a>L'agente client e il gruppo di sincronizzazione sono in data center diversi

Sia l'agente client che il gruppo di sincronizzazione devono essere nello stesso data center. Per impostare questa configurazione, effettuare una delle operazioni seguenti:

-   Creare un nuovo agente nello stesso data center del gruppo di sincronizzazione. Registrare quindi il database con tale agente. Per altre informazioni, vedere [Procedura: Installare un agente client di sincronizzazione dati SQL (anteprima)](#install-a-sql-data-sync-client-agent).

-   Eliminare il gruppo di sincronizzazione corrente. Crearlo quindi di nuovo nello stesso data center dell'agente.

#### <a name="the-client-agents-list-of-databases-is-not-current"></a>L'elenco di database dell'agente client non è aggiornato

Arrestare e quindi riavviare il servizio agente client.
L'agente locale scarica l'elenco di database associati solo al primo invio della chiave dell'agente, non agli invii successivi della chiave dell'agente. I database registrati durante uno spostamento dell'agente non vengono quindi visualizzati nell'istanza dell'agente originale.

## <a name="client-agent-doesnt-start-error-1069"></a>L'agente client non viene avviato (errore 1069)

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Si scopre che l'agente è in esecuzione in un computer che ospita SQL Server. Quando si prova ad avviare l'agente manualmente, viene visualizzata una finestra di dialogo con il messaggio di errore "Errore 1069: Il servizio non è stato avviato a causa di un errore in fase di accesso".

![Finestra di dialogo dell'errore 1069 di sincronizzazione dei dati](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

### <a name="cause"></a>Causa

Una probabile causa di questo errore è che la password nel server locale sia stata modificata dopo la creazione dell'agente e l'assegnazione di una password di accesso.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Aggiornare la password dell'agente alla password del server corrente.

1. Individuare il servizio di anteprima dell'agente client di sincronizzazione dati SQL (anteprima).

    a. Fare clic su **Avvia**.

    b. Digitare "services.msc" nella casella di ricerca.

    c. Nei risultati della ricerca fare clic su "Servizi".

    d. Nella finestra **Servizi** scorrere fino alla voce **SQL Data Sync (Preview) Agent Preview** (Anteprima dell'agente di sincronizzazione dati SQL - Anteprima).

2. Fare clic con il pulsante destro del mouse sulla voce e scegliere **Arresta**.

3. Fare clic con il pulsante destro del mouse sulla voce e quindi scegliere **Proprietà**.

4. Nella finestra **SQL Data Sync (Preview) Agent Preview Properties** (Proprietà anteprima dell'agente di sincronizzazione dati SQL - Anteprima) fare clic sulla scheda **Accedi**.

5. Immettere la password nella casella di testo Password.

6. Confermare la password nella casella di testo Conferma password.

7. Fare clic su **Applica** e quindi su **OK**.

8. Nella finestra **Servizi** fare clic con il pulsante destro del mouse sul servizio **SQL Data Sync (Preview) Agent Preview** (Anteprima dell'agente di sincronizzazione dati SQL - Anteprima), quindi scegliere **Avvia**.

9. Chiudere la finestra **Servizi**.

## <a name="i-get-a-disk-out-of-space-message"></a>Viene visualizzato un messaggio di spazio su disco non sufficiente

### <a name="cause"></a>Causa

Il messaggio di spazio su disco non sufficiente può essere visualizzato quando rimangono ancora file da eliminare. Questa condizione può verificarsi a causa del software antivirus o perché i file sono aperti quando si prova a eseguire le operazioni di eliminazione.

### <a name="solution"></a>Soluzione

La soluzione consiste nell'eliminare manualmente i file di sincronizzazione in `%temp%` (`del \*sync\* /s`) e quindi nel rimuovere anche le sottodirectory.

> [!IMPORTANT]
> Attendere che la sincronizzazione venga completata prima di eliminare qualsiasi file.

## <a name="i-cannot-delete-my-sync-group"></a>Non è possibile eliminare il gruppo di sincronizzazione

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Non si riesce a eliminare un gruppo di sincronizzazione.

### <a name="causes"></a>Cause

Le situazioni seguenti possono impedire l'eliminazione di un gruppo di sincronizzazione.

-   L'agente client è offline.

-   L'agente client è disinstallato o mancante. 

-   Un database è offline. 

-   È in corso il provisioning o la sincronizzazione del gruppo di sincronizzazione. 

### <a name="solutions"></a>Soluzioni

Per risolvere l'errore di eliminazione di un gruppo di sincronizzazione, effettuare i controlli seguenti:

-   Assicurarsi che l'agente client sia online, quindi riprovare.

-   Se l'agente client è disinstallato o comunque mancante:

    a. Rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL (anteprima) se il file esiste.

    b. Installare l'agente nello stesso computer locale o in un altro, inviare la chiave dell'agente dal portale generato per l'agente che risulta offline.

-   **Il servizio di sincronizzazione dati SQL (anteprima) è arrestato.**

    a. Nel menu **Start** cercare Servizi.

    b. Fare clic su Servizi nei risultati della ricerca.

    c. Trovare il servizio **SQL Data Sync (Preview) Preview** (Anteprima di sincronizzazione dati SQL - Anteprima).

    d. Se lo stato del servizio è **Arrestato**, fare clic con il pulsante destro del mouse sul nome del servizio e scegliere **Avvia** dal menu a discesa.

-   Controllare i database SQL e i database SQL Server per assicurarsi che siano tutti online.

-   Attendere il termine del processo di provisioning o di sincronizzazione, quindi riprovare a eliminare il gruppo di sincronizzazione.

## <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>La sincronizzazione non riesce nell'interfaccia utente del portale per i database locali associati all'agente client

### <a name="description-and-symptoms"></a>Descrizione e sintomi

La sincronizzazione non riesce nell'interfaccia utente del portale di sincronizzazione dati SQL (anteprima) per i database locali associati all'agente. Nel computer locale che esegue l'agente vengono visualizzati errori System.IO.IOException nel log eventi, indicanti che lo spazio su disco non è sufficiente.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Creare più spazio nell'unità in cui si trova la directory %TEMP%.

## <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>Non è possibile annullare la registrazione di un database SQL Server locale

### <a name="cause"></a>Causa

Molto probabilmente si sta provando ad annullare la registrazione di un database che è già stato eliminato.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Per annullare la registrazione di un database SQL Server locale, selezionare il database e fare clic su **Forza eliminazione**.

Se questa operazione non riesce a rimuovere il database dal gruppo di sincronizzazione, seguire questa procedura:

1. Arrestare e quindi riavviare il servizio host dell'agente client.

    a. Fare clic sul menu Start.

    b. Immettere *services.msc* nella casella di ricerca.

    c. Nella sezione Programmi del riquadro dei risultati fare doppio clic su **Servizi**.

    d. Trovare e fare clic con il pulsante destro del mouse sul servizio **SQL Data Sync (Preview)** (Sincronizzazione dati SQL - Anteprima).

    e. Se il servizio è in esecuzione, arrestarlo.

    f. Fare clic con il pulsante destro del mouse e scegliere **Avvia**.

    g. Controllare se il database non è più registrato. Se non è più registrato, l'operazione è stata completata. In caso contrario, procedere con il passaggio successivo.

2. Aprire l'app dell'agente client (SqlAzureDataSyncAgent).

3. Fare clic su **Modifica credenziali** e specificare le credenziali per il database in modo che sia raggiungibile.

4. Procedere con l'annullamento della registrazione.

## <a name="i-cannot-submit-the-agent-key"></a>Non è possibile inviare la chiave dell'agente

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Dopo avere creato o ricreato una chiave per un agente, si prova a inviarla tramite l'applicazione SqlAzureDataSyncAgent, ma non è possibile completare l'invio.

![Finestra di dialogo di errore di sincronizzazione: non è possibile inviare una chiave per un agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Prima di continuare, verificare che un errore di una delle condizioni seguenti non sia la causa del problema.

-   Il servizio di Windows di sincronizzazione dati SQL (anteprima) è in esecuzione.

-   L'account del servizio per il servizio di Windows di anteprima di sincronizzazione dati SQL (anteprima) ha accesso alla rete.

-   L'agente client può contattare il servizio localizzatore. Controllare che la chiave del Registro di sistema seguente abbia il valore "https://locator.sync.azure.com/LocatorServiceApi.svc"

    -   In un computer x86: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   In un computer x64: `HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

### <a name="cause"></a>Causa

La chiave dell'agente identifica in modo univoco ogni agente locale. Per funzionare, la chiave deve soddisfare due condizioni:

-   La chiave dell'agente client nel server di sincronizzazione dati SQL (anteprima) e nel computer locale deve essere identica.

-   La chiave dell'agente client può essere usata una sola volta.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

L'agente non funziona perché una o entrambe queste condizioni non sono soddisfatte. Per fare in modo che l'agente funzioni nuovamente:

1. Generare una nuova chiave.

2. Applicare la nuova chiave all'agente.

Per applicare la nuova chiave all'agente, seguire questa procedura:

1. Usare Esplora File per passare alla directory di installazione dell'agente. La directory di installazione predefinita è `c:\\program files (x86)\\microsoft sql data sync`.

2. Fare doppio clic sulla sottodirectory `bin`.

3. Avviare l'applicazione `SqlAzureDataSyncAgent`.

4. Fare clic su **Submit Agent Key** (Invia la chiave dell'agente).

5. Incollare la chiave dagli Appunti nello spazio disponibile.

6. Fare clic su **OK**.

7. Chiudere il programma.

## <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>Non si hanno privilegi sufficienti per avviare i servizi di sistema

### <a name="cause"></a>Causa

Questo errore si verifica in due situazioni:

-   Il nome utente e/o la password non è corretta.

-   L'account utente specificato non ha privilegi sufficienti per accedere come servizio.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Concedere all'account utente le credenziali accesso come servizio.

1. Passare a **Start | Pannello di controllo | Strumenti di amministrazione | Criteri di sicurezza locali | Criteri locali | Assegnazione diritti utente**.

2. Trovare e fare clic sulla voce **Accesso come servizio**.

3. Aggiungere l'account utente nella finestra di dialogo **Proprietà - Accesso come servizio**.

4. Fare clic su **Applica** e quindi su **OK**.

5. Chiudere le finestre.

## <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>L'app dell'agente di sincronizzazione locale non riesce a connettersi al servizio di sincronizzazione locale

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Attenersi alla procedura seguente:

1. Uscire dall'app.

2. Aprire il pannello Servizi componenti.

    a. Nella casella di ricerca della barra delle applicazioni digitare "services.msc".

    b. Fare doppio clic su "Servizi" nei risultati della ricerca.

3. Arrestare e quindi riavviare il servizio "SQL Data Sync (Preview) Preview" (Anteprima di sincronizzazione dati SQL - Anteprima).

4. Riavviare l'app.

## <a name="install-uninstall-or-repair-fails"></a>L'installazione, la disinstallazione o il ripristino non riesce

### <a name="cause"></a>Causa

Le cause dell'errore possono essere diverse. Per determinare la causa specifica di questo errore, è necessario analizzare i log.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Per trovare la causa specifica dell'errore riscontrato, è necessario generare ed esaminare i log di Windows Installer. È possibile attivare la registrazione dalla riga di comando. Si supponga ad esempio che il file AgentServiceSetup.msi scaricato sia LocalAgentHost.msi. Generare e analizzare i file di log usando le righe di comando seguenti:

-   Per le installazioni: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   Per le disinstallazioni: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

È anche possibile abilitare la registrazione per tutte le installazioni eseguite da Windows Installer. L'articolo della Microsoft Knowledge Base [Come abilitare la registrazione di Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornisce una soluzione con un clic per attivare la registrazione per Windows Installer e il percorso di questi log.

## <a name="a-database-has-an-out-of-date-status"></a>Un database ha lo stato "Non aggiornato"

### <a name="cause"></a>Causa

La sincronizzazione dati SQL (anteprima) rimuove i database che sono stati offline per 45 o più giorni (calcolati dal momento in cui il database è stato portato offline) dal servizio. Se un database è offline per 45 o più giorni e quindi torna online, lo stato viene impostato su "Non aggiornato".

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Per evitare lo stato "Non aggiornato", assicurarsi che nessuno dei database rimanga offline per 45 o più giorni.

Se lo stato di un database è "Non aggiornato", è necessario seguire questa procedura:

1. Rimuovere il database scaduto dal gruppo di sincronizzazione.

2. Aggiungere di nuovo il database al gruppo di sincronizzazione.

> [!WARNING]
> Si perdono tutte le modifiche apportate al database mentre era offline.

## <a name="a-sync-group-has-an-out-of-date-status"></a>Un gruppo di sincronizzazione ha lo stato "Non aggiornato"

### <a name="cause"></a>Causa

Se non è possibile applicare una o più modifiche per l'intero periodo di conservazione di 45 giorni, un gruppo di sincronizzazione può diventare obsoleto.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Per evitare lo stato "Non aggiornato", esaminare i risultati dei processi di sincronizzazione nel visualizzatore cronologia con regolarità e analizzare e risolvere eventuali modifiche che non vengono applicate.

Se lo stato di un gruppo di sincronizzazione è "Non aggiornato", è necessario eliminare il gruppo di sincronizzazione e ricrearlo.

## <a name="i-see-erroneous-data-in-my-tables"></a>Vengono visualizzati dati non corretti nelle tabelle

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Se sono interessate dalla sincronizzazione tabelle con lo stesso nome, ma da schemi diversi di un database, in queste tabelle vengono visualizzati dati non corretti dopo la sincronizzazione.

### <a name="cause-and-fix"></a>Causa e correzione

Il processo di provisioning di sincronizzazione dati SQL (anteprima) usa le stesse tabelle di rilevamento per le tabelle con lo stesso nome, ma in schemi diversi. Le modifiche da entrambe le tabelle vengono quindi applicate nella stessa tabella di rilevamento e questo comportamento causa la modifica di dati non corretti durante la sincronizzazione.

### <a name="resolution-or-workaround"></a>Risoluzione o soluzione alternativa

Assicurarsi che i nomi delle tabelle interessate dalla sincronizzazione siano diversi anche se appartengono a schemi diversi.

## <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>Vengono visualizzati dati della chiave primaria incoerenti dopo una sincronizzazione riuscita

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Dopo una sincronizzazione segnalata come riuscita e nonostante il log non indichi righe non riuscite o ignorate, si osserva che i dati della chiave primaria sono incoerenti tra i database del gruppo di sincronizzazione.

### <a name="cause"></a>Causa

Questo comportamento dipende dalla progettazione. Le modifiche apportate a una colonna chiave primaria restituiscono dati incoerenti nelle righe in cui la chiave primaria è stata modificata.

### <a name="resolution-or-workaround"></a>Risoluzione o soluzione alternativa

Per evitare questo problema, assicurarsi che non vengano modificati dati in una colonna chiave primaria.

Per risolvere il problema dopo che si è verificato, è necessario eliminare la riga interessata da tutti gli endpoint del gruppo di sincronizzazione e quindi reinserirla.

## <a name="i-see-a-significant-degradation-in-performance"></a>Si osserva una riduzione delle prestazioni considerevole

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Le prestazioni vengono considerevolmente ridotte, anche a tal punto da non poter neppure avviare l'interfaccia utente di sincronizzazione dati.

### <a name="cause"></a>Causa

La causa più probabile è un ciclo di sincronizzazione. Un ciclo di sincronizzazione si verifica quando una sincronizzazione basata sul gruppo di sincronizzazione A attiva una sincronizzazione basata sul gruppo di sincronizzazione B, che a sua volta attiva una sincronizzazione basata sul gruppo di sincronizzazione A. La situazione effettiva può essere più complessa, se coinvolge più di due gruppi di sincronizzazione nel ciclo, ma il fattore significativo è la presenza di un'attivazione circolare delle sincronizzazioni causata dalla sovrapposizione dei gruppi di sincronizzazione.

### <a name="resolution-or-workaround"></a>Risoluzione o soluzione alternativa

La correzione migliore è prevenire questa situazione. Verificare che non siano presenti riferimenti circolari nei gruppi di sincronizzazione. Le righe sincronizzate da un gruppo di sincronizzazione non possono essere sincronizzate da un altro gruppo di sincronizzazione.

## <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>L'agente client non può essere eliminato dal portale se il database locale associato non è raggiungibile

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Se un endpoint locale (ovvero un database) registrato con un agente client di sincronizzazione dati SQL (anteprima) non è raggiungibile, l'agente client non può essere eliminato.

### <a name="cause"></a>Causa

L'agente locale non può essere eliminato perché il database non raggiungibile è ancora registrato con l'agente. Quando si prova a eliminare l'agente, il processo di eliminazione prova a raggiungere il database, ma non riesce.

### <a name="resolution-or-workaround"></a>Risoluzione o soluzione alternativa

Usare l'eliminazione forzata per eliminare il database non raggiungibile.

> [!NOTE]
> Se dopo una sincronizzazione con eliminazione forzata le tabelle di metadati sono ancora presenti, usare deprovisioningutil.exe per pulirle.

## <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>Un gruppo di sincronizzazione non può essere eliminato entro tre minuti dalla disinstallazione o dall'arresto dell'agente

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Non è possibile eliminare un gruppo di sincronizzazione entro tre minuti dalla disinstallazione o dall'arresto dell'agente client di sincronizzazione dati SQL (anteprima) associato.

### <a name="resolution-or-workaround"></a>Risoluzione o soluzione alternativa

1. Rimuovere un gruppo di sincronizzazione mentre gli agenti di sincronizzazione associati sono online (consigliato).

2. Se l'agente è offline, ma è installato, portarlo online nel computer locale. Attendere quindi che lo stato dell'agente venga visualizzato come online nel portale di sincronizzazione dati SQL (anteprima) e rimuovere il gruppo di sincronizzazione.

3. Se l'agente è offline perché è stato disinstallato, seguire questa procedura, quindi provare a eliminare il gruppo di sincronizzazione.

    a.  Rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL (anteprima) se il file esiste.

    b.  Installare l'agente nello stesso computer locale o in un altro, inviare la chiave dell'agente dal portale generato per l'agente che risulta offline.

## <a name="my-sync-group-is-stuck-in-the-processing-state"></a>Il gruppo di sincronizzazione è bloccato nello stato di elaborazione

### <a name="description-and-symptoms"></a>Descrizione e sintomi

Un gruppo di sincronizzazione nella sincronizzazione dati SQL (anteprima) è da molto tempo nello stato di elaborazione, non risponde al comando di arresto e i log non visualizzano nuove voci.

### <a name="causes"></a>Cause

Una delle condizioni seguenti può causare il blocco di un gruppo di sincronizzazione nello stato di elaborazione.

-   **L'agente client è offline.** Assicurarsi che l'agente client sia online, quindi riprovare.

-   **L'agente client è disinstallato o mancante.** Se l'agente client è disinstallato o comunque mancante:

    1. Rimuovere il file XML dell'agente dalla cartella di installazione della sincronizzazione dati SQL (anteprima) se il file esiste.

    2. Installare l'agente nello stesso computer locale o in un altro. Inviare quindi la chiave dell'agente dal portale generato per l'agente visualizzato come offline.

-   **Il servizio di sincronizzazione dati SQL (anteprima) è arrestato.**

    1. Nel menu **Start** cercare Servizi.

    2. Fare clic su Servizi nei risultati della ricerca.

    3. Trovare il servizio **SQL Data Sync (Preview)** (Sincronizzazione dati SQL - Anteprima).

    4. Se lo stato del servizio è **Arrestato**, fare clic con il pulsante destro del mouse sul nome del servizio e scegliere **Avvia** dal menu a discesa.

### <a name="solution-or-workaround"></a>Soluzione o soluzione alternativa

Se non è possibile risolvere il problema, lo stato del gruppo di sincronizzazione può essere reimpostato dal supporto tecnico Microsoft. Perché lo stato venga reimpostato, creare un post nel [forum del database SQL di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted) e includere l'ID della sottoscrizione e l'ID del gruppo di sincronizzazione del gruppo che deve essere reimpostato. Un tecnico del supporto tecnico Microsoft risponderà al post e comunicherà quando lo stato sarà stato reimpostato.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sincronizzazione dati SQL, vedere:

-   [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   [Introduzione alla sincronizzazione dati SQL di Azure](sql-database-get-started-sql-data-sync.md)
-   [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)

-   Esempi di PowerShell completi che illustrano come configurare la sincronizzazione dati SQL:
    -   [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Scaricare la documentazione dell'API REST di sincronizzazione dati SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Per altre informazioni sul database SQL, vedere:

-   [Panoramica del database SQL](sql-database-technical-overview.md)
-   [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
