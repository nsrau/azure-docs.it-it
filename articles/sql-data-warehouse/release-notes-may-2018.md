---
title: Note sulla versione di Azure SQL Data Warehouse - Maggio 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: c17cb13bff0ea9eb3b0bb2caf5bb527fa3958428
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852611"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Novità di Azure SQL Data Warehouse Maggio 2018 
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di maggio 2018. 

## <a name="gen-2-instances"></a>Istanze di seconda generazione
![alt](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Il livello di seconda generazione ottimizzato per le risorse di calcolo di Azure SQL Data Warehouse definisce nuovi standard di prestazioni per il data warehousing cloud. I clienti ottengono ora prestazioni di query fino a cinque volte migliori, concorrenza fino a quattro volte superiore e capacità di calcolo fino a cinque volte superiori rispetto alla generazione corrente. È ora in grado di gestire 128 query simultanee da un singolo cluster, la quantità più elevata rispetto a qualsiasi servizio di data warehousing cloud.

Vedere l'annuncio nel blog [Turbocharge cloud analytics with Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) (Accelerare le analisi cloud con Azure SQL Data Warehouse) di Rohan Kumar, Vicepresidente corporate, Azure Data.

## <a name="auto-statistics"></a>Statistiche automatiche
Le statistiche sono essenziali per l'ottimizzazione della generazione dei piani di query in utilità di ottimizzazione moderne basate sui costi, come il motore in SQL Data Warehouse. Quando tutte le query sono note in anticipo, la determinazione degli oggetti statistici da creare è un'attività facilmente realizzabile. Tuttavia, quando il sistema deve affrontare ad hoc e query casuale come avviene spesso per i carichi di lavoro di data warehousing, gli amministratori di sistema potrebbero farcela a stimare le statistiche dovranno essere creati iniziali per i piani di esecuzione di query potenzialmente non ottimali e più lungo tempi di risposta alle query. Un modo per attenuare il problema consiste nel creare in anticipo oggetti statistici in tutte le colonne della tabella. Il processo tuttavia prevede una penalità, perché gli oggetti statistici devono essere gestiti durante il processo di caricamento della tabella, provocando tempi di caricamento più lunghi.

SQL Data Warehouse supporta ora la creazione automatica di oggetti statistici, offrendo maggiore flessibilità, produttività e facilità d'uso per amministratori del sistema e sviluppatori, assicurando al tempo stesso che il sistema continui a offrire piani di esecuzione di qualità elevata e tempi di risposta ottimali.

Per abilitare o disabilitare la creazione automatica delle statistiche in SQL Data Warehouse, eseguire l'istruzione seguente:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Come procedura consigliata e indicazione, è consigliabile impostare l'opzione `AUTO_CREATE_STATISTICS` su `ON`.

> [!NOTE]
> La creazione automatica delle statistiche è *abilitata per impostazione predefinita* per tutti i nuovi data warehouse.
>  

Per altre informazioni, vedere l'articolo [Opzioni ALTER DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options).

## <a name="rejected-row-support"></a>Supporto per le righe rifiutate
I clienti usano spesso [PolyBase (tabelle esterne) per caricare i dati](design-elt-data-loading.md) in SQL Data Warehouse per le prestazioni elevate e la natura parallela del caricamento dei dati. PolyBase è il modello di caricamento predefinito per il caricamento di dati anche tramite [Azure Data Factory](https://azure.com/adf). 

SQL Data Warehouse aggiunge la possibilità di definire una posizione di riga rifiutata tramite il parametro `REJECTED_ROW_LOCATION` con l'istruzione [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql). Dopo l'esecuzione di [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) dalla tabella esterna, eventuali righe che non è stato possibile caricare verranno archiviate in un file vicino all'origine per indagini aggiuntive. 

Per altri dettagli sul comportamento relativo alle righe rifiutate, vedere il blog [Load confidently with SQL Data Warehouse PolyBase Rejected Row Location](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) (Caricamento in tutta sicurezza con la posizione di righe rifiutate PolyBase in SQL Data Warehouse).

L'esempio seguente mostra la nuova sintassi per specificare le righe rifiutate.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>ALTER VIEW
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) consente a un utente di modificare una vista creata in precedenza, senza dovere eseguire operazioni di tipo DELETE/CREATE per la vista e riapplicare le autorizzazioni. 

L'esempio seguente modifica una vista creata in precedenza.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
La funzione [CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) restituisce una stringa risultante dalla concatenazione di due o più valori in modo end-to-end. Separa i valori concatenati con il delimitatore specificato nel primo argomento. La funzione `CONCAT_WS` è utile per la generazione di output di tipo CSV (Comma-Separated Value).

L'esempio seguente mostra la concatenazione di un set di valori interi con una virgola.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
L'istruzione restituisce il risultato seguente:
```
result
---------
1,2,3
```
L'esempio seguente mostra la concatenazione di un set di valori di tipo misto con una virgola.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
L'istruzione restituisce il risultato seguente:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
La stored procedure di sistema [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) restituisce informazioni sui tipi di dati supportati dall'ambiente corrente. Viene in genere usata dagli strumenti che si connettono tramite connessioni ODBC per l'analisi del tipo di dati.

L'esempio seguente recupera i dettagli per tutti i tipi di dati supportati da SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>Modifica funzionale di SELECT INTO con ORDER BY
SQL Data Warehouse bloccherà ora le query `SELECT INTO` che contengono una clausola `ORDER BY`. Questa operazione aveva in precedenza esito positivo tramite prima di tutto l'ordinamento dei dati in memoria e quindi l'inserimento dei dati nella tabella di destinazione e il riordinamento dei dati in modo che corrispondano alla forma della tabella.

### <a name="previous-behavior"></a>Comportamento precedente
L'istruzione seguente ha esito positivo con overhead di elaborazione aggiuntivo.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Comportamento corrente
L'istruzione seguente genererà un errore che indica che la clausola `ORDER BY` non è supportata in un'istruzione `SELECT INTO`.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
L'istruzione con errore ha restituito quanto segue:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>Stato della query SET PARSEONLY ON (modifica funzionale)
L'uso della sintassi `SET PARSEONLY ON` consente a un utente di fare in modo che il motore di SQL Data Warehouse esamini la sintassi di ogni istruzione T-SQL e restituisca eventuali messaggi di errore senza compilare o eseguire l'istruzione. In precedenza nella vista di sistema [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) lo stato per queste istruzioni rimaneva `Running`. La vista `sys.dm_pdw_exec_requests` restituirà ora lo stato come `Complete`.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso alcune informazioni su SQL Data Warehouse, vedere come [creare un SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se non si ha familiarità con Azure, il [glossario di Azure][Azure glossary] può essere utile quando si incontrano termini nuovi. Oppure vedere alcune delle altre risorse disponibili per SQL Data Warehouse.  

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
