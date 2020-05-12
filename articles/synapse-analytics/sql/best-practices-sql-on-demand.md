---
title: Procedure consigliate per SQL su richiesta (anteprima) in Azure sinapsi Analytics
description: Suggerimenti e procedure consigliate che è opportuno tenere presente durante l'utilizzo di SQL su richiesta (anteprima).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a1a33404982b16e458e97aaf9959ff5dd52d1cce
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198891"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Procedure consigliate per SQL su richiesta (anteprima) in Azure sinapsi Analytics

In questo articolo è presente una raccolta di procedure consigliate per l'uso di SQL su richiesta (anteprima). SQL su richiesta è una risorsa aggiuntiva in Azure sinapsi Analytics.

## <a name="general-considerations"></a>Considerazioni generali

SQL su richiesta consente di eseguire query sui file negli account di archiviazione di Azure. Non dispone di funzionalità di archiviazione locale o di inserimento. Di conseguenza, tutti i file di destinazione della query sono esterni a SQL su richiesta. Tutti gli elementi correlati alla lettura di file dall'archiviazione potrebbero avere un effetto sulle prestazioni di esecuzione delle query.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Trovare un account di archiviazione di Azure e SQL su richiesta

Per ridurre al minimo la latenza, colocare l'account di archiviazione di Azure e l'endpoint SQL su richiesta. Gli account di archiviazione e gli endpoint sottoposti a provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per ottenere prestazioni ottimali, se si accede ad altri account di archiviazione con SQL su richiesta, assicurarsi che si trovino nella stessa area. Se non si trovano nella stessa area, si verifica un aumento della latenza per il trasferimento di rete dei dati tra le aree remote e l'endpoint.

## <a name="azure-storage-throttling"></a>Limitazione di archiviazione di Azure

Più applicazioni e servizi possono accedere all'account di archiviazione. La limitazione dell'archiviazione si verifica quando le operazioni di i/o al secondo e la velocità effettiva combinate generate da applicazioni, servizi e carichi di lavoro SQL su richiesta superano i limiti dell'account di archiviazione. Di conseguenza, si verificherà un effetto negativo significativo sulle prestazioni di esecuzione delle query.

Una volta rilevata la limitazione, SQL su richiesta dispone di una gestione incorporata di questo scenario. SQL su richiesta effettuerà richieste di archiviazione a un ritmo più lento fino a quando non viene risolta la limitazione delle richieste.

> [!TIP]
> Per un'esecuzione ottimale delle query, non è necessario sottoporre a stress l'account di archiviazione con altri carichi di lavoro durante l'esecuzione di query

## <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di query

Se possibile, è possibile preparare i file per ottenere prestazioni migliori:

- Convertire CSV e JSON in parquet-parquet è un formato a colonne. Poiché è compresso, le dimensioni del file sono inferiori ai file CSV o JSON con gli stessi dati. SQL su richiesta richiede meno tempo e richieste di archiviazione per leggerlo.
- Se una query è destinata a un singolo file di grandi dimensioni, è possibile suddividerla in più file più piccoli.
- Provare a mantenere le dimensioni del file CSV al di sotto di 10 GB.
- È preferibile disporre di file di dimensioni uguali per un singolo percorso OPENROWSET o una posizione di tabella esterna.
- Partizionare i dati archiviando le partizioni in cartelle o nomi di file diversi: selezionare [Usa funzioni filename e FilePath per individuare partizioni specifiche](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Push di caratteri jolly a livelli inferiori nel percorso

È possibile usare caratteri jolly nel percorso per [eseguire query su più file e cartelle](develop-storage-files-overview.md#query-multiple-files-or-folders). SQL su richiesta elenca i file nell'account di archiviazione a partire dal primo * usando l'API di archiviazione ed Elimina i file che non corrispondono al percorso specificato. La riduzione dell'elenco iniziale di file può migliorare le prestazioni se sono presenti molti file che corrispondono al percorso specificato fino al primo carattere jolly.

## <a name="use-appropriate-data-types"></a>Usare i tipi di dati appropriati

I tipi di dati usati nella query influiscono sulle prestazioni. Per ottenere prestazioni migliori, è possibile: 

- Utilizzare le dimensioni dei dati più piccole che conterranno il valore massimo possibile.
  - Se la lunghezza massima del valore del carattere è di 30 caratteri, utilizzare il tipo di dati carattere di lunghezza 30.
  - Se tutti i valori delle colonne di tipo carattere sono di dimensioni fisse, utilizzare char o nchar. In caso contrario, utilizzare varchar o nvarchar.
  - Se il valore massimo della colonna integer è 500, utilizzare smallint poiché è il tipo di dati più piccolo in grado di supportare questo valore. [Qui](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)è possibile trovare intervalli di tipi di dati Integer.
- Se possibile, utilizzare varchar e char anziché nvarchar e nchar.
- Se possibile, utilizzare i tipi di dati basati su Integer. Le operazioni Sort, join e Group by vengono eseguite più velocemente su numeri interi rispetto ai dati dei caratteri.
- Se si utilizza l'inferenza dello schema, [controllare il tipo di dati derivato](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Controllare i tipi di dati dedotti

L' [inferenza dello schema](query-parquet-files.md#automatic-schema-inference) consente di scrivere rapidamente query ed esplorare i dati senza conoscere lo schema del file. Questa comodità si comporta a scapito dei tipi di dati inferiti di dimensioni superiori a quelle effettivamente. Si verifica quando nei file di origine non sono disponibili informazioni sufficienti per assicurarsi che venga utilizzato il tipo di dati appropriato. Ad esempio, i file parquet non contengono metadati sulla lunghezza massima delle colonne di caratteri e SQL su richiesta lo deduce come varchar (8000). 

È possibile controllare i tipi di dati di query risultanti utilizzando [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15).

Nell'esempio seguente viene illustrato come è possibile ottimizzare i tipi di dati dedotti. La stored procedure viene utilizzata per visualizzare i tipi di dati dedotti. 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Questo è il set di risultati.

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|INT|4|

Quando si conoscono i tipi di dati dedotti per la query, è possibile specificare i tipi di dati appropriati:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni FileInfo e FilePath per individuare partizioni specifiche

I dati sono spesso organizzati in partizioni. È possibile richiedere a SQL su richiesta di eseguire query su file e cartelle specifiche. Questa funzione ridurrà il numero di file e la quantità di dati che la query deve leggere ed elaborare. Un ulteriore vantaggio è che potrai ottenere prestazioni migliori.

Per altre informazioni, vedere funzioni [filename](develop-storage-files-overview.md#filename-function) e [filePath](develop-storage-files-overview.md#filepath-function) ed esempi su come [eseguire una query su file specifici](query-specific-files.md).

> [!TIP]
> Eseguire sempre il cast del risultato delle funzioni FilePath e FileInfo ai tipi di dati appropriati. Se si usano tipi di dati character, verificare che venga usata la lunghezza appropriata.

> [!NOTE]
> Le funzioni usate per l'eliminazione della partizione, FilePath e FileInfo, non sono attualmente supportate per le tabelle esterne diverse da quelle create automaticamente per ogni tabella creata in sinapsi Spark.

Se i dati archiviati non sono partizionati, provare a partizionarlo per poter usare queste funzioni per ottimizzare le query destinate a tali file. Quando si [eseguono query su tabelle Spark partizionate](develop-storage-files-spark-tables.md) da SQL su richiesta, la query verrà destinata automaticamente solo ai file necessari.

## <a name="use-parser_version-20-for-querying-csv-files"></a>Usare PARSER_VERSION 2,0 per eseguire query sui file CSV

È possibile utilizzare il parser ottimizzato per le prestazioni quando si eseguono query sui file CSV. Per informazioni dettagliate, controllare [PARSER_VERSION](develop-openrowset.md) .

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare CETAS per migliorare le prestazioni di esecuzione delle query e i join

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili in SQL su richiesta. CETAS è un'operazione parallela che crea metadati della tabella esterna ed Esporta i risultati della query SELECT in un set di file nell'account di archiviazione.

È possibile utilizzare CETAS per archiviare parti di query utilizzate di frequente, come le tabelle di riferimento Unite in join, a un nuovo set di file. A questo punto, è possibile aggiungere join a questa singola tabella esterna anziché ripetere i join comuni in più query.

Quando CETAS genera file parquet, le statistiche vengono create automaticamente quando la prima query è destinata a questa tabella esterna, con conseguente miglioramento delle prestazioni.

## <a name="aad-pass-through-performance"></a>Prestazioni pass-through di AAD

SQL su richiesta consente di accedere ai file nell'archivio usando le credenziali pass-through o SAS di AAD. È possibile che si verifichino prestazioni più lente con il confronto tra AAD e la firma di accesso condiviso. 

Per ottenere prestazioni migliori, provare a usare le credenziali di firma di accesso condiviso per accedere allo spazio di archiviazione fino a quando non si migliora le prestazioni di AAD

## <a name="next-steps"></a>Passaggi successivi

Esaminare l'articolo sulla [risoluzione dei](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) problemi e delle soluzioni comuni. Se si usa il pool SQL anziché SQL su richiesta, vedere l'articolo [procedure consigliate per il pool SQL](best-practices-sql-pool.md) per istruzioni specifiche.
