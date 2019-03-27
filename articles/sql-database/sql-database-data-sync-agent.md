---
title: Agente di sincronizzazione dei dati per la sincronizzazione dati SQL Azure | Microsoft Docs
description: Informazioni su come installare ed eseguire l'agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure per sincronizzare i dati con i database SQL Server in locale
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: adb8917605a00208b328e7fd15f96d28c7838988
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485205"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Agente di sincronizzazione dei dati per la sincronizzazione dati SQL Azure

Sincronizzare i dati con i database SQL Server locali installando e configurando l'agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure. Per altre informazioni sulla sincronizzazione dei dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL](sql-database-sync-data.md).

> [!IMPORTANT]
> Al momento, la sincronizzazione dati SQL di Azure **non** supporta Istanza gestita di database SQL di Azure.

## <a name="download-and-install"></a>Download e installazione

Per scaricare l'agente di sincronizzazione dei dati, visitare [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Installazione invisibile all'utente

Per installare automaticamente l'agente di sincronizzazione dei dati dal prompt dei comandi, immettere un comando simile all'esempio seguente. Controllare il nome del file con estensione msi scaricato e specificare i propri valori per gli argomenti **TARGETDIR** e **SERVICEACCOUNT**.

- Se non si specifica un valore per **TARGETDIR**, il valore predefinito è `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Se si specifica `LocalSystem` come valore di **SERVICEACCOUNT**, usare l'autenticazione di SQL Server quando si configura l'agente per la connessione a SQL Server locale.

- Se come valore di **SERVICEACCOUNT** si specifica un account utente di dominio o un account utente locale, è anche necessario specificare la password con l'argomento **SERVICEPASSWORD**. Ad esempio: `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Sincronizzare i dati con SQL Server locale

Per configurare l'agente di sincronizzazione dei dati in modo da poter sincronizzare i dati con uno o più database SQL Server locali, vedere [Aggiungere un database SQL Server locale](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="agent-faq"></a> Domande frequenti sull'agente di sincronizzazione dei dati

### <a name="why-do-i-need-a-client-agent"></a>Perché è necessario un agente client?

Il servizio di sincronizzazione dati SQL comunica con i database SQL Server tramite l'agente client. Questa funzionalità di sicurezza impedisce la comunicazione diretta con i database protetti da un firewall. Per comunicare con l'agente, il servizio di sincronizzazione dati SQL usa connessioni crittografate e un token univoco o *chiave dell'agente*. I database SQL Server autenticano l'agente usando la stringa di connessione o la chiave dell'agente. Questa struttura offre un livello elevato di sicurezza per i dati.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Quante istanze dell'interfaccia utente dell'agente locale è possibile eseguire?

È possibile eseguire una sola istanza dell'interfaccia utente.

### <a name="how-can-i-change-my-service-account"></a>In che modo si può modificare l'account del servizio?

Dopo avere installato un agente client, l'unico modo per modificare l'account del servizio consiste nel disinstallarlo e installare un nuovo agente client con un nuovo account di servizio.

### <a name="how-do-i-change-my-agent-key"></a>Come si modifica la chiave dell'agente?

Una chiave dell'agente può essere usata una sola volta da un agente. Non può essere riusata quando si rimuovere l'agente e se ne installa uno nuovo. Non può essere usata neppure con più agenti. Se è necessario creare una nuova chiave per un agente esistente, assicurarsi che la stessa chiave venga registrata con l'agente client e con il servizio di sincronizzazione dati SQL.

### <a name="how-do-i-retire-a-client-agent"></a>Come si ritira un agente client?

Per invalidare o ritirare immediatamente un agente, rigenerarne la chiave nel portale, ma non inviarla all'interfaccia utente dell'agente. La rigenerazione di una chiave invalida la chiave precedente, a prescindere che l'agente sia online o offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Come si sposta un agente client in un altro computer?

Se si vuole eseguire l'agente locale da un computer diverso da quello corrente, eseguire le operazioni seguenti:

1. Installare l'agente nel computer desiderato.
2. Accedere al portale di sincronizzazione dati SQL e rigenerare una chiave per il nuovo agente.
3. Usare l'interfaccia utente del nuovo agente per inviare la nuova chiave dell'agente.
4. Attendere che l'agente client scarichi l'elenco di database locali registrati in precedenza.
5. Immettere le credenziali di database per tutti i database visualizzati come non raggiungibili. Questi database devono essere raggiungibili dal nuovo computer in cui è installato l'agente.

## <a name="agent-tshoot"></a> Risolvere i problemi relativi all'agente di sincronizzazione dei dati

- [L'installazione, la disinstallazione o il ripristino dell'agente non riesce](#agent-install)

- [L'agente client non funziona dopo l'annullamento della disinstallazione](#agent-uninstall)

- [Il database non è presente nell'elenco dell'agente](#agent-list)

- [L'agente client non viene avviato (errore 1069)](#agent-start)

- [Non è possibile inviare la chiave dell'agente](#agent-key)

- [L'agente client non può essere eliminato dal portale se il database locale associato non è raggiungibile](#agent-delete)

- [L'app dell'agente di sincronizzazione locale non riesce a connettersi al servizio di sincronizzazione locale](#agent-connect)

### <a name="agent-install"></a> L'installazione, la disinstallazione o il ripristino dell'agente non riesce

- **Causa**. Molti scenari potrebbero causare questo errore. Per determinare la causa specifica di questo errore, analizzare i log.

- **Risoluzione**. Per trovare la causa specifica dell'errore, generare ed esaminare i log di Windows Installer. È possibile attivare la registrazione al prompt dei comandi. Ad esempio, se il file di installazione scaricato è `SQLDataSyncAgent-2.0-x86-ENU.msi`, generare e analizzare i file di log usando le righe di comando seguenti:

  - Per le installazioni: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Per le disinstallazioni: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    È anche possibile attivare la registrazione per tutte le installazioni eseguite da Windows Installer. L'articolo della Microsoft Knowledge Base [Come abilitare la registrazione di Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornisce una soluzione con un clic per attivare la registrazione per Windows Installer e il percorso dei log.

### <a name="agent-uninstall"></a> L'agente client non funziona dopo l'annullamento della disinstallazione

L'agente client non funziona, anche dopo che è stata annullata la disinstallazione.

- **Causa**. Questo problema si verifica perché l'agente client di sincronizzazione dati SQL non archivia le credenziali.

- **Risoluzione**. È possibile provare queste due soluzioni:

    -   Usare services.msc per immettere di nuovo le credenziali per l'agente client.
    -   Disinstallare questo agente client e installarne uno nuovo. Scaricare e installare l'agente client più recente dall'[Area download](https://go.microsoft.com/fwlink/?linkid=221479).

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

Si scopre che l'agente è in esecuzione in un computer che ospita SQL Server. Quando si prova ad avviare l'agente manualmente, viene visualizzata una finestra di dialogo con il messaggio "Errore 1069: Il servizio non è stato avviato a causa di un errore in fase di accesso."

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

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Eseguire l'agente di sincronizzazione dei dati dal prompt dei comandi

È possibile eseguire i comandi dell'agente di sincronizzazione dei dati seguenti dal prompt dei comandi:

### <a name="ping-the-service"></a>Eseguire il ping del servizio

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Esempio

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Visualizzare i database registrati

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Esempio

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Inviare la chiave dell'agente

#### <a name="usage"></a>Uso

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Esempio

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Registrare un database

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Esempi

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Annullare la registrazione di un database

Quando si usa questo comando per annullare la registrazione di un database, si effettua il deprovisioning completo del database. Se il database fa parte di altri gruppi di sincronizzazione, questa operazione li interrompe.

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Esempio

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Aggiornare le credenziali

#### <a name="usage"></a>Uso

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Esempi

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sincronizzazione dati SQL, vedere gli articoli seguenti:

-   Panoramica: [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL di Azure](sql-database-sync-data.md)
-   Configurare la sincronizzazione dati
    - Nel portale - [Esercitazione: Configurare la sincronizzazione dati SQL per sincronizzare i dati tra il database SQL di Azure e SQL Server in locale](sql-database-get-started-sql-data-sync.md)
    - Con PowerShell
        -  [Usare PowerShell per sincronizzare più database SQL di Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Usare PowerShell per la sincronizzazione tra un database SQL di Azure e un database locale di SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Procedure consigliate: [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-database-best-practices-data-sync.md)
-   Monitoraggio: [Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure](sql-database-sync-monitor-oms.md)
-   Risoluzione dei problemi: [Risolvere i problemi della sincronizzazione dati SQL di Azure](sql-database-troubleshoot-data-sync.md)
-   Aggiornare lo schema di sincronizzazione
    -   Con Transact-SQL: [Automatizzare la replica delle modifiche dello schema nella sincronizzazione dati SQL di Azure](sql-database-update-sync-schema.md)
    -   Con PowerShell: [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](scripts/sql-database-sync-update-schema.md)
