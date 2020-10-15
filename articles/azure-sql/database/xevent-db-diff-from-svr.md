---
title: Eventi estesi
description: Vengono descritti gli eventi estesi (XEvent) in Database SQL di Azure e come le sessioni di eventi sono leggermente diverse da sessioni di eventi in Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: c8f73c0789cd0211deeb66af5c7300a81d7b1be0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619815"
---
# <a name="extended-events-in-azure-sql-database"></a>Eventi estesi nel database SQL di Azure 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Il set di funzionalità degli eventi estesi nel database SQL di Azure è un subset solido delle funzionalità di SQL Server e Istanza gestita SQL di Azure.

*XEvent* è un nome alternativo informale utilizzato talvolta per "eventi estesi" in blog e altri percorsi informali.

Ulteriori informazioni sugli eventi estesi sono disponibili all'indirizzo:

- [Avvio rapido: Eventi estesi in SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Eventi estesi](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Prerequisiti

In questo argomento si presuppone che si dispone già di una conoscenza di:

- [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/)
- [Eventi estesi](/sql/relational-databases/extended-events/extended-events)

- La maggior parte della documentazione sugli eventi estesi si applica ai SQL Server, al database SQL di Azure e al Istanza gestita SQL di Azure.

Un’esposizione precedente a quanto riportato di seguito è utile quando si sceglie il file evento come [destinazione](#AzureXEventsTargets):

- [Servizio di Archiviazione di Azure](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell con archiviazione di Azure](/powershell/module/az.storage/)

## <a name="code-samples"></a>Esempi di codice

Gli argomenti correlati forniscono due esempi di codice:

- [Codice di destinazione del buffer circolare per eventi estesi nel database SQL di Azure](xevent-code-ring-buffer.md)

  - Breve script Transact-SQL semplice.
  - Nell'argomento dell'esempio di codice si evidenzia che, una volta completata la destinazione del buffer circolare, è necessario rilasciarne le risorse tramite l'esecuzione di un'istruzione `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` alter-drop. Successivamente è possibile aggiungere un'altra istanza del buffer circolare da `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.

- [Codice di destinazione del file evento per eventi estesi nel database SQL di Azure](xevent-code-event-file.md)

  - Fase 1 è PowerShell per creare un contenitore di Archiviazione di Azure.
  - Fase 2 è Transact-SQL che utilizza il contenitore di Archiviazione di Azure.

## <a name="transact-sql-differences"></a>Differenze di Transact-SQL

- Quando si esegue il comando [CREATE EVENT SESSION](/sql/t-sql/statements/create-event-session-transact-sql) su SQL Server, si utilizza la clausola **ON SERVER** . Tuttavia, nel database SQL di Azure si usa invece la clausola **on database** .
- La clausola **ON DATABASE** riguarda anche i comandi Transact-SQL [ALTER EVENT SESSION](/sql/t-sql/statements/alter-event-session-transact-sql) e [DROP EVENT SESSION](/sql/t-sql/statements/drop-event-session-transact-sql).

- Una procedura consigliata consiste nell'includere l'opzione della sessione eventi di **STARTUP_STATE = ON** nelle istruzioni **CREATE EVENT SESSION** o **ALTER EVENT SESSION**.
  - Il valore **= ON** supporta un riavvio automatico dopo una riconfigurazione del database logico a causa di un failover.

## <a name="new-catalog-views"></a>Nuove viste del catalogo

La funzionalità degli eventi estesi è supportata da diverse [viste del catalogo](https://msdn.microsoft.com/library/ms174365.aspx). Le viste del catalogo indicano i *metadati o le definizioni* di sessioni di eventi create dall'utente nel database corrente. Le viste non restituiscono informazioni sulle istanze delle sessioni di eventi attivi.

| Nome della<br/>vista del catalogo | Description |
|:--- |:--- |
| **sys.database_event_session_actions** |Restituisce una riga per ogni azione su ogni evento di una sessione dell'evento. |
| **sys.database_event_session_events** |Restituisce una riga per ogni evento in una sessione di eventi. |
| **sys.database_event_session_fields** |Restituisce una riga per ogni colonna personalizzabile impostata in modo esplicito su eventi e destinazioni. |
| **sys.database_event_session_targets** |Restituisce una riga per ogni destinazione di evento per una sessione eventi. |
| **sys.database_event_sessions** |Restituisce una riga per ogni sessione di eventi nel database. |

In Microsoft SQL Server le viste del catalogo simili hanno nomi che includono *.server\_* anziché *.database\_*. Il modello del nome è simile a **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>[DMV](https://msdn.microsoft.com/library/ms188754.aspx)

Il database SQL di Azure include [viste a gestione dinamica (DMV)](https://msdn.microsoft.com/library/bb677293.aspx) che supportano gli eventi estesi. Le DMV indicano le sessioni di eventi *attive* .

| Nome della DMV | Description |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Restituisce informazioni sulle azioni di sessione di evento. |
| **sys.dm_xe_database_session_events** |Restituisce informazioni sugli eventi di sessione. |
| **sys.dm_xe_database_session_object_columns** |Mostra i valori di configurazione per gli oggetti associati a una sessione. |
| **sys.dm_xe_database_session_targets** |Restituisce informazioni sulle destinazioni della sessione. |
| **sys.dm_xe_database_sessions** |Restituisce una riga per ogni sessione di eventi con ambito nel database corrente. |

In Microsoft SQL Server, le viste del catalogo simili sono denominate senza la parte del nome del * \_ database* , ad esempio:

- **sys.dm_xe_sessions**, anziché il nome<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMV comuni a entrambi

Per gli eventi estesi sono disponibili DMV aggiuntivi comuni al database SQL di Azure, Istanza gestita di Azure SQL e Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Trovare gli eventi estesi, le azioni e le destinazioni disponibili

È possibile eseguire un semplice **SELECT** di SQL per ottenere un elenco di eventi, azioni e destinazioni disponibili.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Destinazioni per le sessioni di eventi del database SQL di Azure

Di seguito sono riportate le destinazioni che consentono di acquisire i risultati delle sessioni di eventi nel database SQL di Azure:

- [Destinazione buffer circolare](https://msdn.microsoft.com/library/ff878182.aspx) : contiene per un tempo breve i dati degli eventi nella memoria.
- [Destinazione contatore eventi](https://msdn.microsoft.com/library/ff878025.aspx) : conta tutti gli eventi che si verificano durante una sessione di eventi estesi.
- [Destinazione file evento](https://msdn.microsoft.com/library/ff878115.aspx) : scrive buffer completi in un contenitore di Archiviazione di Azure.

L'API [Event Tracing for Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) non è disponibile per gli eventi estesi nel database SQL di Azure.

## <a name="restrictions"></a>Restrizioni

Esistono un paio di differenze relative alla sicurezza che si comportano nell'ambiente cloud del database SQL di Azure:

- Gli eventi estesi si basano sul modello di isolamento single-tenant. Una sessione di eventi in un database non può accedere a dati o eventi da un altro database.
- Non è possibile emettere un'istruzione **CREATE EVENT SESSION** nel contesto del database **master**.

## <a name="permission-model"></a>Modello di autorizzazione

È necessario avere l'autorizzazione **Controllo** nel database per emettere un'istruzione **CREATE EVENT SESSION**. Il proprietario del database (dbo) dispone dell’autorizzazione **controllo** .

### <a name="storage-container-authorizations"></a>Autorizzazioni del contenitore di archiviazione

Il token della firma di accesso condiviso generato per il contenitore di Archiviazione di Azure deve specificare **rwl** per le autorizzazioni. Questo valore **rwl** fornisce le autorizzazioni seguenti:

- Lettura
- Scrittura
- Elenco

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Esistono scenari in cui un uso intensivo di eventi estesi può accumulare più memoria attiva di quanto è adatto per l'intero sistema. Quindi, il database SQL di Azure imposta e regola in modo dinamico i limiti sulla quantità di memoria attiva che può essere accumulata da una sessione eventi. Molti fattori vengono utilizzati nel calcolo dinamico.

Se si riceve un messaggio di errore che indica che è stato applicato un massimo di memoria, alcune azioni correttive da eseguire sono:

- Eseguire meno sessioni di eventi simultanee.
- Tramite le istruzioni **CREATE** e **ALTER** per le sessioni di eventi, ridurre la quantità di memoria specificata nella clausola **MAX\_MEMORY**.

### <a name="network-latency"></a>Latenza di rete

La destinazione del **file evento** potrebbe subire una latenza di rete o errori durante il mantenimento dei dati nei BLOB di archiviazione di Azure . Altri eventi nel database SQL di Azure potrebbero essere ritardati durante l'attesa del completamento della comunicazione di rete. Questo ritardo può rallentare il carico di lavoro.

- Per ridurre questo rischio delle prestazioni, evitare di impostare l'opzione **EVENT_RETENTION_MODE** su **NO_EVENT_LOSS** nelle definizioni della sessione di eventi.

## <a name="related-links"></a>Collegamenti correlati

- [Uso di Azure PowerShell con archiviazione di Azure](/powershell/module/az.storage/).
- [Cmdlet di Archiviazione di Azure](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Uso di Azure PowerShell con Archiviazione di Azure](/powershell/module/az.storage/)
- [Come usare l'archiviazione BLOB da .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Post del blog di Jonathan Kehayias sugli eventi estesi in Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- La pagina Web *Aggiornamenti di Azure*, con visualizzazione limitata dal parametro ai soli aggiornamenti relativi al database SQL di Azure:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
