---
title: Ottimizzazione delle prestazioni con indice columnstore cluster ordinato
description: Consigli e considerazioni da conoscere quando si usa l'indice columnstore cluster ordinato per migliorare le prestazioni delle query.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 2113e5ac3563a22c5f2c6b755230b05fb9a2cb35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583864"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ottimizzazione delle prestazioni con indice columnstore cluster ordinato  

Quando gli utenti eseguono una query su una tabella columnstore nel pool SQL Synapse, l'ottimizzatore controlla i valori minimo e massimo archiviati in ogni segmento.  I segmenti che si trovano all'esterno dei limiti del predicato di query non vengono letti dal disco alla memoria.  Una query può ottenere prestazioni più veloci se il numero di segmenti da leggere e le dimensioni totali sono ridotte.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Indice columnstore cluster ordinato e non ordinatoOrdered vs.

Per impostazione predefinita, per ogni tabella creata senza un'opzione di indice, un componente interno (generatore di indici) crea un indice CCI (Clustered columnstore) non ordinato.  I dati in ogni colonna vengono compressi in un segmento Rowgroup CCI separato.  Sono disponibili metadati sull'intervallo di valori di ogni segmento, pertanto i segmenti che non rientrano nei limiti del predicato di query non vengono letti dal disco durante l'esecuzione della query.  CCI offre il massimo livello di compressione dei dati e riduce le dimensioni dei segmenti da leggere in modo che le query possano essere eseguite più velocemente. Tuttavia, poiché il generatore di indici non ordina i dati prima di comprimerli in segmenti, potrebbero verificarsi segmenti con intervalli di valori sovrapposti, facendo sì che le query leggano più segmenti dal disco e richiedono più tempo per il completamento.  

Quando si crea un CCI ordinato, il motore SQL Synapse ordina i dati esistenti in base alle chiavi di ordine prima che il generatore di indici li comprima in segmenti di indice.  Con i dati ordinati, la sovrapposizione dei segmenti è ridotta consentendo alle query di avere un'eliminazione dei segmenti più efficiente e quindi prestazioni più veloci perché il numero di segmenti da leggere dal disco è inferiore.  Se tutti i dati possono essere ordinati in memoria contemporaneamente, è possibile evitare la sovrapposizione dei segmenti.  A causa di tabelle di grandi dimensioni nei data warehouse, questo scenario non si verifica spesso.  

Per controllare gli intervalli di segmenti per una colonna, eseguire questo comando con il nome della tabella e il nome della colonna:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> In una tabella CCI ordinata, i nuovi dati risultanti dallo stesso batch di operazioni DML o di caricamento dei dati vengono ordinati all'interno di tale batch, non esiste alcun ordinamento globale tra tutti i dati nella tabella.  Gli utenti possono REBUILD il CCI ordinato per ordinare tutti i dati nella tabella.  In Synapse SQL, l'indice columnstore REBUILD è un'operazione offline.  Per una tabella partizionata, il REBUILD viene eseguito una partizione alla volta.  I dati nella partizione che viene ricompilata sono "offline" e non sono disponibili fino al completamento di REBUILD per tale partizione. 

## <a name="query-performance"></a>Prestazioni delle query

Il miglioramento delle prestazioni di una query da un CCI ordinato dipende dai modelli di query, dalle dimensioni dei dati, dalla capacità di ordinamento dei dati, dalla struttura fisica dei segmenti e dalla Classe di risorse e DWU scelta per l'esecuzione della query.  Gli utenti devono esaminare tutti questi fattori prima di scegliere le colonne di ordinamento durante la progettazione di una tabella CCI ordinata.

Le query con tutti questi modelli vengono in genere eseguite più velocemente con CCI ordinato.  
1. Le query hanno predicati di uguaglianza, disuguaglianza o intervallo
1. Le colonne predicato e le colonne CCI ordinate sono uguali.  
1. Le colonne predicato vengono utilizzate nello stesso ordine dell'ordinale di colonna delle colonne CCI ordinate.  
 
In questo esempio, la tabella T1 ha un indice columnstore cluster ordinato nella sequenza di Col_C, Col_B e Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Le prestazioni della query 1 possono trarre maggiori vantaggi dal CCI ordinato rispetto alle altre tre query. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Prestazioni di caricamento dei dati

Le prestazioni del caricamento dei dati in una tabella CCI ordinata sono simili a quelle di una tabella partizionata.  Il caricamento dei dati in una tabella CCI ordinata può richiedere più tempo di una tabella CCI non ordinata a causa dell'operazione di ordinamento dei dati, tuttavia le query possono essere eseguite più velocemente in seguito con CCI ordinato.  

Di seguito è riportato un esempio di confronto delle prestazioni del caricamento di dati in tabelle con schemi diversi.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Di seguito è riportato un esempio di confronto delle prestazioni delle query tra CCI e CCI ordinato.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Ridurre la sovrapposizione dei segmenti

Il numero di segmenti sovrapposti dipende dalla dimensione dei dati da ordinare, dalla memoria disponibile e dall'impostazione del grado massimo di parallelismo (MAXDOP) durante la creazione di CCI ordinato. Di seguito sono riportate le opzioni per ridurre la sovrapposizione dei segmenti durante la creazione di CCI ordinato.

- Usare la classe di risorse xlargerc su una DWU superiore per consentire una maggiore quantità di memoria per l'ordinamento dei dati prima che il generatore di indici comprima i dati in segmenti.  Una volta in un segmento di indice, la posizione fisica dei dati non può essere modificata.  Non è disponibile alcun ordinamento dei dati all'interno di un segmento o tra segmenti.  

- Creare CCI ordinato con MAXDOP 1.  Ogni thread utilizzato per la creazione di CCI ordinato lavora su un subset di dati e lo ordina localmente.  Non esiste un ordinamento globale tra i dati ordinati in base a thread diversi.  L'uso di thread paralleli può ridurre il tempo necessario per creare un CCI ordinato, ma genererà più segmenti sovrapposti rispetto all'utilizzo di un singolo thread.  Attualmente, l'opzione MAXDOP è supportata solo nella creazione di una tabella CCI ordinata utilizzando il comando CREATE TABLE AS SELECT.  La creazione di un CCI ordinato tramite i comandi CREATE INDEX o CREATE TABLE non supporta l'opzione MAXDOP. Ad esempio,

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Preordinare i dati in base alle chiavi di ordinamento prima di caricarli nelle tabelle.

Ecco un esempio di una distribuzione di tabella CCI ordinata con zero segmenti sovrapposti seguendo le raccomandazioni precedenti. La tabella CCI ordinata viene creata in un database DWU1000c tramite CTAS da una tabella heap da 20 GB utilizzando MAXDOP 1 e xlargerc.  Il CCI viene ordinato in una colonna BIGINT senza duplicati.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Creare CCI ordinato su tabelle di grandi dimensioni

La creazione di un CCI ordinato è un'operazione offline.  Per le tabelle senza partizioni, i dati non saranno accessibili agli utenti fino al completamento del processo di creazione CCI ordinato.   Per le tabelle partizionate, poiché il motore crea la partizione CCI ordinata per partizione, gli utenti possono comunque accedere ai dati nelle partizioni in cui la creazione di CCI ordinato non è in corso.   È possibile utilizzare questa opzione per ridurre al minimo il tempo di inattività durante la creazione di CCI ordinato su tabelle di grandi dimensioni:You can use this option to minimize the downtime during ordered CCI creation on large tables: 

1.    Creare partizioni nella tabella di grandi dimensioni di destinazione (denominata Table_A).
2.    Creare una tabella CCI ordinata vuota (denominata Table_B) con lo stesso schema di tabella e di partizione della tabella A.Create an empty ordered CCI table (called Table_B) with the same table and partition schema as Table A.
3.    Passare da una partizione dalla tabella A alla tabella B.
4.    Eseguire alter INDEX <Ordered_CCI_Index> <Table_B <> REBUILD PARTITION - <Partition_ID> nella tabella B per ricostruire la partizione a commutazione di commutazione.  
5.    Ripetere i passaggi 3 e 4 per ogni partizione in Table_A.
6.    Una volta che tutte le partizioni sono passate da Table_A a Table_B e sono state ricompilate, rilasciare Table_A e rinominare Table_B in Table_A. 

## <a name="examples"></a>Esempi

**A. Per verificare la presenza di colonne ordinate e ordinali:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Per modificare l'ordinale delle colonne, aggiungere o rimuovere colonne dall'elenco degli ordini o passare da CCI a CCI ordinato:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).
