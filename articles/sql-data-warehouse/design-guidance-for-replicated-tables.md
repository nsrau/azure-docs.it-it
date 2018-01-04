---
title: Linee guida di progettazione per tabelle replicate - Azure SQL Data Warehouse | Microsoft Docs
description: Consigli per la progettazione di tabelle replicate nello schema Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ronortloff
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/23/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: 575b3c5710d744e99c6e02439577a362eb17c67e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Linee guida di progettazione per l'uso di tabelle replicate in Azure SQL Data Warehouse
Questo articolo offre alcuni consigli per la progettazione di tabelle replicate nello schema Azure SQL Data Warehouse. Usare questi consigli per migliorare le prestazioni delle query riducendo lo spostamento dei dati e la complessità delle query stesse.

> [!NOTE]
> La funzionalità delle tabelle replicate è attualmente disponibile in anteprima pubblica. Alcuni comportamenti sono soggetti a modifiche.
> 

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone una certa familiarità con i concetti di distribuzione e spostamento dei dati in SQL Data Warehouse.  Per altre informazioni, vedere l'articolo relativo all'[architettura](massively-parallel-processing-mpp-architecture.md). 

Come parte della progettazione di tabelle, è necessario comprendere quanto più possibile i propri dati e il modo in cui vengono eseguite query sui dati.  Ad esempio, considerare queste domande:

- Quali sono le dimensioni della tabella?   
- Quanto spesso viene aggiornata la tabella?   
- Sono presenti tabelle dei fatti e delle dimensioni in un data warehouse?   

## <a name="what-is-a-replicated-table"></a>Che cos'è una tabella replicata?
Una tabella replicata include una copia completa della tabella accessibile in ogni nodo di calcolo. La replica di una tabella elimina la necessità di trasferire dati tra i nodi di calcolo prima di un join o un'aggregazione. Poiché la tabella ha più copie, le tabelle replicate funzionano meglio quando le dimensioni delle tabelle sono inferiori a 2 GB, già compresse.

Il diagramma seguente mostra una tabella replicata accessibile in ogni nodo di calcolo. In SQL Data Warehouse la tabella replicata viene interamente copiata in un database di distribuzione in ogni nodo di calcolo. 

![Tabella replicata](media/guidance-for-using-replicated-tables/replicated-table.png "Tabella replicata")  

Le tabelle replicate sono più adatte per piccole tabelle delle dimensioni in uno schema star. Le tabelle delle dimensioni hanno in genere dimensioni tali da rendere possibile l'archiviazione e la conservazione di più copie. Nelle dimensioni sono archiviati dati descrittivi che cambiano raramente, come il nome e l'indirizzo di un cliente e i dettagli del prodotto. La rarità delle modifiche dei dati comporta un numero minore di ricompilazioni della tabella replicata. 

Provare a usare una tabella replicata nei casi seguenti:

- La dimensione della tabella su disco è inferiore a 2 GB, indipendentemente dal numero di righe. Per individuare la dimensione di una tabella, usare il comando [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql): `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- La tabella viene usata in join che richiederebbero altrimenti lo spostamento dei dati. Ad esempio, un join in tabelle con distribuzione hash richiede lo spostamento dei dati quando le colonne di join non si trovano nella stessa colonna di distribuzione. Se una delle tabelle con distribuzione hash ha dimensioni ridotte, provare una tabella replicata. Un join in una tabella round robin richiede lo spostamento dei dati. È consigliabile usare tabelle replicate invece di tabelle round robin nella maggior parte dei casi. 


Provare a convertire una tabella distribuita esistente in una tabella replicata nei casi seguenti:

- I piani di query usano operazioni di spostamento dei dati che trasmettono i dati a tutti i nodi di calcolo. L'operazione BroadcastMoveOperation è costosa e rallenta le prestazioni delle query. Per visualizzare le operazioni di spostamento dei dati nei piani di query, usare [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).
 
Le tabelle replicate possono essere causa di prestazioni delle query non ottimali nei casi seguenti:

- La tabella prevede frequenti operazioni di inserimento, aggiornamento ed eliminazione. Queste operazioni di Data Manipulation Language (DML) richiedono una ricompilazione della tabella replicata. La ricompilazione causa spesso un rallentamento delle prestazioni.
- Il data warehouse viene ridimensionato spesso. Il ridimensionamento di un data warehouse comporta la modifica del numero dei nodi di calcolo, che richiede una ricompilazione.
- La tabella include un numero elevato di colonne, ma le operazioni sui dati accedono in genere solo a una quantità ridotta di colonne. In questo scenario, invece di replicare l'intera tabella, può essere più efficace eseguire una distribuzione hash della tabella e quindi creare un indice per le colonne cui si accede di frequente. Quando una query richiede lo spostamento dei dati, SQL Data Warehouse sposta solo i dati presenti nelle colonne richieste. 



## <a name="use-replicated-tables-with-simple-query-predicates"></a>Usare tabelle replicate con predicati di query semplici
Prima di scegliere di distribuire o replicare una tabella, considerare i tipi di query che si prevede di eseguire sulla tabella. Se possibile:

- Usare tabelle replicate per query con predicati di query semplici, come le query di uguaglianza o disuguaglianza.
- Usare tabelle distribuite per query con predicati di query complessi, come LIKE o NOT LIKE.

Le query che richiedono un uso intensivo della CPU hanno prestazioni migliori quando il lavoro viene distribuito tra tutti i nodi di calcolo. Ad esempio, le query che eseguono calcoli in ogni riga di una tabella hanno prestazioni migliori nelle tabelle distribuite che non nelle tabelle replicate. Poiché una tabella replicata viene completamente archiviata in ogni nodo di calcolo, una query che richiede un uso intensivo di CPU su una tabella replicata viene eseguita sull'intera tabella in ogni nodo di calcolo. Il calcolo aggiuntivo può rallentare le prestazioni delle query.

Questa query, ad esempio, ha un predicato complesso.  Viene eseguita più rapidamente quando il fornitore è una tabella distribuita invece di una tabella replicata. In questo esempio il fornitore può avere una distribuzione hash o una distribuzione round robin.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Convertire le tabelle round robin esistenti in tabelle replicate
Se sono già presenti tabelle round robin, è consigliabile convertirle in tabelle replicate se soddisfano i criteri specificati in questo articolo. Le tabelle replicate migliorano le prestazioni rispetto alle tabelle round robin, perché eliminano la necessità di spostare i dati.  Una tabella round robin richiede lo spostamento dei dati per i join. 

Questo esempio usa [CTAS](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) per modificare la tabella DimSalesTerritory in una tabella replicata. Questo esempio funziona indipendentemente dal fatto che per DimSalesTerritory sia stata eseguita una distribuzione hash o round robin.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

--Create statistics on new table
CREATE STATISTICS [SalesTerritoryKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryKey]);
CREATE STATISTICS [SalesTerritoryAlternateKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryAlternateKey]);
CREATE STATISTICS [SalesTerritoryRegion] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryRegion]);
CREATE STATISTICS [SalesTerritoryCountry] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryCountry]);
CREATE STATISTICS [SalesTerritoryGroup] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryGroup]);

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Esempio di prestazioni delle query delle tabelle round robin rispetto alle tabelle replicate 

Una tabella replicata non richiede lo spostamento dei dati per i join, perché l'intera tabella è già presente in ogni nodo di calcolo. Se viene eseguita una distribuzione round robin delle tabelle delle dimensioni, un join copia interamente la tabella delle dimensioni in ogni nodo di calcolo. Per spostare i dati, il piano di query contiene un'operazione chiamata BroadcastMoveOperation. Questo tipo di operazione di spostamento dei dati rallenta le prestazioni delle query e viene eliminato usando tabelle replicate. Per visualizzare i passaggi del piano di query, usare la vista del catalogo di sistema [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql). 

Nella query seguente sullo schema AdventureWorks, ad esempio, la tabella ` FactInternetSales` è con distribuzione hash. Le tabelle `DimDate` e `DimSalesTerritory` sono tabelle delle dimensioni più piccole. Questa query restituisce le vendite totali in America del Nord per l'anno fiscale 2004:
 
```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
`DimDate` e `DimSalesTerritory` sono state ricreate come tabelle round robin. Di conseguenza, la query ha mostrato il piano di query seguente, che prevede più operazioni di spostamento di trasmissione: 
 
![Piano di query round robin](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

`DimDate` e `DimSalesTerritory` sono state ricreate come tabelle replicate e su di esse è stata eseguita una query. Il piano di query risultante è molto più breve e non include spostamenti di trasmissione.

![Piano di query di tabelle replicate](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Considerazioni sulle prestazioni per la modifica di tabelle replicate
SQL Data Warehouse implementa una tabella replicata mantenendo una versione master della tabella. Il servizio copia la versione master in un database di distribuzione in ogni nodo di calcolo. Quando viene apportata una modifica, SQL Data Warehouse aggiorna prima la tabella master. Richiede quindi una ricompilazione delle tabelle in ogni nodo di calcolo. Una ricompilazione di una tabella replicata include la copia della tabella in ogni nodo di calcolo e quindi la ricompilazione degli indici.

Le compilazioni sono necessarie dopo le operazioni seguenti:
- Vengono caricati o modificati dati
- Il data warehouse viene ridimensionato in base a un [livello di servizio](performance-tiers.md#service-levels) diverso
- Viene aggiornata la definizione di tabella

Le ricompilazioni non sono necessarie dopo le operazioni seguenti:
- Operazione di sospensione
- Operazione di ripresa

La ricompilazione non viene eseguita immediatamente dopo la modifica dei dati. Al contrario, la ricompilazione viene attivata la prima volta che una query esegue una selezione dalla tabella.  Nell'istruzione di selezione iniziale dalla tabella sono inclusi i passaggi per ricompilare la tabella replicata.  Poiché la ricompilazione viene eseguita all'interno della query, l'impatto dell'istruzione di selezione iniziale può essere significativo, a seconda delle dimensioni della tabella.  Se sono interessate più tabelle replicate che richiedono una ricompilazione, ogni copia viene ricompilata in serie come passaggi all'interno dell'istruzione.  Per mantenere la coerenza dei dati durante la ricompilazione della tabella replicata, viene applicato un blocco esclusivo alla tabella.  Il blocco impedisce qualsiasi accesso alla tabella durante la ricompilazione. 

### <a name="use-indexes-conservatively"></a>Usare gli indici con moderazione
Alle tabelle replicate si applicano le procedure di indicizzazione standard. SQL Data Warehouse ricompila ogni indice di tabella replicata come parte della ricompilazione. Usare gli indici solo quando le prestazioni sono più importanti del costo della ricompilazione degli indici.  
 
### <a name="batch-data-loads"></a>Caricare in batch i dati
Quando si caricano dati in tabelle replicate, provare a ridurre al minimo le ricompilazioni eseguendo i caricamenti in batch. Eseguire tutti i caricamenti in batch prima di eseguire istruzioni di selezione.

Ad esempio, questo modello di carico carica dati da quattro origini e richiama quattro ricompilazioni. 

- Caricamento dall'origine 1.
- L'istruzione di selezione attiva la ricompilazione 1.
- Caricamento dall'origine 2.
- L'istruzione di selezione attiva la ricompilazione 2.
- Caricamento dall'origine 3.
- L'istruzione di selezione attiva la ricompilazione 3.
- Caricamento dall'origine 4.
- L'istruzione di selezione attiva la ricompilazione 4.

Ad esempio, questo modello di carico carica dati da quattro origini, ma richiama una sola ricompilazione.

- Caricamento dall'origine 1.
- Caricamento dall'origine 2.
- Caricamento dall'origine 3.
- Caricamento dall'origine 4.
- L'istruzione di selezione attiva la ricompilazione.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Ricompilare una tabella replicata dopo un caricamento in batch
Per garantire tempi di esecuzione di query coerenti, è consigliabile forzare un aggiornamento delle tabelle replicate dopo un caricamento in batch. In caso contrario, la prima query deve attendere l'aggiornamento delle tabelle, che include la ricompilazione degli indici. A seconda delle dimensioni e del numero di tabelle replicate interessate, l'impatto sulle prestazioni può essere significativo.  

Questa query usa la DMV [sys.pdw_replicated_table_cache_state](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) per elencare le tabelle replicate che sono state modificate, ma non ricompilate.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Per forzare una ricompilazione, eseguire l'istruzione seguente in ogni tabella nell'output precedente. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Passaggi successivi 
Per creare una tabella replicata, usare una di queste istruzioni:

- [CREATE TABLE (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE AS SELECT (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Per una panoramica delle tabelle distribuite, vedere [Tabelle distribuite](sql-data-warehouse-tables-distribute.md).



