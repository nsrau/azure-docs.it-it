---
title: Risoluzione dei problemi di connettività con database SQL di Microsoft Azure | Microsoft Docs
description: Risoluzione dei problemi di connettività con database SQL di Microsoft Azure
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 3717b36e37f7d9cca206b5a9e8e3cded50393748
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974422"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Risoluzione dei problemi di connettività con database SQL di Microsoft Azure

Quando la connessione al database SQL di Azure non riesce, vengono visualizzati messaggi di errore. I problemi di connessione possono essere causati dalla riconfigurazione del database SQL di Azure, dalle impostazioni del firewall, dal timeout della connessione o da informazioni di accesso non corrette. Inoltre, se viene raggiunto il limite massimo per alcune risorse del database SQL di Azure, non è possibile connettersi al database SQL di Azure.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Errore 40613: Il database < x > sul server < y > non è attualmente disponibile

**Errore dettagliato**

``40613: Database <DBname> on server <server name> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '<Tracing ID>'.``

Per risolvere il problema:

1. Controllare il [Dashboard del servizio Microsoft Azure](https://status.azure.com/status) per eventuali interruzioni note. 
2. Se non sono presenti interruzioni note, passare al [sito Web di supporto Microsoft Azure](http://azure.microsoft.com/support/options) per aprire un caso di supporto.

Per ulteriori informazioni, vedere l'argomento relativo alla [risoluzione dei problemi relativi al database nel server attualmente non disponibile](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Si è verificato un errore specifico dell'istanza o relativo alla rete durante il tentativo di stabilire una connessione a SQL Server

Il problema si verifica perché l'applicazione non è in grado di connettersi al server.

Per risolvere questo problema, eseguire la procedura (in ordine) nella sezione seguente, denominata **passaggi per risolvere i problemi di connessione comuni**.

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Il server non è stato trovato o non è accessibile (errori 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>Errore 26: Errore durante l'individuazione del server/dell'istanza specificati

**Errore dettagliato**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>Errore 40: Impossibile aprire una connessione a SQL Server

**Errore dettagliato**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Errore 10053: si è verificato un errore a livello di trasporto durante la ricezione dei risultati dal server.

**Errore dettagliato**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Questi problemi si verificano perché l'applicazione non è in grado di connettersi al server.

Per risolvere questo problema, eseguire la procedura (in ordine) nella sezione seguente, denominata **passaggi per risolvere i problemi di connessione comuni**.

## <a name="cannot-connect-to--servername--due-to-firewall-issues"></a>Impossibile connettersi a < ServerName > a causa di problemi del firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Errore 40615: Impossibile connettersi a < ServerName >

Per risolvere questo problema, [configurare le impostazioni del firewall nel database SQL usando il portale di Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Errore 5: Impossibile connettersi a < ServerName >

Per risolvere questo problema, assicurarsi che la porta 1433 sia aperta per le connessioni in uscita su tutti i firewall tra il client e Internet. 

Per ulteriori informazioni, vedere [Configure the Windows Firewall to Allow SQL Server Access](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-login-to-the-server-errors-18456-40531"></a>Impossibile accedere al server (errori 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Accesso non riuscito per l'utente ' < nome utente >'

**Errore dettagliato**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Per risolvere questo problema, contattare l'amministratore del servizio per fornire un nome utente e una password SQL validi.

In genere, l'amministratore del servizio può seguire la procedura seguente per aggiungere l'account di accesso:

1. Accedere al server usando SQL Server Management Studio (SSMS).
2. Controllare se il nome di accesso è disabilitato usando la query SQL seguente:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Se il nome corrispondente è disabilitato, abilitarlo usando l'istruzione seguente: 

   ```
   Alter login <User name> enable
   ```

4. Se il nome utente dell'account di accesso SQL non esiste, crearlo usando SSMS:

   1. Fare doppio clic su **Sicurezza** per espandere la selezione. 
   2. Fare clic con il pulsante destro del mouse su **Account di accesso** e scegliere **Nuovo account di accesso**. 
   3. Nello script generato con segnaposto è possibile modificare ed eseguire la query SQL seguente:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Fare doppio clic su **Database**. 
6. Selezionare il database a cui si desidera concedere l'autorizzazione per l'utente.
7. Fare doppio clic su **Sicurezza**. 
8. Fare clic con il pulsante destro del mouse su **Utenti** e scegliere **Nuovo utente**. 
9. Nello script generato con segnaposto è possibile modificare ed eseguire la query SQL seguente: 

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

Per altre informazioni, vedere [Gestione di database e account di accesso in database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Errori di timeout della connessione scaduti

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): Timeout della connessione scaduto.

**Errore dettagliato**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): Timeout scaduto.

**Errore dettagliato**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. Entity. Core. EntityException: Non è stato possibile aprire il provider sottostante.

**Errore dettagliato**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Non è possibile connettersi al nome del server < > .''

**Errore dettagliato**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Queste eccezioni possono verificarsi a causa di problemi di connessione o di query. Per confermare l'errore a causa di problemi di connettività, vedere la sezione seguente intitolata **verificare se l'errore è dovuto a un problema di connettività**:

I timeout di connessione si verificano perché l'applicazione non è in grado di connettersi al server. Per risolvere questo problema, eseguire la procedura (in ordine) nella sezione seguente, denominata **passaggi per risolvere i problemi di connessione comuni**.

## <a name="transient-errors-errors-40197-40545"></a>Errori temporanei (errori 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Errore 40197: Il servizio ha rilevato un errore durante l'elaborazione della richiesta. Riprova più tardi. Codice di errore < codice >

Questo problema si verifica a causa di un errore temporaneo rilevato durante una riconfigurazione o un failover nel back-end.

Per risolvere il problema, attendere un breve periodo di tempo e riprovare. Non è richiesto alcun caso di supporto, a meno che il problema rimanga persistente.

Come procedura consigliata, assicurarsi che la logica di ripetizione dei tentativi sia attiva. Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [risolvere gli errori temporanei e gli errori di connessione al database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-due-to-hitting-some-system-defined-limit"></a>Connessione terminata a causa di un limite definito dal sistema

### <a name="error-10928-resource-id-d"></a>Errore 10928: ID risorsa: %d.

**Errore dettagliato**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Per risolvere questo problema, provare con uno dei metodi seguenti:

* Verificare se sono presenti query con esecuzione prolungata:

  > [!NOTE]
  > Si tratta di un approccio minimalista che potrebbe non risolvere necessariamente il problema.

  1. Controllare la vista [sys. dm _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) per visualizzare tutte le richieste di blocco eseguendo la query SQL seguente:

     ```
     SELECT * FROM dm_exec_requests
     ```

  2. Determinare il **buffer di input** per il blocco Head.
  3. Ottimizzare la query del blocco Head.

    Per una procedura dettagliata per la risoluzione dei problemi, vedere la pagina relativa all'esecuzione di una [query nel cloud](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Se il database raggiunge costantemente il limite nonostante l'indirizzamento delle query di blocco e con esecuzione prolungata, provare a eseguire l'aggiornamento a una delle nuove edizioni di anteprima, ad esempio le [edizioni standard o Premium](https://azure.microsoft.com/pricing/details/sql-database/).

Per altre informazioni sulle opzioni di prezzo del database SQL, vedere [prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

Per altre informazioni sulle viste a gestione dinamica, vedere [viste a gestione dinamica del sistema](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Per altre informazioni su questo messaggio di errore, vedere [limiti delle risorse del database SQL per il server di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Errore 10929: ID risorsa: 1.

**Errore dettagliato**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

Per ulteriori informazioni su questo errore, vedere [messaggi di errore per i programmi client del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)

### <a name="error-40501-the-service-is-currently-busy"></a>Errore 40501: Il servizio è attualmente occupato

**Errore dettagliato**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Si tratta di un errore di limitazione del motore, che indica che sono stati superati i limiti delle risorse.

Per ulteriori informazioni sui limiti delle risorse, vedere [limiti delle risorse del server di database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544--the-database-has-reached-its-size-quota"></a>Errore 40544: Il database ha raggiunto la quota di dimensioni

**Errore dettagliato**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Questo errore si verifica quando il database ha raggiunto la quota di dimensioni.

I passaggi seguenti consentono di risolvere il problema o fornire opzioni aggiuntive che è possibile prendere in considerazione.

1. Controllare le dimensioni correnti del database usando il dashboard nel portale di Azure.

   > [!NOTE]
   > Per identificare le tabelle che utilizzano la maggior parte dello spazio e potenziali candidati per la pulizia, è possibile utilizzare la query SQL seguente:

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
3. Se le dimensioni del database hanno già superato le dimensioni massime supportate per l'edizione, è possibile eseguire una delle operazioni seguenti:
   1. Eseguire le normali attività di pulizia del database (pulitura dei dati indesiderati tramite troncamento/eliminazione e così via oppure spostare i dati in uscita con SSIS, bcp e così via).
   2. Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni. 
   
   *  Per la scalabilità del database, vedere [ridimensionare le risorse di un database singolo](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) e [ridimensionare le risorse](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Errore 40549: La sessione è stata terminata a causa di una transazione a esecuzione prolungata.

**Errore dettagliato**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Se si verifica ripetutamente questo messaggio di errore, provare a eseguire questi passaggi per risolvere il problema: 

1. Controllare la vista sys. dm _exec_requests per visualizzare tutte le sessioni aperte con un valore elevato per la colonna total_elapsed_time eseguendo lo script SQL seguente:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Determinare il buffer di input per la query con esecuzione prolungata. 
3. Ottimizzare la query.

Prendere in considerazione anche l'invio in batch delle query. Per informazioni sull'invio in batch, vedere [come usare l'invio in batch per migliorare le prestazioni delle applicazioni del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Per una procedura dettagliata per la risoluzione dei problemi, vedere la pagina relativa all'esecuzione di una [query nel cloud](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Errore 40551: La sessione è stata terminata a causa di un utilizzo eccessivo di TEMPDB.

**Errore dettagliato**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Per risolvere questo problema, attenersi alla seguente procedura:

1. Modificare le query per ridurre l'utilizzo dello spazio della tabella temporanea. 
2. Eliminare gli oggetti temporanei dopo che non sono più necessari. 
3. Troncare le tabelle o rimuovere le tabelle inutilizzate.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Errore 40552: La sessione è stata terminata a causa di un utilizzo eccessivo di spazio del log della transazione.

**Errore dettagliato**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Per risolvere il problema, seguire questi metodi: 

* Il problema si verifica a causa di operazioni di inserimento, aggiornamento o eliminazione. Provare a ridurre il numero di righe utilizzate immediatamente implementando la suddivisione in batch o la suddivisione in più transazioni più piccole.
* Il problema si verifica a causa delle operazioni di ricompilazione dell'indice. Assicurarsi di rispettare la formula seguente: numero di righe interessate nella tabella * (dimensione media del campo aggiornato in byte + 80) < 2 GB

  > [!NOTE]
  > Per la ricompilazione dell'indice, le dimensioni medie del campo che viene aggiornato devono essere sostituite dalla dimensione media dell'indice.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Errore 40553: La sessione è stata terminata a causa di un utilizzo eccessivo della memoria.

**Errore dettagliato**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Per risolvere il problema, provare a ottimizzare la query.

Per una procedura dettagliata per la risoluzione dei problemi, vedere la pagina relativa all'esecuzione di una [query nel cloud](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Impossibile aprire il database "Master" richiesto dall'account di accesso. Accesso non riuscito.

Questo problema si verifica perché l'account non dispone dell'autorizzazione di accesso al database master. Tuttavia, per impostazione predefinita, SQL Server Management Studio (SSMS) tenta di connettersi al database master.

Per risolvere il problema, seguire questa procedura:

1. Nella schermata di accesso di SSMS fare clic su **Opzioni**e quindi su **Proprietà connessione**. 
2. In **Connetti al database**Digitare il nome del database predefinito dell'utente come database di accesso predefinito, quindi fare clic su **Connetti**.

   ![cannot-open-database-master. png](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-due-to-a-connectivity-issue"></a>Verificare se un errore è dovuto a un problema di connettività

Per verificare se un errore è dovuto a un problema di connettività, esaminare l'analisi dello stack per i frame che mostrano le chiamate per aprire una connessione come le seguenti (si noti il riferimento alla classe **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Quando l'eccezione si verifica a causa di problemi di query, si noterà uno stack di chiamate simile a quello riportato di seguito. si noti il riferimento alla classe **SqlCommand** . In questi scenari, [ottimizzare le query](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
Per ulteriori indicazioni sull'ottimizzazione delle prestazioni, vedere gli argomenti seguenti:

* [Come gestire gli indici e le statistiche di Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ottimizzare le prestazioni delle query nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Monitoraggio delle prestazioni del database SQL di Azure tramite le viste a gestione dinamica](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Uso dell'archivio query nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>Passaggi per risolvere i problemi di connessione comuni

1. Verificare che TCP IP sia abilitato come protocollo client nel server applicazioni. Per ulteriori informazioni, vedere [configure client Protocols](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Nei server applicazioni in cui non sono installati SQL Server Tools, verificare che TCP IP sia abilitato eseguendo **cliconfg. exe** (SQL Server utilità di rete client). 
2. Controllare la stringa di connessione dell'applicazione per assicurarsi che sia configurata correttamente. Verificare, ad esempio, che la stringa di connessione specifichi la porta corretta (1433) e il nome completo del server.
Vedere [ottenere SQL Server informazioni di connessione](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Provare ad aumentare il **timeout**della connessione. Microsoft consiglia di usare un timeout di connessione di almeno 30 secondi. 
4. Testare la connettività tra il server applicazioni e il database SQL di Azure usando [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), un file UDL, un ping e un Telnet. Per ulteriori informazioni, vedere [risoluzione dei](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) problemi di connettività SQL Server e [diagnostica per problemi di connettività](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Come passaggio per la risoluzione dei problemi, è anche possibile provare a testare la connettività in un computer client diverso.

5. Come procedura consigliata, assicurarsi che la logica di ripetizione dei tentativi sia attiva. Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [risolvere gli errori temporanei e gli errori di connessione al database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Se i passaggi precedenti non consentono di risolvere il problema, provare a raccogliere più dati e contattare il supporto tecnico. Se l'applicazione è un servizio cloud, abilitare la registrazione. Questo passaggio restituisce l'indicatore di data e ora UTC dell'errore. Inoltre, SQL Azure restituisce l'ID traccia. Il [servizio supporto tecnico clienti Microsoft](http://azure.microsoft.com/support/options/) può utilizzare queste informazioni. 

Per altre informazioni su come abilitare la registrazione, vedere [abilitare la registrazione diagnostica per le app Web nel servizio app Azure](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Documenti correlati**

* [Architettura della connettività di SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Controlli di accesso alla rete del database SQL di Azure e data warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)