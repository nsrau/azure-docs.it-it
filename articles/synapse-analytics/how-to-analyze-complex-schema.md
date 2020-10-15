---
title: Analizzare lo schema con matrici e strutture annidate
description: Come analizzare matrici e strutture annidate con Apache Spark e SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 43e4027c9d88b209ed0606e045dd5b4d47d3ff6e
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088517"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analizzare i tipi di dati complessi in Azure sinapsi Analytics

Questo articolo è pertinente per i file parquet e i contenitori nel [collegamento sinapsi di Azure per Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). È possibile usare Spark o SQL per leggere o trasformare i dati con schemi complessi, ad esempio matrici o strutture annidate. L'esempio seguente viene completato con un unico documento, ma può essere facilmente scalabile fino a miliardi di documenti con Spark o SQL. Il codice incluso in questo articolo usa PySpark (Python).

## <a name="use-case"></a>Caso d'uso

I tipi di dati complessi sono sempre più comuni e rappresentano una sfida per gli ingegneri di dati. L'analisi di matrici e schemi annidati può comportare query SQL complesse e dispendiose in termini di tempo. Inoltre, può essere difficile rinominare o eseguire il cast del tipo di dati delle colonne nidificate. Inoltre, quando si utilizzano oggetti profondamente annidati, è possibile riscontrare problemi di prestazioni.

Gli ingegneri dei dati devono comprendere come elaborare in modo efficiente i tipi di dati complessi e renderli facilmente accessibili a tutti. Nell'esempio seguente si usa Spark in Azure sinapsi Analytics per leggere e trasformare gli oggetti in una struttura flat tramite frame di dati. Usare il modello senza server di SQL in Azure sinapsi Analytics per eseguire query direttamente su tali oggetti e restituire tali risultati come tabella normale.

## <a name="what-are-arrays-and-nested-structures"></a>Che cosa sono le matrici e le strutture annidate?

Il seguente oggetto deriva da [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). In questo oggetto sono presenti strutture annidate e matrici che contengono strutture annidate.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Esempio di schema di matrici e strutture annidate
Quando si esegue la stampa dello schema del frame di dati dell'oggetto (denominato **DF**) con il comando `df.printschema` , viene visualizzata la rappresentazione seguente:

* Il giallo rappresenta le strutture nidificate.
* Verde rappresenta una matrice con due elementi.

[![Codice con evidenziazione gialla e verde, che mostra l'origine dello schema](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` e `_etag` sono stati aggiunti al sistema perché il documento è stato inserito nell'archivio transazionale Azure Cosmos DB.

Il frame di dati precedente viene conteggiato per 5 colonne e solo una riga. Dopo la trasformazione, il frame di dati curato avrà 13 colonne e 2 righe in formato tabulare.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Appiattimento di strutture annidate e matrici esplose

Con Spark in Azure sinapsi Analytics, è facile trasformare le strutture annidate in colonne ed elementi di matrice in più righe. Usare la procedura seguente per l'implementazione di.

[![Diagramma di flusso che illustra i passaggi per le trasformazioni Spark](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>Definire una funzione per rendere flat lo schema annidato

Questa funzione può essere utilizzata senza modifiche. Creare una cella in un [notebook di PySpark](quickstart-apache-spark-notebook.md) con la funzione seguente:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Utilizzare la funzione per rendere flat lo schema annidato

In questo passaggio viene reso Flat lo schema annidato del frame di dati (**DF**) in un nuovo frame di dati ( `df_flat` ):

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

La funzione di visualizzazione deve restituire 10 colonne e una riga. La matrice e i relativi elementi annidati sono ancora presenti.

### <a name="transform-the-array"></a>Trasformare la matrice

Qui si trasforma la matrice, `context_custom_dimensions` , nel frame di dati `df_flat` , in un nuovo frame di dati `df_flat_explode` . Nel codice seguente viene anche definita la colonna da selezionare:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

La funzione di visualizzazione deve restituire 10 colonne e 2 righe. Il passaggio successivo consiste nel rendere flat gli schemi annidati con la funzione definita nel passaggio 1.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Utilizzare la funzione per rendere flat lo schema annidato

Infine, si usa la funzione per rendere flat lo schema annidato del frame di dati `df_flat_explode` in un nuovo frame di dati `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

La funzione di visualizzazione deve visualizzare 13 colonne e 2 righe.

La funzione `printSchema` del frame di dati `df_flat_explode_flat` restituisce il risultato seguente:

[![Codice che mostra lo schema finale](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Leggi direttamente le matrici e le strutture annidate

Con il modello senza server di SQL, è possibile eseguire query e creare viste e tabelle su tali oggetti.

In primo luogo, a seconda della modalità di archiviazione dei dati, gli utenti devono utilizzare la tassonomia riportata di seguito. Tutti gli elementi visualizzati in maiuscolo sono specifici del caso d'uso:

| In blocco | Formato |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = AccountName; database = DATAbasename; Collection = CollectionName; Region = REGIONTOQUERY ', Secret =' YOURSECRET ' |' CosmosDB ' (collegamento a sinapsi di Azure)|


Sostituire ogni campo come segue:
* "YOUR BULK over" è la stringa di connessione dell'origine dati a cui ci si connette.
* ' Il tipo precedente ' è il formato usato per la connessione all'origine.

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Esistono due tipi diversi di operazioni:

- Il primo tipo di operazione è indicato nella riga di codice seguente, che definisce la colonna denominata `contextdataeventTime` che fa riferimento all'elemento annidato `Context.Data.eventTime` . 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  Questa riga definisce la colonna denominata `contextdataeventTime` che fa riferimento all'elemento annidato `Context>Data>eventTime` .

- Il secondo tipo di operazione USA `cross apply` per creare nuove righe per ogni elemento nella matrice. Definisce quindi ogni oggetto annidato. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Se la matrice include 5 elementi con 4 strutture annidate, il modello senza server di SQL restituisce 5 righe e 4 colonne. Il modello senza server di SQL può eseguire query sul posto, eseguire il mapping della matrice in 2 righe e visualizzare tutte le strutture annidate in colonne.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come eseguire query sul collegamento sinapsi per Azure Cosmos DB con Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Eseguire query su tipi annidati di Parquet](./sql/query-parquet-nested-types.md) 
