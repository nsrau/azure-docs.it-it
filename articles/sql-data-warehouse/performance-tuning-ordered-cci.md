---
title: Ottimizzazione delle prestazioni con Azure SQL Data Warehouse indice columnstore cluster ordinato | Microsoft Docs
description: Raccomandazioni e considerazioni che è necessario tenere presente quando si usa l'indice columnstore cluster ordinato per migliorare le prestazioni delle query.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 74a1a2218020718a05c9d01de96ddf4fccb35eb4
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802574"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ottimizzazione delle prestazioni con indice columnstore cluster ordinato  

Quando gli utenti eseguono una query su una tabella columnstore in Azure SQL Data Warehouse, l'utilità di ottimizzazione controlla i valori minimo e massimo archiviati in ogni segmento.  I segmenti che non rientrano nei limiti del predicato della query non vengono letti dal disco alla memoria.  Una query può ottenere prestazioni più veloci se il numero di segmenti da leggere e le dimensioni totali sono ridotte.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Confronto tra indice columnstore cluster ordinato e non ordinato 
Per impostazione predefinita, per ogni tabella data warehouse di Azure creata senza un'opzione di indice, un componente interno (Generatore di indici) crea un indice columnstore cluster non ordinato (CCI).  I dati in ogni colonna vengono compressi in un segmento CCI rowgroup separato.  Sono presenti metadati nell'intervallo di valori di ogni segmento, quindi i segmenti che non rientrano nei limiti del predicato della query non vengono letti dal disco durante l'esecuzione della query.  CCI offre il massimo livello di compressione dei dati e riduce le dimensioni dei segmenti da leggere, in modo che le query possano essere eseguite più velocemente. Tuttavia, poiché il generatore di indici non Ordina i dati prima di comprimerli in segmenti, è possibile che si verifichino segmenti con intervalli di valori sovrapposti, facendo in modo che le query leggano più segmenti dal disco e imprendano più tempo.  

Quando si crea una CCI ordinata, il motore di Azure SQL Data Warehouse Ordina i dati in memoria in base alle chiavi degli ordini prima che il generatore di indici lo comprime in segmenti di indice.  Con i dati ordinati, la sovrapposizione dei segmenti è ridotta, consentendo alle query di avere un'eliminazione più efficiente del segmento e quindi prestazioni più veloci perché il numero di segmenti da leggere dal disco è inferiore.  Se tutti i dati possono essere ordinati in memoria contemporaneamente, è possibile evitare la sovrapposizione del segmento.  Date le grandi dimensioni dei dati nelle tabelle data warehouse, questo scenario non si verifica spesso.  

Per verificare gli intervalli di segmenti per una colonna, eseguire questo comando con il nome della tabella e il nome della colonna:

```sql
SELECT o.name, pnp.index_id, cls.row_count, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table_Name>' and cols.name = '<Column_Name>' 
ORDER BY o.name, pnp.distribution_id, cls.min_data_id

```

## <a name="data-loading-performance"></a>Prestazioni di caricamento dei dati

Le prestazioni del caricamento dei dati in una tabella CCI ordinata sono simili al caricamento dei dati in una tabella partizionata.  
Il caricamento dei dati in una tabella CCI ordinata può richiedere più tempo rispetto al caricamento dei dati in una tabella CCI non ordinata a causa dell'ordinamento dei dati.  

Di seguito è riportato un esempio di confronto delle prestazioni di caricamento dei dati nelle tabelle con schemi diversi.
![Performance_comparison_data_loading @ no__t-1
 
## <a name="reduce-segment-overlapping"></a>Riduzione della sovrapposizione del segmento
Di seguito sono riportate le opzioni per ridurre ulteriormente la sovrapposizione dei segmenti quando si crea un'CCI ordinata in una nuova tabella tramite CTAS o in una tabella esistente con dati:

- Usare una classe di risorse più grande per consentire l'ordinamento di più dati in una sola volta in memoria prima che il generatore di indici li comprime in segmenti.  Una volta in un segmento di indice, la posizione fisica dei dati non può essere modificata.  Nessun ordinamento dei dati in un segmento o in più segmenti.  

- Usare un grado di parallelismo inferiore (ad esempio, DOP = 1).  Ogni thread usato per la creazione di CCI ordinati funziona su un subset di dati e lo ordina localmente.  Non esiste alcun ordinamento globale tra i dati ordinati in base a thread diversi.  L'uso di thread paralleli può ridurre il tempo necessario per creare un'CCI ordinata, ma genererà più segmenti sovrapposti rispetto all'uso di un singolo thread. 
- Pre-ordinare i dati in base alle chiavi di ordinamento prima di caricarli in tabelle Azure SQL Data Warehouse.

## <a name="create-ordered-cci-on-large-tables"></a>Creare CCI ordinati in tabelle di grandi dimensioni
La creazione di una CCI ordinata è un'operazione offline.  Per le tabelle senza partizioni, i dati non saranno accessibili agli utenti fino al completamento del processo di creazione di CCI ordinato.   Per le tabelle partizionate, poiché il motore crea la partizione CCI ordinata per partizione, gli utenti possono comunque accedere ai dati nelle partizioni in cui la creazione di CCI ordinata non è in corso.   È possibile utilizzare questa opzione per ridurre al minimo il tempo di inattività durante la creazione di CCI ordinata su tabelle di grandi dimensioni: 

1.  Creare partizioni nella tabella di grandi dimensioni di destinazione (denominata tabella A).
2.  Creare una tabella CCI ordinata vuota (denominata Table B) con la stessa tabella e lo stesso schema di partizione della tabella A.
3.  Passare una partizione dalla tabella A alla tabella B.
4.  Eseguire ALTER INDEX < Ordered_CCI_Index > Rebuild nella tabella B per ricompilare la partizione commutata.  
5.  Ripetere i passaggi 3 e 4 per ogni partizione nella tabella A.
6.  Una volta passate tutte le partizioni dalla tabella A alla tabella B e ricompilate, eliminare la tabella A e rinominare la tabella B nella tabella A. 

## <a name="examples"></a>Esempi

**A. Per verificare la presenza di colonne ordinate e ordinale:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Per modificare il numero ordinale di colonna, aggiungere o rimuovere colonne dall'elenco degli ordini oppure per passare da CCI a ordered CCI:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse](sql-data-warehouse-overview-develop.md).
