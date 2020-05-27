---
title: Procedure consigliate per SQL su richiesta (anteprima) in Azure Synapse Analytics
description: Raccomandazioni e procedure consigliate da tenere presenti durante l'uso di SQL su richiesta (anteprima).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 86678365d1510199247e8a1aaa48ec844d07de32
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592934"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Procedure consigliate per SQL su richiesta (anteprima) in Azure Synapse Analytics

Questo articolo include una raccolta di procedure consigliate per l'uso di SQL su richiesta (anteprima). SQL su richiesta è una risorsa aggiuntiva inclusa in Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerazioni generali

SQL su richiesta consente di eseguire query sui file negli account di archiviazione di Azure. Non include funzionalità di archiviazione o inserimento in locale. Di conseguenza, tutti i file di destinazione della query sono esterni a SQL su richiesta. Tutte le operazioni correlate alla lettura di file dall'archiviazione possono avere un impatto sulle prestazioni delle query.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Condividere la posizione dell'account di archiviazione di Azure e di SQL su richiesta

Per ridurre al minimo la latenza, condividere la posizione dell'account di archiviazione di Azure e dell'endpoint SQL su richiesta. Gli account di archiviazione e gli endpoint sottoposti a provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per prestazioni ottimali, se si accede ad altri account di archiviazione con SQL su richiesta, assicurarsi che si trovino nella stessa area. Se non si trovano nella stessa area, si verificherà un aumento della latenza per il trasferimento in rete dei dati tra l'area remota e quella dell'endpoint.

## <a name="azure-storage-throttling"></a>Limitazione delle richieste di archiviazione di Azure

Più applicazioni e servizi possono accedere all'account di archiviazione. La limitazione delle richieste di archiviazione si verifica quando il numero combinato di operazioni di I/O al secondo o unità di elaborazione generato da applicazioni, servizi e carichi di lavoro di SQL su richiesta supera i limiti dell'account di archiviazione. Di conseguenza, si verificherà un effetto negativo significativo sulle prestazioni delle query.

Una volta rilevata la limitazione delle richieste, SQL su richiesta prevede una soluzione predefinita di gestione di questo scenario. SQL su richiesta effettuerà richieste di archiviazione a un ritmo più lento fino a quando non verrà risolta la limitazione delle richieste.

> [!TIP]
> Per un'esecuzione ottimale delle query, è consigliabile evitare di sovraccaricare l'account di archiviazione con altri carichi di lavoro durante l'esecuzione di query.

## <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di query

Se possibile, preparare i file per migliorare le prestazioni:

- Convertire i file CSV e JSON in Parquet, un formato a colonne. Poiché è compresso, le dimensioni dei file sono ridotte rispetto a quelle dei file CSV o JSON con gli stessi dati. Per leggerli in SQL su richiesta, saranno necessari tempi e richieste di archiviazione inferiori.
- Se una query è destinata a un singolo file di grandi dimensioni, è possibile dividerla in più file di dimensioni ridotte.
- Provare a mantenere le dimensioni dei file CSV al di sotto di 10 GB.
- È preferibile avere file di dimensioni uguali per un singolo percorso OPENROWSET o per la proprietà LOCATION di una tabella esterna.
- Partizionare i dati archiviando le partizioni in cartelle o nomi file diversi. Vedere come [usare le funzioni filename e filepath per impostare partizioni specifiche come destinazione](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Eseguire il push di caratteri jolly per ridurre i livelli di un percorso

È possibile usare caratteri jolly nel percorso per [eseguire query su più file e cartelle](develop-storage-files-overview.md#query-multiple-files-or-folders). SQL su richiesta elenca i file nell'account di archiviazione a partire dal primo asterisco (*) usando l'API di archiviazione ed elimina i file che non corrispondono al percorso specificato. La riduzione dell'elenco iniziale di file può migliorare le prestazioni nel caso in cui molti file corrispondano al percorso specificato fino al primo carattere jolly.

## <a name="use-appropriate-data-types"></a>Usare i tipi di dati appropriati

I tipi di dati usati nella query influiscono sulle prestazioni. Per migliorare le prestazioni, è possibile: 

- Usare le dimensioni di dati minime che supportano il valore massimo possibile.
  - Se la lunghezza massima del valore in caratteri è 30, usare il tipo di dati char di lunghezza 30.
  - Se tutti i valori delle colonne di caratteri sono di dimensioni fisse, usare char o nchar. In caso contrario, usare varchar o nvarchar.
  - Se il valore massimo della colonna di interi è 500, usare smallint, perché è il tipo di caratteri minimo in grado di supportare questo valore. Per informazioni sugli intervalli di tipi di dati integer, vedere [qui](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15).
- Se possibile, usare varchar e char invece di nvarchar e nchar.
- Se possibile, usare tipi di dati basati su integer. Le operazioni sort, join e group by vengono eseguite più velocemente su numeri interi che non su dati di tipo carattere.
- Se si usa l'inferenza dello schema, [controllare il tipo di dati dedotto](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Controllare i tipi di dati dedotti

L'[inferenza dello schema](query-parquet-files.md#automatic-schema-inference) consente di scrivere rapidamente query e di esplorare i dati senza conoscere lo schema del file. Questa vantaggio si ottiene a discapito dei tipi di file dedotti, che risultano più grandi di quanto non lo siano in realtà. Questa situazione si verifica quando nei file di origine non sono disponibili informazioni sufficienti per assicurare che venga usato il tipo di dati appropriato. Ad esempio, i file Parquet non contengono metadati sulla lunghezza massima delle colonne di caratteri e SQL su richiesta deduce che si tratti di varchar (8000). 

È possibile controllare i tipi di dati risultanti della query usando [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15).

L'esempio seguente mostra come ottimizzare i tipi di dati dedotti. La procedura si usa per mostrare i tipi di dati dedotti. 
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
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|INT|4|

Se si conoscono i tipi di dati dedotti per la query, è possibile specificare quelli appropriati:

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

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni fileinfo e filepath per indicare come destinazione partizioni specifiche

I dati sono spesso organizzati in partizioni. È possibile indicare a SQL su richiesta di eseguire query su cartelle e file specifici. Questa funzione ridurrà il numero di file e la quantità di dati che la query deve leggere ed elaborare. Un ulteriore vantaggio è che si otterranno prestazioni più elevate.

Per altre informazioni, vedere le funzioni [filename](develop-storage-files-overview.md#filename-function) e [filepath](develop-storage-files-overview.md#filepath-function), oltre ad esempi su come [eseguire query su file specifici](query-specific-files.md).

> [!TIP]
> Eseguire sempre il cast del risultato delle funzioni filepath e fileinfo al tipo di dati appropriato. Se si usano tipi di dati char, assicurarsi che venga usata la lunghezza appropriata.

> [!NOTE]
> Le funzioni usate per l'eliminazione delle partizioni, filepath e fileinfo, non sono attualmente supportate per tabelle esterne diverse da quelle create automaticamente per ogni tabella creata in Apache Spark per Azure Synapse Analytics.

Se i dati archiviati non sono partizionati, provare a partizionarli per poter usare queste funzioni e ottimizzare le query destinate a tali file. Quando [si eseguono query su tabelle Spark partizionate](develop-storage-files-spark-tables.md) da SQL su richiesta, la query verrà destinata automaticamente solo ai file necessari.

## <a name="use-parser_version-20-for-querying-csv-files"></a>Usare PARSER_VERSION 2.0 per eseguire query sui file CSV

Quando si eseguono query su file CSV, è possibile usare il parser ottimizzato per le prestazioni. Per informazioni dettagliate, vedere [PARSER_VERSION](develop-openrowset.md).

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare CETAS per ottimizzare le prestazioni e i join delle query

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili in SQL su richiesta. CETAS è un'operazione parallela che crea metadati di tabelle esterne ed esporta i risultati delle query SELECT in un set di file nell'account di archiviazione.

È possibile usare CETAS per archiviare parti di query usate di frequente, come le tabelle di riferimento unite in join, in un nuovo set di file. In seguito, è possibile creare un join con questa singola tabella esterna invece di ripetere join comuni in più query.

Poiché CETAS genera file Parquet, le statistiche vengono create automaticamente quando la prima query raggiunge questa tabella esterna, con un conseguente miglioramento delle prestazioni.

## <a name="aad-pass-through-performance"></a>Prestazioni del pass-through di AAD

SQL su richiesta consente di accedere ai file nell'archiviazione usando le credenziali pass-through o di firma di accesso condiviso di AAD. È possibile che si riscontrino prestazioni più lente con il pass-through di AAD rispetto alla firma di accesso condiviso. 

Se sono necessarie prestazioni più elevate, provare a usare credenziali di firma di accesso condiviso per accedere all'archiviazione finché le prestazioni del pass-through di AAD non risultano migliorate.

## <a name="next-steps"></a>Passaggi successivi

Vedere anche l'articolo sulla [risoluzione dei problemi](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) comuni e sulle soluzioni. Se si usa il pool SQL invece di SQL su richiesta, vedere l'articolo [Procedure consigliate per il pool SQL](best-practices-sql-pool.md) per istruzioni specifiche.
