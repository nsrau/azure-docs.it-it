---
title: Note sulla versione di Azure SQL Data Warehouse - Giugno 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4348a634fd5b2b33f36d8e79f28caf659b82ccf4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "67626144"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Novità di Azure SQL Data Warehouse Giugno 2018
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di giugno 2018. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Punti di ripristino definiti dall'utente
SQL Data Warehouse acquisisce automaticamente gli snapshot del data warehouse ogni 8 ore garantendo un obiettivo del punto di ripristino (RPO) di otto ore. Sebbene questi snapshot automatizzati facilitino gli oneri di gestione dell'esecuzione del data warehouse, è necessario creare snapshot nei momenti critici in base alle esigenze aziendali. Ad esempio, l'esecuzione di uno snapshot subito prima della distribuzione di nuovi script o un carico di dati significativi nel data warehouse per abilitare un punto di ripristino subito prima dell'operazione. 

SQL Data Warehouse supporta ora i [punti di ripristino definiti dall'utente](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) tramite il cmdlet [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) .

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Sicurezza a livello di colonna
Gestire l'accesso ai dati e la sicurezza nel data warehouse è fondamentale per creare fiducia con clienti e partner. SQL Data Warehouse [supporta adesso la sicurezza a livello di colonna (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) che consente di modificare le autorizzazioni per visualizzare i dati sensibili limitando l'accesso utente a colonne specifiche all'interno delle tabelle, senza dover riprogettare il data warehouse.

La sicurezza a livello di colonna (CLS) consente ai clienti di controllare l'accesso alle colonne della tabella in base al contesto di esecuzione o dell'appartenenza ai gruppi dell'utente tramite istruzioni T-SQL [GRANT](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) standard. La logica di restrizione dell'accesso si trova nel livello database e non lontano dai dati in un altro livello applicazione, semplificando l'implementazione complessiva della sicurezza.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="object_schema_name"></a>OBJECT_SCHEMA_NAME
La funzione [OBJECT_SCHEMA_NAME()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql)restituisce il nome dello schema di database per gli oggetti con ambito schema. Questa funzione è diventata comune negli strumenti ETL quando si esegue la convalida dello schema dell'oggetto. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Risultati di esempio**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systime_zone_info-view"></a>Supporto per la vista sys.time_zone_info
La vista [Sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) restituisce informazioni sui fusi orari supportati in Azure SQL Data Warehouse.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Risultati di esempio**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdm_pdw_exec_requests-behavior-change"></a>Le operazioni di creazione di statistiche automatiche verranno visualizzate in sys.dm_pdw_exec_requests (modifica funzionale)

Con l'introduzione delle [operazioni di creazione di statistiche automatiche](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic), Azure SQL Data Warehouse genera statistiche per ottimizzare l'esecuzione di query. La versione di giugno 2018 offre la possibilità di monitorare il momento in cui le statistiche vengono generate automaticamente mediante l'aggiunta di un record nella vista [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) ogni volta che viene eseguita l'operazione [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql).

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**Risultati di esempio**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come SQL Data Warehouse, viene illustrato come [creare rapidamente una SQL data warehouse][create a SQL Data Warehouse]. Se non si ha familiarità con Azure, il [glossario di Azure][Azure glossary] può essere utile quando si incontrano termini nuovi. Oppure vedere alcune delle altre risorse disponibili per SQL Data Warehouse.  

* [Casi di successo dei clienti]
* [Blog]
* [Richieste di funzionalità]
* [Video]
* [Blog Customer Advisory Team]
* [Forum su Stack Overflow]
* [Twitter]


[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Casi di successo dei clienti]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum su Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
