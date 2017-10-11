---
title: Creare chiavi surrogate con IDENTITY | Microsoft Docs
description: Informazioni su come usare IDENTITY per creare chiavi surrogate per le tabelle.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/13/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3ab5d159e6eaeb830135fe134e108b0e4de4b7d6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>Creare chiavi surrogate con IDENTITY
> [!div class="op_single_selector"]
> * [Panoramica][Overview]
> * [Tipi di dati][Data Types]
> * [Distribuzione][Distribute]
> * [Indice][Index]
> * [Partizione][Partition]
> * [Statistiche][Statistics]
> * [Temporanee][Temporary]
> * [Identità][Identity]
> 
> 

Molti progettisti di modelli di dati preferiscono creare chiavi surrogate sulle tabelle durante la progettazione dei modelli per i data warehouse. È possibile usare la proprietà IDENTITY per raggiungere questo obiettivo in modo semplice ed efficace senza effetti sulle prestazioni di caricamento. 

## <a name="get-started-with-identity"></a>Introduzione a IDENTITY
È possibile definire una tabella con la proprietà IDENTITY al momento della creazione, usando una sintassi simile all'istruzione seguente:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

È quindi possibile usare `INSERT..SELECT` per popolare la tabella.

## <a name="behavior"></a>Comportamento
La proprietà IDENTITY è progettata per supportare la scalabilità orizzontale tra tutte le distribuzioni nel data warehouse senza influenzare le prestazioni di caricamento. Pertanto, l'implementazione di IDENTITY è orientata al raggiungimento di questi obiettivi. Questa sezione illustra le varie sfumature dell'implementazione per comprenderle appieno.  

### <a name="allocation-of-values"></a>Allocazione dei valori
La proprietà IDENTITY non garantisce l'ordine di allocazione dei valori surrogati, in modo conforme al comportamento di SQL Server e del database SQL di Azure. Tuttavia, in Azure SQL Data Warehouse, l'assenza di una garanzia è più evidente. 

L'esempio seguente è una dimostrazione:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

Nell'esempio precedente due righe raggiungono la distribuzione 1. La prima riga ha il valore surrogato 1 nella colonna `C1` e la seconda riga ha il valore surrogato 61. Entrambi questi valori sono stati generati dalla proprietà IDENTITY. L'allocazione dei valori non è tuttavia contigua. Questo comportamento dipende dalla progettazione.

### <a name="skewed-data"></a>Dati asimmetrici 
L'intervallo di valori per il tipo di dati è distribuito uniformemente tra le distribuzioni. Nel caso di una tabella distribuita con dati asimmetrici, l'intervallo di valori disponibili per il tipo di dati può esaurirsi in modo anomalo. Ad esempio, se tutti i dati vengono destinati a una singola distribuzione, la tabella ha effettivamente accesso solo a un sessantesimo dei valori del tipo di dati. Per questo motivo, la proprietà IDENTITY è limitata solo ai tipi di dati `INT` e `BIGINT`.

### <a name="selectinto"></a>SELECT..INTO
Quando una colonna IDENTITY esistente viene selezionata in una nuova tabella, la nuova colonna eredita la proprietà IDENTITY, a meno che non sia vera una delle condizioni seguenti:
- L'istruzione SELECT contiene un join.
- Più istruzioni SELECT sono unite in join tramite l'istruzione UNION.
- La colonna IDENTITY è elencata più di una volta nell'elenco SELECT.
- La colonna IDENTITY fa parte di un'espressione.
    
Se una di queste condizioni è vera, la colonna viene creata come NOT NULL invece di ereditare la proprietà IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT
CREATE TABLE AS SELECT (CTAS) ha lo stesso comportamento di SQL Server documentato per SELECT... INTO. Tuttavia, non è possibile specificare una proprietà IDENTITY nella definizione di colonna della parte `CREATE TABLE` dell'istruzione. Non è possibile nemmeno usare la funzione IDENTITY nella parte `SELECT` dell'istruzione CTAS. Per popolare una tabella, è necessario usare l'istruzione `CREATE TABLE` per definire la tabella, seguita da `INSERT..SELECT` per popolarla.

## <a name="explicitly-insert-values-into-an-identity-column"></a>Inserire in modo esplicito valori in una colonna IDENTITY 
SQL Data Warehouse supporta la sintassi `SET IDENTITY_INSERT <your table> ON|OFF`. È possibile usare questa sintassi per inserire in modo esplicito i valori nella colonna IDENTITY.

Molti progettisti di modelli di dati preferiscono usare valori negativi predefiniti per alcune righe nelle dimensioni. Un esempio è la riga -1 o "membro sconosciuto". 

Il prossimo script mostra come aggiungere in modo esplicito questa riga tramite SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>Caricare i dati in una tabella con IDENTITY

La presenza della proprietà IDENTITY ha alcune implicazioni per il codice di caricamento dei dati. Questa sezione evidenzia alcuni modelli di base per il caricamento di dati nelle tabelle usando IDENTITY. 

### <a name="load-data-with-polybase"></a>Caricare dati con PolyBase
Per caricare dati in una tabella e generare una chiave surrogata con IDENTITY, creare la tabella e quindi usare INSERT... SELECT o INSERT... VALUES per eseguire il caricamento.

L'esempio seguente illustra il modello di base:
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Non è attualmente possibile usare `CREATE TABLE AS SELECT` per il caricamento di dati in una tabella con una colonna IDENTITY.
> 

Per altre informazioni sul caricamento dei dati tramite lo strumento BCP (Bulk Copy Program), vedere gli articoli seguenti:

- [Caricare dati con PolyBase][]
- [Procedure consigliate per PolyBase][]

### <a name="load-data-with-bcp"></a>Caricare dati con BCP
BCP è uno strumento da riga di comando utilizzabile per caricare dati in SQL Data Warehouse. Uno dei parametri di questo strumento (-E) controlla il comportamento di BCP quando si caricano dati in una tabella con una colonna IDENTITY. 

Quando viene specificato il parametro -E, vengono mantenuti i valori contenuti nel file di input per la colonna con IDENTITY. Se -E *non* viene specificato, i valori in questa colonna vengono ignorati. Se la colonna IDENTITY non è inclusa, i dati vengono caricati come di consueto. I valori vengono generati in base al criterio di incremento e seeding della proprietà.

Per altre informazioni sul caricamento di dati con BCP, vedere gli articoli seguenti:

- [Caricare dati con BCP][]
- [BCP in MSDN][]

## <a name="catalog-views"></a>Viste del catalogo
SQL Data Warehouse supporta la vista del catalogo `sys.identity_columns`. Questa vista è utilizzabile per identificare una colonna con la proprietà IDENTITY.

Per comprendere meglio lo schema del database, questo esempio mostra come integrare `sys.identity_columns` con altre viste del catalogo di sistema:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limitazioni
La proprietà IDENTITY non può essere usata negli scenari seguenti:
- Quando il tipo di dati della colonna non è INT o BIGINT
- Quando la colonna è anche la chiave di distribuzione
- Quando la tabella è una tabella esterna 

Le funzioni correlate seguenti non sono supportate in SQL Data Warehouse:

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>Attività

Questa sezione include esempi di codice utilizzabili per eseguire attività comuni quando si usano colonne IDENTITY.

> [!NOTE] 
> La colonna C1 è la colonna IDENTITY in tutte le attività seguenti.
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Individuare il valore massimo allocato per una tabella
Usare la funzione `MAX()` per determinare il valore massimo allocato per una tabella distribuita:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Trovare il valore di seeding e incremento per la proprietà IDENTITY
È possibile usare le viste del catalogo per individuare i valori di configurazione di incremento e seeding di IDENTITY per una tabella usando la query seguente: 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sullo sviluppo di tabelle, vedere [Panoramica delle tabelle][Overview], [Tipi di dati per le tabelle][Data Types], [Distribuire una tabella][Distribute], [Indicizzare una tabella][Index], [Partizionare una tabella][Partition] e [Tabelle temporanee][Temporary]. 
* Per altre informazioni sulle procedure consigliate, vedere [Procedure consigliate per SQL Data Warehouse][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[Caricare dati con BCP]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[Caricare dati con PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[Procedure consigliate per PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[bcp in MSDN]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  
