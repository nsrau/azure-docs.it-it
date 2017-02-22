---
title: Gestire un database SQL con SSMS | Documentazione Microsoft
description: Informazioni su come usare SQL Server Management Studio per gestire database e server di database SQL.
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: c5f1ab504bcb639260b2500d462a56ea30cab9aa


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Gestione di database SQL di Azure tramite SQL Server Management Studio

È possibile usare SQL Server Management Studio (SSMS) per amministrare i server e i database del database SQL di Azure. In questo argomento vengono illustrate le attività comuni con SSMS. Prima di iniziare, è necessario disporre già di un server e di un database creati nel database SQL di Azure. Per altre informazioni, vedere [Create your first Azure SQL Database](sql-database-get-started.md) (Creare il primo database SQL di Azure) e [Connect and Query using SSMS](sql-database-connect-query-ssms.md) (Connettersi ed eseguire query tramite SSMS).

> [!TIP]
> Per un'esercitazione che illustra come creare un server, creare un firewall basato su server, visualizzare le proprietà del server, connettersi usando SQL Server Management Studio, eseguire query nel database master, creare un database di esempio e un database vuoto, eseguire query relative alle proprietà del database, connettersi usando SQL Server Management Studio ed eseguire query nel database di esempio, vedere l'[Esercitazione introduttiva](sql-database-get-started.md).
>

Quando si lavora con il database SQL di Azure, si consiglia di utilizzare la versione più recente di SSMS. 

> [!IMPORTANT]
> Usare sempre la versione più recente di SSMS poiché che viene costantemente migliorato in modo da essere compatibile con gli ultimi aggiornamenti di Azure e del database SQL. Per scaricare la versione più recente, vedere [Scaricare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>Creazione e gestione dei database SQL di Azure
Durante la connessione al database **master** , è possibile creare nuovi database nel server e modificare o eliminare database esistenti. Nei passaggi seguenti viene descritto come completare diverse attività comuni di gestione del database tramite Management Studio. Per eseguire queste attività, assicurarsi di essere connessi al database **master** con l'account di accesso dell'entità di livello server creato al momento della configurazione del server.

Per aprire una finestra Query in Management Studio, aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi fare clic su **Nuova query**.

* Per creare un database, usare l'istruzione **CREATE DATABASE** . Per altre informazioni, vedere [CREATE DATABASE (database SQL)](https://msdn.microsoft.com/library/dn268335.aspx). La seguente istruzione consente di creare un database denominato **myTestDB** specificando che si tratta di un database Standard S0 Edition con una dimensione massima di 250 GB.
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

Fare clic su **Execute** per eseguire la query.

* Usare l'istruzione **ALTER DATABASE** per modificare un database esistente, ad esempio se si desidera modificarne il nome e l'edizione. Per altre informazioni, vedere [ALTER DATABASE (database SQL)](https://msdn.microsoft.com/library/ms174269.aspx). La seguente istruzione consente di modificare il database creato nel passaggio precedente per modificare l'edizione in Standard S1.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* Per eliminare un database esistente, utilizzare l'istruzione **DROP DATABASE** . Per altre informazioni, vedere [DROP DATABASE (database SQL)](https://msdn.microsoft.com/library/ms178613.aspx). La seguente istruzione consente di eliminare il database **myTestDB** , ma non immediatamente perché servirà per creare account di accesso nel prossimo passaggio.
  
      DROP DATABASE myTestBase;
* Il database master presenta la vista **sys.databases** che può essere utilizzata per visualizzare i dettagli di tutti i database. Per visualizzare tutti i database esistenti, eseguire la seguente istruzione:
  
      SELECT * FROM sys.databases;
* Nel database SQL l'istruzione **USE** non è supportata per passare da un database a un altro. È necessario stabilire invece una connessione diretta al database di destinazione.

> [!NOTE]
> Molte delle istruzioni Transact-SQL che consentono di creare o modificare un database devono essere eseguite nell'ambito del proprio batch e non possono essere raggruppate insieme ad altre istruzioni Transact-SQL. Per altre informazioni, vedere i dettagli specifici di ogni istruzione.
> 
> 

## <a name="create-and-manage-logins"></a>Creare e gestire gli account di accesso
Il database **master** contiene gli account di accesso riconoscendo quelli che dispongono delle autorizzazioni per creare database o ulteriori account di accesso. Per gestire gli account di accesso, connettersi al database **master** con l'account di accesso dell'entità di livello server creato al momento della configurazione del server. È possibile usare le istruzioni **CREATE LOGIN**, **ALTER LOGIN** o **DROP LOGIN** per eseguire query sul database master che gestisce gli account di accesso per l'intero server. Per altre informazioni, vedere [Gestione di database e account di accesso in database SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx). 

* Usare l'istruzione **CREATE LOGIN** per creare un account di accesso di livello server. Per altre informazioni, vedere [CREATE LOGIN (database SQL)](https://msdn.microsoft.com/library/ms189751.aspx). La seguente istruzione crea un account di accesso denominato **login1**. Sostituire **password1** con la password desiderata.
  
      CREATE LOGIN login1 WITH password='password1';
* Per concedere autorizzazioni di livello database, utilizzare l'istruzione **CREATE USER** . Tutti gli account di accesso devono essere creati nel database **master** . Per connettere un account di accesso a un database diverso, è necessario concedere a tale account autorizzazioni di livello database usando l'istruzione **CREATE USER** nel database a cui si desidera connetterlo. Per altre informazioni, vedere [CREATE USER (database SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 
* Per concedere a login1 le autorizzazioni per un database denominato **myTestDB**, eseguire i passaggi seguenti:
  
  1. Per aggiornare Esplora oggetti in modo da visualizzare il database **myTestDB** creato, fare clic con il pulsante destro del mouse sul nome del server in Esplora oggetti e quindi scegliere **Aggiorna**.  
     
     Se si è chiusa la connessione, è possibile ristabilirla selezionando **Connect Object Explorer** dal menu File.
  2. Fare clic con il pulsante destro del mouse sul database **myTestDB** e selezionare **Nuova query**.
  3. Eseguire l'istruzione seguente sul database myTestDB per creare un utente database denominato **login1User** che corrisponde all'account di accesso a livello di server **login1**.
     
         CREATE USER login1User FROM LOGIN login1;
* Usare la stored procedure **sp\_addrolemember** per concedere all'account utente il livello appropriato di autorizzazioni per il database. Per altre informazioni, vedere [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). L'istruzione seguente concede a **login1User** autorizzazioni di sola lettura del database mediante l'aggiunta di **login1User** al ruolo **db\_datareader**.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* Per modificare un account di accesso esistente, ad esempio se si desidera modificarne la password, utilizzare l'istruzione **ALTER LOGIN** . Per altre informazioni, vedere [ALTER LOGIN (database SQL)](https://msdn.microsoft.com/library/ms189828.aspx). L'istruzione **ALTER LOGIN** deve essere eseguita sul database **master**. Tornare alla finestra Query relativa a tale database. La seguente istruzione consente di modificare l'account di accesso **login1** per reimpostare la password. Sostituire **newPassword** con la password desiderata e **oldPassword** con la password corrente per l'account di accesso.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* Per eliminare un account di accesso esistente, utilizzare l'istruzione **DROP LOGIN** . L'eliminazione di un account di accesso a livello del server comporta anche l'eliminazione di eventuali account utente database associati. Per altre informazioni, vedere [DROP DATABASE (database SQL)](https://msdn.microsoft.com/library/ms178613.aspx). L'istruzione **DROP LOGIN** deve essere eseguita sul database **master**. La seguente istruzione consente di eliminare l'account di accesso **login1** .
  
      DROP LOGIN login1;
* Il database master presenta la vista **sys.sql\_logins** che può essere usata per visualizzare gli account di accesso. Per visualizzare tutti gli account di accesso esistenti, eseguire la seguente istruzione:
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Monitorare il database SQL utilizzando viste a gestione dinamica
Il database SQL supporta diverse viste a gestione dinamica che possono essere usate per il monitoraggio di database specifici. Di seguito vengono forniti alcuni esempi del tipo di dati di monitoraggio che possono essere recuperati tramite tali viste. Per informazioni dettagliate e altri esempi d'uso, vedere [Monitoraggio di database SQL di Azure mediante le viste a gestione dinamica](https://msdn.microsoft.com/library/azure/ff394114.aspx).

* L'esecuzione di query in una vista a gestione dinamica richiede autorizzazioni **VIEW DATABASE STATE** . Per concedere le autorizzazioni **VIEW DATABASE STATE** a un utente database specifico, connettersi al database ed eseguire la seguente istruzione sul database:
  
      GRANT VIEW DATABASE STATE TO login1User;
* Usare la vista **sys.dm\_db\_partition\_stats** per calcolare le dimensioni del database. La vista **sys.dm\_db\_partition\_stats** restituisce informazioni sulle pagine e sul numero di righe per ogni partizione del database, che possono essere usate per calcolare le dimensioni del database. La query seguente restituisce la dimensione del database in megabyte:
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* Usare le viste **sys.dm\_exec\_connections** e **sys.dm\_exec\_sessions** per recuperare informazioni sulle attuali connessioni degli utenti e sulle attività interne relative al database. La seguente query restituisce informazioni relative alla connessione corrente:
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* Usare la vista **sys.dm\_exec\_query\_stats** per recuperare statistiche aggregate sulle prestazioni per piani di query nella cache. La seguente query restituisce informazioni sulle cinque principali query classificate per tempo medio CPU.
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;




<!--HONumber=Feb17_HO3-->


