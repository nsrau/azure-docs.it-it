---
title: Non sono supportate in T-SQL del Database SQL di Azure | Documentazione Microsoft
description: Istruzioni Transact-SQL non completamente supportate nel Database SQL di Azure
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: rick.byham@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 3f9077733725174f1eed61d37d544e4f36822f6e
ms.openlocfilehash: d935571ccd18bc15baa000fb8c07fed11b66ba6c


---
# <a name="azure-sql-database-transact-sql-differences"></a>Differenze di Transact-SQL del Database SQL di Azure   
La maggior parte delle funzionalità Transact-SQL essenziali per le applicazioni è supportata in Microsoft SQL Server e Database SQL di Azure. Ad esempio, i componenti SQL principali, come tipi di dati, operatori, funzioni di stringa, funzioni aritmetiche, logiche, del cursore e così via, funzionano come nel server SQL.

## <a name="why-some-transact-sql-is-not-supported"></a>Perché alcune funzioni Transact-SQL non sono supportate?
Il database SQL di Azure è progettato per isolare le funzionalità di tutte le dipendenze nel database master e nel sistema operativo. Di conseguenza molte attività a livello di server non sono appropriate per il database SQL. Le istruzioni Transact-SQL in genere non sono disponibili se configurano opzioni a livello di server e componenti del sistema operativo o se specifica una configurazione del file system. Quando sono necessarie funzionalità esterne al database dell'utente, spesso è disponibile un'alternativa appropriata dal database SQL o da un'altra funzionalità o servizio di Azure. 

Ad esempio, Always On viene sostituito con la replica geografica attiva. Per questo motivo, le istruzioni Transact-SQL relative ai gruppi di disponibilità non sono supportate dal database SQL, come le viste a gestione dinamica correlate a Always On.  

Per un elenco delle funzionalità supportate e non supportate dal database SQL, vedere [Azure SQL Database considerations, guidelines and features](sql-database-features.md) (Considerazioni, linee guida e funzionalità del database SQL di Azure).

La sintassi deprecata in SQL Server in genere non è supportata nel database SQL.

## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>Sintassi di Transact-SQL parzialmente supportata nel database SQL
Il database SQL supporta solo alcuni degli argomenti presenti nelle istruzioni di Transact-SQL di SQL Server 2016 corrispondenti. Ad esempio, è disponibile l'istruzione `CREATE PROCEDURE`, anche se non sono disponibili tutte le opzioni di `CREATE PROCEDURE`. La descrizione della sintassi completa qui genererebbe confusione e risulterebbe ridondante. Per informazioni dettagliate sulle aree supportate di ogni istruzione, vedere gli argomenti sulla sintassi collegati.

- Database: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER DATABASE](https://msdn.microsoft.com/library/ms174269.aspx)   
- Funzioni: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)   
- Account di accesso: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)   
- Stored procedure: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)   
- Tabelle: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)   
- Tipi (personalizzato): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)   
- Utenti: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)   
- Visualizzazioni: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>Sintassi di Transact-SQL non supportata nel database SQL   
Oltre alle istruzioni Transact-SQL correlate alle funzioni non supportate descritte in [Azure SQL Database considerations, guidelines and features](sql-database-features.md) (Considerazioni, linee guida e funzionalità del database SQL di Azure), non sono supportate le istruzioni e i gruppi di istruzioni seguenti.
- Regole di confronto di oggetti di sistema
- Connessione correlata: istruzioni di Endpoint, `ORIGINAL_DB_NAME`. Database SQL non supporta l'autenticazione di Windows, ma supporta l'analoga autenticazione di Azure Active Directory. Alcuni tipi di autenticazione richiedono l'ultima versione di SQL Server Management Studio. Per ulteriori informazioni, vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md).
- Query tra database mediante nomi composti da tre o quattro parti. Le query tra database di sola lettura sono supportate mediante [query di database elastici](sql-database-elastic-query-overview.md).
- Concatenamento della proprietà tra database, impostazione `TRUSTWORTHY`
- `DATABASEPROPERTY` Usare invece `DATABASEPROPERTYEX`.
- `EXECUTE AS LOGIN` Usare invece 'EXECUTE AS USER'.
- La crittografia è supportata, ad eccezione della gestione delle chiavi estendibile
- Gestione degli eventi: eventi, notifiche degli eventi, notifiche di query
- Sintassi relativa al posizionamento dei file di database, dimensioni e file di database che vengono gestiti automaticamente da Microsoft Azure.
- Sintassi relativa a disponibilità elevata, gestita tramite l'account Microsoft Azure. Questa include la sintassi per backup, ripristino, AlwaysOn, mirroring del database, log shipping e modalità di ripristino.
- Sintassi che si basa sull'agente di lettura log, non disponibile nel database SQL: Replica push, Change Data Capture. Il database SQL può essere iscritto a un articolo di replica push.
- Sintassi che si basa su SQL Server Agent o sul database MSDB: avvisi, operatori, server di gestione centrale. Usare invece gli script, ad esempio Azure PowerShell.
- Funzioni: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Tabelle temporanee globali
- Sintassi delle impostazioni del server relative all'hardware: memoria, thread di lavoro, affinità di CPU, flag di traccia e così via. In alternativa usare i livelli di servizio.
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`, `BULK INSERT` e nomi in quattro parti
- .NET framework [integrazione CLR con SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Ricerca semantica
- Credenziali del server. Usare invece le credenziali con ambito database.
- Elementi a livello di server: ruoli del server, `IS_SRVROLEMEMBER`, `sys.login_token`. `GRANT`, `REVOKE` e `DENY` delle autorizzazioni a livello di server non sono disponibili anche se alcune vengono sostituite da autorizzazioni a livello di database. Alcune viste a gestione dinamica a livello di server dispongono di una vista a gestione dinamica equivalente a livello di database.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opzioni `sp_configure` e `RECONFIGURE`. Con [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)sono disponibili alcune opzioni.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Controllo SQL Server. Usare in alternativa il controllo di Database SQL.
- Traccia SQL Server
- Flag di traccia. Alcuni elementi dei flag di traccia sono stati spostati in modalità di compatibilità.
- Debug di Transact-SQL
- Trigger: Con ambito Server o trigger di accesso
- Istruzione `USE`: per modificare il contesto del database in un database diverso è necessario creare una nuova connessione al nuovo database.

## <a name="full-transact-sql-reference"></a>Riferimento completo di Transact-SQL
Per ulteriori informazioni sulla grammatica Transact-SQL, uso ed esempi, vedere [riferimento a Transact-SQL (motore di Database)](https://msdn.microsoft.com/library/bb510741.aspx) nella documentazione Online di SQL Server. 

### <a name="about-the-applies-to-tags"></a>Informazioni sui tag "Si applica a"
Il riferimento a Transact-SQL include gli argomenti relativi alle versioni di SQL Server dalla 2008 a quella attuale. Sotto il titolo dell'argomento è presente una barra di icone in cui sono elencate le quattro piattaforme SQL Server e la relativa applicabilità. Ad esempio, i gruppi di disponibilità sono stati introdotti in SQL Server 2012. L'argomento [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) indica che l'istruzione si applica a **SQL Server (a partire dalla versione 2012)**. L'istruzione non si applica a SQL Server 2008, SQL Server 2008 R2, Database SQL di Azure, Azure SQL Data Warehouse o Parallel Data Warehouse.

In alcuni casi, il tema generale di un argomento può essere utilizzato in un prodotto, ma esistono differenze minime tra prodotti. Le differenze sono indicate in punti centrali nell'argomento come appropriato.



<!--HONumber=Nov16_HO5-->


