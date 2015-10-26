<properties
   pageTitle="Informazioni su Transact-SQL del Database SQL di Azure | Microsoft Azure"
   description="Istruzioni Transact-SQL nel Database SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/07/2015"
   ms.author="rick.byham@microsoft.com"/>

# Informazioni su Transact-SQL del Database SQL di Azure

La maggior parte delle istruzioni relative a Transact-SQL di SQL Server 2016 sono completamente supportate nel Database SQL di Microsoft Azure. Sono inclusi i tipi di dati di SQL Server, operatori e la stringa, funzioni aritmetiche, logiche e di cursore e gli altri elementi Transact-SQL da cui dipende la maggior parte delle applicazioni. Alcune funzioni non sono supportate o sono supportate parzialmente poiché il Database SQL gestisce il database in modo diverso (ad esempio file, disponibilità elevata e funzionalità di sicurezza) o per le funzionalità speciali, ad esempio Service broker. Poiché il Database SQL isola molte funzionalità dalla dipendenza nel database master, molte attività a livello di server sono inappropriate e non supportate. Le funzionalità deprecate in SQL Server in genere non sono supportate nel Database SQL.

## Eseguire l'aggiornamento alla versione 12 del Database SQL

In questo argomento vengono illustrate le funzionalità disponibili con il Database SQL quando si esegue l'aggiornamento gratuito alla versione 12 del Database di SQL. Per ulteriori informazioni sulla versione 12, vedere[Novità della versione 12 di Database SQL](sql-database-v12-whats-new.md). La versione 12 di Database SQL aggiunge miglioramenti nelle prestazioni e nella gestibilità, nonché supporto per funzionalità aggiuntive. Le funzionalità aggiuntive sono elencate di seguito, suddivise in funzionalità che sono completamente supportate e funzionalità che sono supportate parzialmente.

## Funzionalità parzialmente supportate nella versione 12 di Database SQL

La versione 12 di Database SQL supporta alcuni ma non tutti gli argomenti presenti nelle istruzioni di Transact-SQL di SQL Server 2016 corrispondenti. Ad esempio, è disponibile l'istruzione CREA PROCEDURA anche se l'opzione CON CRITTOGRAFIA di CREA PROCEDURA non è disponibile. Vedere gli argomenti collegati relativi alla sintassi per informazioni dettagliate sulle aree supportate di ogni istruzione.

- Assembly CLR:[CREATE ASSEMBLY](https://msdn.microsoft.com/library/ms189524.aspx)
- Database:[CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- I DMV sono generalmente disponibili per le funzionalità disponibili
- Funzioni:[CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 
- Account di accesso:[CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Stored procedure:[CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tabelle:[CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Tipi (personalizzato):[CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- Utenti:[CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- Visualizzazioni:[CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## Funzionalità non supportate in Database SQL

- Regole di confronto di oggetti di sistema
- Connessione correlata: istruzioni di Endpoint, ORIGINAL\_DB\_NAME. L'autenticazione di Windows non è disponibile per gli accessi o gli utenti del database indipendente.
- Query tra database, concatenamento della proprietà tra database, impostazione TRUSTWORTHY
- Agente di raccolta dati
- Diagrammi di database
- Posta elettronica database
- DATABASEPROPERTY (utilizzare invece DATABASEPROPERTYEX)
- Transazioni distribuite
- Account di accesso EXECUTE AS
- Crittografia: EKM
- Gestione degli eventi: eventi, notifiche degli eventi, notifiche di query
- Funzionalità relative al posizionamento dei file di database, dimensioni e file di database che vengono gestiti automaticamente da Microsoft Azure.
- Funzionalità relative alla disponibilità elevata che viene gestita tramite l’account Microsoft Azure: backup, ripristino, AlwaysOn, mirroring del database, log shipping, modalità di ripristino. Per ulteriori informazioni, vedere Backup e ripristino del database SQL di Azure.
- Funzionalità che si basano sull’agente di lettura log: replica, Change Data Capture.
- Funzionalità che si basano su SQL Server Agent o sul database MSDB: processi, avvisi, operatori, gestione basata su criteri, posta elettronica database, server di gestione centrale.
- FILESTREAM
- Funzioni: fn\_get\_sql, fn\_virtualfilestats, fn\_virtualservernodes
- Tabelle temporanee globali
- Impostazioni del server relative all’hardware: memoria, thread di lavoro, affinità di CPU, flag di traccia e così via. In alternativa usare i livelli di servizio.
- HAS\_DBACCESS
- KILL STATS JOB
- Server collegati, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT, 3 e 4 nomi della parte
- Server master e di destinazione
- Resource governor
- Ricerca semantica
- Credenziali del server
- Elementi a livello server: ruoli del server, IS\_SRVROLEMEMBER, sys.login\_token. Le autorizzazioni a livello di server non sono disponibili anche se alcune vengono sostituite da autorizzazioni a livello di database. Alcune DMV a livello di server non sono disponibili anche se alcune vengono sostituite da DMV a livello di database.
- Serverless express: localdb, istanze utente
- Broker di servizio
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- opzioni di sp\_configure e RECONFIGURE
- SQL Server audit (utilizzare il controllo di Database SQL invece)
- SQL Server Profiler
- Traccia SQL Server
- Flag di traccia
- Debug di Transact-SQL
- Trigger: Con ambito Server o trigger di accesso
- Istruzione USE

## Riferimento completo di Transact-SQL

Per ulteriori informazioni sulla grammatica Transact-SQL, uso ed esempi, vedere[riferimento a Transact-SQL (motore di Database)](https://msdn.microsoft.com/library/bb510741.aspx)nella documentazione Online di SQL Server.

### Informazioni sui tag "Si applica a"

Il riferimento a Transact-SQL include argomenti relativi a SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 e Database SQL di Microsoft Azure. Nella parte superiore di ogni argomento c’è una sezione che indica quali prodotti supportano l'oggetto dell'argomento. Se un prodotto viene omesso, la funzionalità descritta dall'argomento non è disponibile in tale prodotto. Ad esempio, i gruppi di disponibilità sono stati introdotti in SQL Server 2012. L’argomento**CREA AVAILABILITY GROUP**indica che si applica a**SQL Server (SQL Server 2012 fino alla versione corrente)**poiché non è applicabile a SQL Server 2008, SQL Server 2008 R2 o Database SQL di Microsoft Azure.

In alcuni casi, l'oggetto generale dell’argomento può essere utilizzato in un prodotto, ma non tutti gli argomenti sono supportati. Ad esempio, gli utenti di database indipendente sono stati introdotti in SQL Server 2012. L’istruzione**CREATE USER**può essere utilizzata in qualsiasi prodotto di SQL Server, tuttavia la sintassi **WITH PASSWORD**non può essere utilizzata con le versioni precedenti. In questo caso, ulteriori sezioni **si applica a**vengono inserite nella descrizione dell’argomento appropriato nel corpo dell'argomento.

<!---HONumber=Oct15_HO3-->