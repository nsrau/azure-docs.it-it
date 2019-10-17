---
title: Risoluzione dei problemi di connettività con database SQL di Microsoft Azure | Microsoft Docs
description: Viene descritto come risolvere i problemi di connettività nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 9de6d85e1fc54d60f999cfa18665067b3998a432
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390673"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Risoluzione dei problemi di connettività con database SQL di Microsoft Azure

Quando la connessione al database SQL di Azure non riesce, vengono visualizzati messaggi di errore. Questi problemi di connessione possono essere causati da SQL Azure riconfigurazione del database, dalle impostazioni del firewall, da un timeout di connessione o da informazioni di accesso non corrette. Inoltre, se viene raggiunto il limite massimo per alcune risorse del database SQL di Azure, non è possibile connettersi al database SQL di Azure.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Errore 40613: il database < x > sul server < y > non è attualmente disponibile

**Errore dettagliato**

``40613: Database <DBname> on server < server name > is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '< Tracing ID >'.``

Per risolvere il problema:

1. Controllare il [Dashboard del servizio Microsoft Azure](https://status.azure.com/status) per eventuali interruzioni note. 
2. Se non sono presenti interruzioni note, accedere al [sito Web di supporto Microsoft Azure](http://azure.microsoft.com/support/options) per aprire un caso di supporto.

Per ulteriori informazioni, vedere [la pagina relativa alla risoluzione dei problemi relativi all'errore "database sul server non attualmente disponibile"](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Si è verificato un errore specifico dell'istanza o relativo alla rete durante il tentativo di stabilire una connessione a SQL Server

Il problema si verifica se l'applicazione non è in grado di connettersi al server.

Per risolvere il problema, provare a eseguire i passaggi (nell'ordine presentato) nella sezione [passaggi per correggere i problemi di connessione comuni](#steps-to-fix-common-connection-issues) .

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Il server non è stato trovato o non è accessibile (errori 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>Errore 26: errore durante l'individuazione del server/dell'istanza specificati

**Errore dettagliato**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>Errore 40: Impossibile aprire una connessione a SQL Server

**Errore dettagliato**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Errore 10053: si è verificato un errore A livello di trasporto durante la ricezione dei risultati dal server

**Errore dettagliato**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Questi errori si verificano perché l'applicazione non è in grado di connettersi al server.

Per risolvere il problema, provare a eseguire i passaggi (nell'ordine presentato) nella sezione [passaggi per correggere i problemi di connessione comuni](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-servername-due-to-firewall-issues"></a>Impossibile connettersi a <servername> a causa di problemi del firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Errore 40615: Impossibile connettersi a < ServerName >

Per risolvere questo problema, [configurare le impostazioni del firewall nel database SQL tramite il portale di Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Errore 5: non è possibile connettersi al < ServerName >

Per risolvere questo problema, assicurarsi che la porta 1433 sia aperta per le connessioni in uscita su tutti i firewall tra il client e Internet.

Per ulteriori informazioni, vedere [Configure the Windows Firewall to allow SQL Server Access](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Impossibile accedere al server (errori 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Accesso non riuscito per l'utente ' < nome utente >'

**Errore dettagliato**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Per risolvere questo problema, contattare l'amministratore del servizio per fornire un nome utente e una password SQL Server validi.

In genere, l'amministratore del servizio può utilizzare la procedura seguente per aggiungere le credenziali di accesso:

1. Accedere al server usando SQL Server Management Studio (SSMS).
2. Eseguire la query SQL seguente per verificare se il nome dell'account di accesso è disabilitato:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Se il nome corrispondente è disabilitato, abilitarlo usando l'istruzione seguente: 

   ```
   Alter login <User name> enable
   ```

4. Se il nome utente dell'account di accesso SQL non esiste, crearlo attenendosi alla procedura seguente:

   1. In SSMS fare doppio clic su **sicurezza** per espanderla. 
   2. Fare clic con il pulsante destro del mouse su **Account di accesso** e scegliere **Nuovo account di accesso**. 
   3. Nello script generato con segnaposto modificare ed eseguire la query SQL seguente:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Fare doppio clic su **Database**. 
6. Selezionare il database a cui si desidera concedere l'autorizzazione utente.
7. Fare doppio clic su **Sicurezza**. 
8. Fare clic con il pulsante destro del mouse su **Utenti** e scegliere **Nuovo utente**. 
9. Nello script generato con segnaposto modificare ed eseguire la query SQL seguente: 

   ```
   CREATE USER <user_name, sysname, user_name>          
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```
   
   > [!NOTE]
   > È inoltre possibile utilizzare `sp_addrolemember` per eseguire il mapping di utenti specifici a ruoli specifici del database.

Per altre informazioni, vedere [gestione di database e account di accesso nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Errori di timeout della connessione scaduti

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): timeout della connessione scaduto

**Errore dettagliato**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): timeout scaduto

**Errore dettagliato**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. Entity. Core. EntityException: non è stato possibile aprire il provider sottostante

**Errore dettagliato**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Non è possibile connettersi al nome del server < >

**Errore dettagliato**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Queste eccezioni possono verificarsi a causa di problemi di connessione o di query. Per verificare che l'errore sia causato da problemi di connettività, vedere [verificare se un errore è causato da un problema di connettività](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

I timeout di connessione si verificano perché l'applicazione non è in grado di connettersi al server. Per risolvere il problema, provare a eseguire i passaggi (nell'ordine presentato) nella sezione [passaggi per correggere i problemi di connessione comuni](#steps-to-fix-common-connection-issues) .

## <a name="transient-errors-errors-40197-40545"></a>Errori temporanei (errori 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Errore 40197: si è verificato un errore durante l'elaborazione della richiesta da parte del servizio. Riprovare in seguito. Codice di errore < codice >

Questo problema si verifica a causa di un errore temporaneo rilevato durante una riconfigurazione o un failover nel back-end.

Per risolvere il problema, attendere un breve periodo di tempo e riprovare. Non è richiesto alcun caso di supporto, a meno che il problema non venga reso persistente.

Come procedura consigliata, assicurarsi che sia attiva la logica di ripetizione dei tentativi. Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [risolvere gli errori temporanei e gli errori di connessione al database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-because-of-a-system-defined-limit"></a>Connessione terminata a causa di un limite definito dal sistema

### <a name="error-10928-resource-id-d"></a>Errore 10928: ID risorsa:% d

**Errore dettagliato**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Per risolvere questo problema, provare con uno dei metodi seguenti:

* Verificare se sono presenti query con esecuzione prolungata.

  > [!NOTE]
  > Si tratta di un approccio minimalista che potrebbe non risolvere il problema.

  1. Eseguire la query SQL seguente per controllare la vista [sys. dm _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) per visualizzare le richieste di blocco:

             ```
             SELECT * FROM dm_exec_requests
             ```

  2. Determinare il **buffer di input** per il blocco Head.
  3. Ottimizzare la query del blocco Head.

    Per una procedura dettagliata per la risoluzione dei problemi, vedere la pagina relativa all'esecuzione di una [query nel cloud](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Se il database raggiunge costantemente il limite nonostante l'indirizzamento delle query di blocco e con esecuzione prolungata, provare a eseguire l'aggiornamento a una delle nuove edizioni di anteprima, ad esempio [standard o Premium Edition](https://azure.microsoft.com/pricing/details/sql-database/).

Per altre informazioni sulle opzioni di prezzo del database SQL, vedere [prezzi di database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

Per altre informazioni sulle viste a gestione dinamica, vedere [viste a gestione dinamica del sistema](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Per altre informazioni su questo messaggio di errore, vedere [limiti delle risorse del database SQL per il server di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Errore 10929: ID risorsa: 1

**Errore dettagliato**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

Per ulteriori informazioni su questo errore, vedere [messaggi di errore per i programmi client del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages).

### <a name="error-40501-the-service-is-currently-busy"></a>Errore 40501: il servizio è attualmente occupato

**Errore dettagliato**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Si tratta di un errore di limitazione del motore, che indica che sono stati superati i limiti delle risorse.

Per ulteriori informazioni sui limiti delle risorse, vedere [limiti delle risorse del server di database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Errore 40544: il database ha raggiunto la quota di dimensioni

**Errore dettagliato**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Questo errore si verifica quando il database ha raggiunto la quota di dimensioni.

I passaggi seguenti possono essere utili per aggirare il problema o fornire opzioni aggiuntive:

1. Controllare le dimensioni correnti del database usando il dashboard nel portale di Azure.

   > [!NOTE]
   > Per identificare le tabelle che utilizzano la maggior parte dello spazio e sono quindi potenziali candidati per la pulizia, eseguire la query SQL seguente:

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Se le dimensioni correnti non superano le dimensioni massime supportate per l'edizione, è possibile utilizzare ALTER DATABASE per aumentare l'impostazione MAXSIZE. 
3. Se il database ha già superato le dimensioni massime supportate per l'edizione, provare a eseguire uno o più dei passaggi seguenti:
   - Eseguire le normali attività di pulizia del database. Ad esempio, pulire i dati indesiderati usando TRUNCATE/DELETE oppure spostare i dati in uscita usando SQL Server Integration Services (SSIS) o l'utilità per la copia bulk (BCP).
   - Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni.

   - Per la scalabilità del database, vedere [ridimensionare le risorse di un database singolo](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) e [ridimensionare le risorse](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Errore 40549: sessione terminata perché è presente una transazione con esecuzione prolungata

**Errore dettagliato**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Se si verifica ripetutamente questo errore, provare a risolvere il problema attenendosi alla procedura seguente: 

1. Controllare la vista sys. dm _exec_requests per visualizzare tutte le sessioni aperte con un valore elevato per la colonna total_elapsed_time. Eseguire questo controllo eseguendo lo script SQL seguente:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Determinare il buffer di input per la query con esecuzione prolungata. 
3. Ottimizzare la query.

Prendere in considerazione anche l'invio in batch delle query. Per informazioni sull'invio in batch, vedere [come usare l'invio in batch per migliorare le prestazioni delle applicazioni del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Per una procedura dettagliata per la risoluzione dei problemi, vedere la pagina relativa all'esecuzione di una [query nel cloud](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Errore 40551: la sessione è stata terminata a causa di un utilizzo eccessivo di TEMPDB

**Errore dettagliato**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Per risolvere questo problema, attenersi alla seguente procedura:

1. Modificare le query per ridurre l'utilizzo dello spazio della tabella temporanea. 
2. Eliminare gli oggetti temporanei dopo che non sono più necessari. 
3. Troncare le tabelle o rimuovere le tabelle inutilizzate.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Errore 40552: la sessione è stata terminata a causa di un utilizzo eccessivo dello spazio del log delle transazioni

**Errore dettagliato**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Per risolvere il problema, procedere con i metodi seguenti:

* Il problema può verificarsi a causa di operazioni di inserimento, aggiornamento o eliminazione. Provare a ridurre il numero di righe utilizzate immediatamente implementando la suddivisione in batch o la suddivisione in più transazioni più piccole.
* Il problema può verificarsi a causa di operazioni di ricompilazione dell'indice. Per risolvere questo problema, assicurarsi che il numero di righe interessate nella tabella * (dimensione media del campo aggiornato in byte + 80) < 2 gigabyte (GB).

  > [!NOTE]
  > Per la ricompilazione di un indice, le dimensioni medie del campo aggiornato devono essere sostituite dalla dimensione media dell'indice.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Errore 40553: la sessione è stata terminata a causa di un utilizzo eccessivo della memoria

**Errore dettagliato**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Per risolvere il problema, provare a ottimizzare la query.

Per una procedura dettagliata per la risoluzione dei problemi, vedere la pagina relativa all'esecuzione di una [query nel cloud](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Impossibile aprire il database "Master" richiesto dall'account di accesso. Accesso non riuscito.

Questo problema si verifica perché l'account non dispone dell'autorizzazione per accedere al database master. Per impostazione predefinita, tuttavia, SQL Server Management Studio (SSMS) tenta di connettersi al database master.

Per risolvere il problema, seguire questa procedura:

1. Nella schermata di accesso di SSMS selezionare **Opzioni**e quindi selezionare **Proprietà connessione**. 
2. Nel campo **Connetti al database** immettere il nome del database predefinito dell'utente come database di accesso predefinito, quindi selezionare **Connetti**.

   ![Connection properties (Proprietà connessione)](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Verificare se un errore è causato da un problema di connettività

Per verificare se un errore è causato da un problema di connettività, esaminare l'analisi dello stack per i frame che mostrano le chiamate per aprire una connessione come le seguenti (si noti il riferimento alla classe **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Quando l'eccezione viene attivata dai problemi di query, si noterà uno stack di chiamate simile al seguente (si noti il riferimento alla classe **SqlCommand** ). In questa situazione, [ottimizzare le query](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
Per istruzioni aggiuntive sull'ottimizzazione delle prestazioni, vedere le risorse seguenti:

* [Come gestire gli indici e le statistiche di Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ottimizzare le prestazioni delle query nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Monitoraggio delle prestazioni del database SQL di Azure tramite le viste a gestione dinamica](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Uso dell'archivio query nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>Passaggi per risolvere i problemi di connessione comuni

1. Verificare che TCP/IP sia abilitato come protocollo client nel server applicazioni. Per ulteriori informazioni, vedere [configure client Protocols](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Nei server applicazioni in cui non sono installati SQL Server strumenti, verificare che TCP/IP sia abilitato eseguendo **cliconfg. exe** (SQL Server utilità di rete client). 
2. Controllare la stringa di connessione dell'applicazione per assicurarsi che sia configurata correttamente. Verificare, ad esempio, che la stringa di connessione specifichi la porta corretta (1433) e il nome completo del server.
Vedere [ottenere SQL Server informazioni di connessione](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Provare ad aumentare il valore di timeout della connessione. Si consiglia di usare un timeout di connessione di almeno 30 secondi. 
4. Testare la connettività tra il server applicazioni e il database SQL di Azure usando [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), un file UDL, un ping o un Telnet. Per ulteriori informazioni, vedere [risoluzione dei](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) problemi di connettività SQL Server e [diagnostica per problemi di connettività](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Come passaggio per la risoluzione dei problemi, è inoltre possibile testare la connettività in un computer client diverso.

5. Come procedura consigliata, assicurarsi che la logica di ripetizione dei tentativi sia attiva. Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [risolvere gli errori temporanei e gli errori di connessione al database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Se questi passaggi non consentono di risolvere il problema, provare a raccogliere più dati e quindi contattare il supporto tecnico. Se l'applicazione è un servizio cloud, abilitare la registrazione. Questo passaggio restituisce l'indicatore di data e ora UTC dell'errore. Inoltre, SQL Azure restituisce l'ID traccia. Il [servizio supporto tecnico clienti Microsoft](http://azure.microsoft.com/support/options/) può utilizzare queste informazioni. 

Per altre informazioni su come abilitare la registrazione, vedere [abilitare la registrazione diagnostica per le app nel servizio app Azure](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Documenti correlati**

* [Architettura di connettività SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Controlli di accesso alla rete del database SQL di Azure e data warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
