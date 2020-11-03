---
title: Procedure consigliate per SQL su richiesta (anteprima)
description: Raccomandazioni e procedure consigliate da tenere presenti quando si usa SQL su richiesta (anteprima).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 9de3e3503d63cf6dcaa98adc318d86df7700458d
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241872"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Procedure consigliate per SQL su richiesta (anteprima) in Azure Synapse Analytics

Questo articolo include una raccolta di procedure consigliate per l'uso di SQL su richiesta (anteprima). SQL su richiesta è una risorsa in Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerazioni generali

SQL su richiesta consente di eseguire query sui file negli account di archiviazione di Azure. Non include funzionalità di archiviazione o inserimento in locale. Di conseguenza, tutti i file di destinazione della query sono esterni a SQL su richiesta. Tutte le operazioni correlate alla lettura di file dall'archiviazione possono avere un impatto sulle prestazioni delle query.

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Condividere la posizione dell'account di archiviazione di Azure e di SQL su richiesta

Per ridurre al minimo la latenza, condividere la posizione dell'account di archiviazione di Azure e dell'endpoint SQL su richiesta. Gli account di archiviazione e gli endpoint sottoposti a provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per prestazioni ottimali, se si accede ad altri account di archiviazione con SQL su richiesta, assicurarsi che si trovino nella stessa area. Se non si trovano nella stessa area, si verificherà un aumento della latenza per il trasferimento in rete dei dati tra l'area remota e l'area dell'endpoint.

## <a name="azure-storage-throttling"></a>Limitazione delle richieste di archiviazione di Azure

Più applicazioni e servizi possono accedere all'account di archiviazione. La limitazione delle richieste di archiviazione si verifica quando il numero combinato di operazioni di I/O al secondo o unità di elaborazione generato da applicazioni, servizi e carichi di lavoro di SQL su richiesta supera i limiti dell'account di archiviazione. Di conseguenza, si verificherà un effetto negativo significativo sulle prestazioni delle query.

Quando viene rilevata la limitazione delle richieste, SQL su richiesta prevede una soluzione predefinita per risolverla. SQL su richiesta effettuerà richieste di archiviazione a un ritmo più lento fino a quando non verrà risolta la limitazione delle richieste.

> [!TIP]
> Per un'esecuzione ottimale delle query, evitare di sovraccaricare l'account di archiviazione con altri carichi di lavoro durante l'esecuzione di query.

## <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di query

Se possibile, preparare i file per migliorare le prestazioni:

- Convertire CSV e JSON in Parquet. Parquet è un formato a colonne. Poiché è compresso, le dimensioni dei file sono ridotte rispetto a quelle dei file CSV o JSON che contengono gli stessi dati. Per leggerli in SQL su richiesta, sono necessari meno tempo e un numero minore di richieste di archiviazione.
- Se una query è destinata a un singolo file di grandi dimensioni, è possibile dividerla in più file di dimensioni ridotte.
- Provare a mantenere le dimensioni dei file CSV al di sotto di 10 GB.
- È preferibile avere file di dimensioni uguali per un singolo percorso OPENROWSET o per la proprietà LOCATION di una tabella esterna.
- Partizionare i dati archiviando le partizioni in cartelle o nomi di file diversi. Vedere [Usare le funzioni filename e filepath per indicare come destinazione partizioni specifiche](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Eseguire il push di caratteri jolly per ridurre i livelli nel percorso

È possibile usare caratteri jolly nel percorso per [eseguire query su più file e cartelle](query-data-storage.md#query-multiple-files-or-folders). SQL su richiesta elenca i file nell'account di archiviazione, a partire dal primo carattere * con l'API di archiviazione. Elimina i file che non corrispondono al percorso specificato. La riduzione dell'elenco iniziale di file può migliorare le prestazioni nel caso in cui molti file corrispondano al percorso specificato fino al primo carattere jolly.

## <a name="use-appropriate-data-types"></a>Usare i tipi di dati appropriati

I tipi di dati usati nella query influiscono sulle prestazioni. Per migliorare le prestazioni, seguire queste linee guida: 

- Usare le dimensioni di dati minime che supportano il valore massimo possibile.
  - Se la lunghezza massima del valore in caratteri è 30, usare un tipo di dati carattere di lunghezza 30.
  - Se tutti i valori delle colonne carattere sono di dimensioni fisse, usare **char** o **nchar** . In caso contrario, usare **varchar** o **nvarchar** .
  - Se il valore massimo di una colonna di numeri interi è 500, usare **smallint** , perché è il tipo di caratteri più piccolo in grado di supportare questo valore. Per informazioni sugli intervalli dei tipi di dati di numeri interi, vedere [questo articolo](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15&preserve-view=true).
- Se possibile, usare **varchar** e **char** invece di **nvarchar** e **nchar** .
- Se possibile, usare tipi di dati basati su integer. Le operazioni SORT, JOIN e GROUP BY vengono completate più velocemente su numeri interi che non su dati di tipo carattere.
- Se si usa l'inferenza dello schema, [controllare i tipi di dati dedotti](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Controllare i tipi di dati dedotti

L'[inferenza dello schema](query-parquet-files.md#automatic-schema-inference) consente di scrivere rapidamente query e di esplorare i dati senza conoscere gli schemi dei file. Il costo di questa praticità è che i tipi di dati dedotti possono essere più grandi dei tipi di dati effettivi. Questa situazione si verifica quando nei file di origine non sono disponibili informazioni sufficienti per assicurare che venga usato il tipo di dati appropriato. Ad esempio, i file Parquet non contengono metadati sulla lunghezza massima delle colonne di caratteri. SQL su richiesta deduce quindi che si tratti di varchar (8000).

È possibile usare [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) per controllare i tipi di dati risultanti della query.

L'esempio seguente mostra come ottimizzare i tipi di dati dedotti. Questa procedura viene usata per visualizzare i tipi di dati dedotti: 
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

Il set di risultati è il seguente:

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
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni filename e filepath per indicare come destinazione partizioni specifiche

I dati sono spesso organizzati in partizioni. È possibile indicare a SQL su richiesta di eseguire query su cartelle e file specifici. Questa operazione ridurrà il numero di file e la quantità di dati che la query deve leggere ed elaborare. Un ulteriore vantaggio è che si otterranno prestazioni più elevate.

Per altre informazioni, vedere le informazioni sulle funzioni [filename](query-data-storage.md#filename-function) e [filepath](query-data-storage.md#filepath-function) e vedere gli esempi relativi a [query su file specifici](query-specific-files.md).

> [!TIP]
> Eseguire sempre il cast dei risultati delle funzioni filepath e filename al tipo di dati appropriato. Se si usano tipi di dati carattere, assicurarsi di usare la lunghezza appropriata.

> [!NOTE]
> Le funzioni usate per l'eliminazione di partizioni, filepath e filename, non sono attualmente supportate per tabelle esterne diverse da quelle create automaticamente per ogni tabella creata in Apache Spark per Azure Synapse Analytics.

Se i dati archiviati non sono partizionati, prendere in considerazione di partizionarli. In questo modo è possibile usare queste funzioni per ottimizzare le query da eseguire su tali file. Quando [si esegue una query su tabelle Apache Spark per Azure Synapse partizionate](develop-storage-files-spark-tables.md) da SQL su richiesta, la query viene automaticamente eseguita solo sui file necessari.

## <a name="use-parser_version-20-to-query-csv-files"></a>Usare PARSER_VERSION 2.0 per eseguire query su file CSV

Quando si eseguono query su file CSV, è possibile usare un parser ottimizzato per le prestazioni. Per informazioni dettagliate, vedere [PARSER_VERSION](develop-openrowset.md).

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare CETAS per ottimizzare le prestazioni e i join delle query

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili in SQL su richiesta. CETAS è un'operazione parallela che crea metadati di tabelle esterne ed esporta i risultati delle query SELECT in un set di file nell'account di archiviazione.

È possibile usare CETAS per archiviare parti di query usate di frequente, come le tabelle di riferimento unite in join, in un nuovo set di file. È quindi possibile creare un join a questa sola tabella esterna invece di ripetere join comuni in più query.

Poiché CETAS genera file Parquet, le statistiche vengono create automaticamente quando la prima query raggiunge questa tabella esterna, con un conseguente miglioramento delle prestazioni.

## <a name="azure-ad-pass-through-performance"></a>Prestazioni del pass-through di Azure AD

SQL su richiesta consente di accedere ai file nella risorsa di archiviazione usando credenziali pass-through di Azure Active Directory (Azure AD) o di firma di accesso condiviso. È possibile riscontrare prestazioni inferiori con il pass-through di Azure AD rispetto alla firma di accesso condiviso.

Se sono necessarie prestazioni più elevate, provare a usare credenziali di firma di accesso condiviso per accedere allo spazio di archiviazione finché le prestazioni del pass-through di Azure AD non risultano migliorate.

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo sulla [risoluzione dei problemi](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per la risoluzione di problemi comuni. Se si usano pool SQL invece di SQL su richiesta, vedere [Procedure consigliate per i pool SQL](best-practices-sql-pool.md) per istruzioni specifiche.
