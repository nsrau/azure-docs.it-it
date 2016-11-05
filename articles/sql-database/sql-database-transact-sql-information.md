---
title: Non sono supportate in T-SQL del Database SQL di Azure | Microsoft Docs
description: Istruzioni Transact-SQL non completamente supportate nel Database SQL di Azure
services: sql-database
documentationcenter: ''
author: BYHAM
manager: jhubbard
editor: ''
tags: ''

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/30/2016
ms.author: rick.byham@microsoft.com

---
# Differenze di Transact-SQL del Database SQL di Azure
La maggior parte delle funzionalità Transact-SQL essenziali per le applicazioni è supportata in Microsoft SQL Server e Database SQL di Azure. Segue un elenco parziale delle funzionalità supportate per le applicazioni:

* Tipi di dati.
* Operatori.
* Stringa, funzioni aritmetiche, logiche e di cursore.

Tuttavia, il Database SQL di Azure è progettato per isolare le funzionalità di tutte le dipendenze nel database **master**. Di conseguenza molte attività a livello di server non sono appropriate per il Database SQL e non sono supportate. Le funzionalità deprecate in SQL Server in genere non sono supportate nel Database SQL.

> [!NOTE]
> In questo argomento vengono illustrate le funzionalità disponibili con il Database SQL quando si esegue l'aggiornamento gratuito all'attuale versione 12 di Database SQL. Per ulteriori informazioni sulla versione 12, vedere[Novità della versione 12 di Database SQL](sql-database-v12-whats-new.md).
> 
> 

Nelle sezioni seguenti sono elencate le funzionalità parzialmente supportate e quelle non supportate del tutto.

## Funzionalità parzialmente supportate nella versione 12 di Database SQL
La versione 12 di Database SQL supporta solo alcuni degli argomenti presenti nelle istruzioni di Transact-SQL di SQL Server 2016 corrispondenti. Ad esempio, è disponibile l'istruzione CREATE PROCEDURE, anche se non sono disponibili tutte le opzioni di CREATE PROCEDURE. Per informazioni dettagliate sulle aree supportate di ogni istruzione, vedere gli argomenti sulla sintassi collegati.

* Database: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
* I DMV sono generalmente a disposizione per le funzionalità disponibili.
* Funzioni: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
* [KILL](https://msdn.microsoft.com/library/ms173730.aspx)
* Account di accesso: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* Stored procedure: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
* Tabelle: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
* Tipi (personalizzato): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
* Utenti: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
* Visualizzazioni: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## Funzionalità non supportate in Database SQL
* Regole di confronto di oggetti di sistema
* Connessione correlata: istruzioni di Endpoint, ORIGINAL\_DB\_NAME. Database SQL non supporta l'autenticazione di Windows, ma supporta l'analoga autenticazione di Azure Active Directory. Alcuni tipi di autenticazione richiedono l'ultima versione di SQL Server Management Studio. Per ulteriori informazioni, vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md).
* Query tra database mediante nomi composti da tre o quattro parti. Le query tra database di sola lettura sono supportate mediante [query di database elastici](sql-database-elastic-query-overview.md).
* Concatenamento della proprietà tra database, impostazione TRUSTWORTHY
* Agente di raccolta dati
* Diagrammi di database
* Posta elettronica database
* DATABASEPROPERTY (utilizzare invece DATABASEPROPERTYEX)
* Account di accesso EXECUTE AS
* Crittografia: EKM
* Gestione degli eventi: eventi, notifiche degli eventi, notifiche di query
* Funzionalità relative al posizionamento dei file di database, dimensioni e file di database che vengono gestiti automaticamente da Microsoft Azure.
* Funzionalità relative alla disponibilità elevata che viene gestita tramite l'account Microsoft Azure: backup, ripristino, AlwaysOn, mirroring del database, log shipping, modalità di ripristino. Per ulteriori informazioni, vedere Backup e ripristino del database SQL di Azure.
* Funzionalità che si basano sull'agente di lettura log nel database SQL: Replica push, Change Data Capture.
* Funzionalità che si basano su SQL Server Agent o sul database MSDB: processi, avvisi, operatori, gestione basata su criteri, posta elettronica database, server di gestione centrale.
* FILESTREAM
* Funzioni: fn\_get\_sql, fn\_virtualfilestats, fn\_virtualservernodes
* Tabelle temporanee globali
* Impostazioni del server relative all'hardware: memoria, thread di lavoro, affinità di CPU, flag di traccia e così via. In alternativa usare i livelli di servizio.
* HAS\_DBACCESS
* KILL STATS JOB
* Server collegati, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT e 4 nomi della parte
* Server master e di destinazione
* .NET framework [integrazione CLR con SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
* Resource governor
* Ricerca semantica
* Credenziali del server. Usare invece le credenziali con ambito database.
* Elementi a livello server: ruoli del server, IS\_SRVROLEMEMBER, sys.login\_token. Le autorizzazioni a livello di server non sono disponibili anche se alcune vengono sostituite da autorizzazioni a livello di database. Alcune DMV a livello di server non sono disponibili anche se alcune vengono sostituite da DMV a livello di database.
* Serverless express: localdb, istanze utente
* Broker di servizio
* SET REMOTE\_PROC\_TRANSACTIONS
* SHUTDOWN
* sp\_addmessage
* opzioni di sp\_configure e RECONFIGURE. Con [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) sono disponibili alcune opzioni.
* sp\_helpuser
* sp\_migrate\_user\_to\_contained
* Controllo SQL Server. Usare in alternativa il controllo di Database SQL.
* SQL Server Profiler
* Traccia SQL Server
* Flag di traccia. Alcuni elementi dei flag di traccia sono stati spostati in modalità di compatibilità.
* Debug di Transact-SQL
* Trigger: Con ambito Server o trigger di accesso
* Istruzione USE: per modificare il contesto del database in un database diverso è necessario creare una nuova connessione al nuovo database.

## Riferimento completo di Transact-SQL
Per ulteriori informazioni sulla grammatica Transact-SQL, uso ed esempi, vedere[riferimento a Transact-SQL (motore di Database)](https://msdn.microsoft.com/library/bb510741.aspx)nella documentazione Online di SQL Server.

### Informazioni sui tag "Si applica a"
Il riferimento a Transact-SQL include gli argomenti relativi alle versioni di SQL Server dalla 2008 a quella attuale. Sotto il titolo dell'argomento è presente una barra di icone in cui sono elencate le quattro piattaforme SQL Server e la relativa applicabilità. Ad esempio, i gruppi di disponibilità sono stati introdotti in SQL Server 2012. L'argomento [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) indica che l'istruzione si applica a **SQL Server (a partire dalla versione 2012). L'istruzione non si applica a SQL Server 2008, SQL Server 2008 R2, Database SQL di Azure, Azure SQL Data Warehouse o Parallel Data Warehouse.

In alcuni casi, il tema generale di un argomento può essere utilizzato in un prodotto, ma esistono differenze minime tra prodotti. Le differenze sono indicate in punti centrali nell'argomento come appropriato.

<!---HONumber=AcomDC_0831_2016-->