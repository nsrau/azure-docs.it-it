---
title: Risoluzione delle differenze di T-SQL-migrazione
description: Istruzioni Transact-SQL inferiori a quelle completamente supportate nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 02b589eebb716f5a69b4db9f00faf12401b8de7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619007"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL

Quando si [esegue la migrazione del database](migrate-to-database-from-sql-server.md) da SQL Server al database SQL di Azure, è possibile rilevare che il database di SQL Server richiede una nuova progettazione prima di poterla migrare. Questo articolo offre indicazioni utili per eseguire la riprogettazione e capire i motivi che la rendono necessaria. Per rilevare le incompatibilità, usare [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Panoramica

La maggior parte delle funzionalità Transact-SQL usate dalle applicazioni è supportata in Microsoft SQL Server e nel database SQL di Azure. Ad esempio, i componenti SQL di base quali tipi di dati, operatori, stringhe, funzioni aritmetiche, logiche e cursori funzionano in modo identico in SQL Server e nel database SQL. T-SQL presenta, tuttavia, alcune differenze negli elementi DDL (Data Definition Language) e DML (Data Manipulation Language), di conseguenza alcune istruzioni e query T-SQL sono supportate solo in parte (questo verrà descritto più avanti in questo articolo).

Esistono inoltre alcune funzionalità e sintassi che non sono supportate perché il database SQL di Azure è progettato per isolare le funzionalità dalle dipendenze nel database master e nel sistema operativo. Di conseguenza, la maggior parte delle attività a livello di server non è appropriata al database SQL. Le opzioni e le istruzioni T-SQL non sono disponibili se configurano opzioni a livello di server, componenti del sistema operativo o specificano file system configurazione. Quando sono necessarie tali funzionalità, spesso è disponibile un'alternativa appropriata dal database SQL o da un'altra funzionalità o un altro servizio di Azure.

Ad esempio, la disponibilità elevata è integrata nel database SQL di Azure mediante una tecnologia simile a [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Le istruzioni T-SQL relative ai gruppi di disponibilità non sono supportate dal database SQL, così come le viste a gestione dinamica correlate ai gruppi di disponibilità AlwaysOn.

Per un elenco delle funzionalità supportate e non supportate dal database SQL, vedere  [Confronto delle funzionalità del database SQL di Azure](features-comparison.md). L'elenco in questa pagina integra le linee guida e l'articolo sulle funzionalità e si concentra sulle istruzioni Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Istruzioni di sintassi Transact-SQL con differenze parziali

Sono disponibili le istruzioni DDL (data definition language) di base, tuttavia alcune istruzioni DDL contengono estensioni relative al posizionamento del disco e a funzionalità non supportate.

- Le istruzioni CREATE e ALTER DATABASE offrono numerose opzioni. Le istruzioni includono le opzioni di posizionamento dei file, FILESTREAM e service broker che si applicano solo a SQL Server. Questo potrebbe non essere importante se si creano database prima della migrazione, ma se si esegue la migrazione di codice T-SQL che crea database è necessario confrontare [create database (database SQL di Azure)](https://msdn.microsoft.com/library/dn268335.aspx) con la sintassi SQL Server in [create database (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) per assicurarsi che tutte le opzioni usate siano supportate. CREATE DATABASE per il database SQL di Azure ha anche opzioni di obiettivo di servizio e di scalabilità elastica che si applicano solo al database SQL.
- Le istruzioni CREATE e ALTER TABLE includono opzioni FileTable che non possono essere usate nel database SQL perché FILESTREAM non è supportato.
- Sono supportate le istruzioni CREATE e ALTER LOGIN, ma il database SQL non offre tutte le opzioni. Per aumentare la portabilità del database, il database SQL invita a usare utenti contenuti nel database anziché gli account di accesso tutte le volte che è possibile. Per ulteriori informazioni, vedere [CREATE/ALTER LOGIN](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) e [Manage Logins and Users](logins-create-manage.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintassi di Transact-SQL non supportata nel database SQL di Azure

Oltre alle istruzioni Transact-SQL correlate alle funzionalità non supportate descritte in confronto tra le [funzionalità del database SQL di Azure](features-comparison.md), le istruzioni e i gruppi di istruzioni seguenti non sono supportati. Di conseguenza, se il database di cui deve essere eseguita la migrazione usa le funzionalità seguenti, è necessario riprogettare T-SQL in modo da eliminare queste funzionalità e istruzioni T-SQL.

- Regole di confronto di oggetti di sistema
- Connessione correlata: istruzioni di endpoint. Il database SQL non supporta l'autenticazione di Windows, ma supporta l'autenticazione Azure Active Directory simile. Alcuni tipi di autenticazione richiedono l'ultima versione di SQL Server Management Studio. Per altre informazioni, vedere [connessione al database SQL o ad Azure sinapsi Analytics (in precedenza SQL Data Warehouse) usando l'autenticazione di Azure Active Directory](authentication-aad-overview.md).
- Query tra database mediante nomi composti da tre o quattro parti. Le query tra database di sola lettura sono supportate mediante [query di database elastici](elastic-query-overview.md).
- Concatenamento della proprietà tra database, impostazione `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Usare invece 'EXECUTE AS USER'.
- La crittografia è supportata, ad eccezione della gestione delle chiavi estendibile
- Gestione degli eventi: notifiche degli eventi, notifiche di query
- Posizionamento dei file: sintassi relativa al posizionamento dei file di database, dimensioni e file di database che vengono gestiti automaticamente da Microsoft Azure.
- Disponibilità elevata: sintassi relativa a disponibilità elevata, gestita tramite l'account Microsoft Azure. Questa include la sintassi per backup, ripristino, AlwaysOn, mirroring del database, log shipping e modalità di ripristino.
- Lettura log: sintassi che si basa sulla lettura log, che non è disponibile nel database SQL: replica push, Change Data Capture. Il database SQL può essere iscritto a un articolo di replica push.
- Funzioni: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: sintassi delle impostazioni del server relative all'hardware: memoria, thread di lavoro, affinità di CPU, flag di traccia. Usare i livelli di servizio e le dimensioni di calcolo.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` e nomi in quattro parti
- .NET framework: integrazione CLR con SQL Server
- Ricerca semantica
- Credenziali del server: usare le [credenziali con ambito database](https://msdn.microsoft.com/library/mt270260.aspx).
- Elementi a livello di server: ruoli del server, `sys.login_token`. `GRANT`, `REVOKE` e `DENY` delle autorizzazioni a livello di server non sono disponibili anche se alcune sono sostituite dalle autorizzazioni a livello di database. Alcune viste a gestione dinamica a livello di server dispongono di una vista a gestione dinamica equivalente a livello di database.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opzioni `sp_configure` e `RECONFIGURE`. Con [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)sono disponibili alcune opzioni.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: sintassi che si basa su SQL Server Agent o sul database MSDB: avvisi, operatori, server di gestione centrale. Usare invece gli script, ad esempio Azure PowerShell.
- SQL Server audit: usare il controllo del database SQL.
- Traccia SQL Server
- Flag di traccia: alcuni elementi dei flag di traccia sono stati spostati in modalità di compatibilità.
- Debug di Transact-SQL
- Trigger: Con ambito Server o trigger di accesso
- Istruzione `USE`: per modificare il contesto del database in un database diverso è necessario creare una nuova connessione al nuovo database.

## <a name="full-transact-sql-reference"></a>Riferimento completo di Transact-SQL

Per ulteriori informazioni sulla grammatica, sull'utilizzo e sugli esempi di Transact-SQL, vedere Guida di [riferimento a Transact-SQL (motore di database)](https://msdn.microsoft.com/library/bb510741.aspx)   in documentazione online di SQL Server.

### <a name="about-the-applies-to-tags"></a>Informazioni sui tag "Si applica a"

Le informazioni di riferimento su Transact-SQL includono articoli correlati alle versioni di SQL Server dalla 2008 a quella attuale. Sotto il titolo dell'articolo è presente una barra delle icone, che elenca le quattro piattaforme SQL Server e indica l'applicabilità. Ad esempio, i gruppi di disponibilità sono stati introdotti in SQL Server 2012. L'articolo [creare un gruppo](https://msdn.microsoft.com/library/ff878399.aspx)   di disponibilità indica che l'istruzione si applica a **SQL Server (a partire da 2012)**. L'istruzione non si applica a SQL Server 2008, SQL Server 2008 R2, al database SQL di Azure, ad Azure sinapsi Analytics (in precedenza SQL Data Warehouse) o data warehouse paralleli.

In alcuni casi, l'oggetto generale di un articolo può essere usato in un prodotto, ma esistono differenze minime tra i prodotti. Le differenze sono indicate in punti centrali nell'articolo come appropriato. In alcuni casi, l'oggetto generale di un articolo può essere usato in un prodotto, ma esistono differenze minime tra i prodotti. Le differenze sono indicate in punti centrali nell'articolo come appropriato. Ad esempio, l'articolo creare TRIGGER è disponibile nel database SQL. Tuttavia, l'opzione **all Server** per i trigger a livello di server indica che i trigger a livello di server non possono essere utilizzati nel database SQL. Usare i trigger a livello di database.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco delle funzionalità supportate e non supportate dal database SQL, vedere  [Confronto delle funzionalità del database SQL di Azure](features-comparison.md). L'elenco in questa pagina integra le linee guida e l'articolo sulle funzionalità e si concentra sulle istruzioni Transact-SQL.
