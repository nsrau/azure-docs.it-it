---
title: Analizzare lo schema con matrici e strutture annidate
description: Come analizzare matrici e strutture annidate con Apache Spark e SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907953"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Analizzare tipi di dati complessi in sinapsi

Questo articolo è pertinente per i file parquet e i contenitori nel **collegamento sinapsi di Azure per Azure Cosmos DB**. Spiega in che modo gli utenti possono usare Spark o SQL per leggere o trasformare i dati con schemi complessi, ad esempio matrici o strutture annidate. L'esempio seguente viene eseguito con un unico documento, ma può essere facilmente ridimensionato a miliardi di documenti con Spark o SQL. Il codice seguente usa PySpark (Python).

## <a name="use-case"></a>Caso d'uso

Con i tipi di dati moderni, i tipi di dati complessi sono spesso comuni per gestire e rappresentare una sfida per gli ingegneri di dati. L'analisi dello schema annidato e delle matrici presenta alcune problemi:
* Complessa per la scrittura di query SQL
* Difficile rinominare/eseguire il cast del tipo di dati delle colonne nidificate
* Problemi di prestazioni con oggetti profondamente annidati

Gli ingegneri dei dati devono comprendere come elaborare in modo efficiente i tipi di dati e renderli facilmente accessibili a tutti.

Nell'esempio seguente viene usata la funzione sinapsi Spark per leggere e trasformare gli oggetti tramite frame di dati in una struttura flat. La sinapsi SQL senza server viene utilizzata per eseguire una query direttamente su tali oggetti e restituire tali risultati come tabella normale.

## <a name="what-are-arrays-and-nested-structures"></a>Che cosa sono le matrici e le strutture annidate?

L'oggetto seguente deriva da app Insight. In questo oggetto sono presenti strutture annidate, ma anche matrici che contengono strutture nidificate.

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
Quando si stampa lo schema del frame di dati di tale oggetto (denominato **DF**) con il comando **DF. PrintSchema**, viene visualizzata la rappresentazione seguente:

* il colore giallo rappresenta la struttura annidata
* il colore verde rappresenta una matrice con due elementi

[![Origine dello schema](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid, _ts e _etag sono stati aggiunti nel sistema perché il documento è stato inserito in Azure Cosmos DB archivio transazionale.

Il frame di dati sopra riportato viene conteggiato solo per 5 colonne e una riga. Dopo la trasformazione, il frame di dati curato avrà 13 colonne e due righe in un formato tabulare.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Rendere flat le strutture annidate ed esplodere matrici con Apache Spark

Con la sinapsi Spark, la trasformazione di strutture annidate in colonne ed elementi di matrice in più righe è un'operazione semplice. I passaggi seguenti possono essere usati da tutti per la propria implementazione.

[![Passaggi di trasformazioni Spark](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**Passaggio 1**: si definisce una funzione per rendere flat lo schema annidato. Questa funzione può essere utilizzata senza modifiche. Creare una cella in un notebook di Pyspark con tale funzione:

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

**Passaggio 2**: usare la funzione per rendere flat lo schema annidato del frame di dati **DF** in un nuovo frame di dati **df_flat**:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

La funzione di visualizzazione deve restituire 10 colonne e una riga. La matrice e i relativi elementi annidati sono ancora presenti.

**Passaggio 3**: ora è possibile trasformare la matrice **context_custom_dimensions** nel frame di dati **df_flat** in un nuovo **df_flat_explode**di dataframe. Nel codice riportato di seguito viene definita anche la colonna selezionata:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

La funzione di visualizzazione deve restituire il risultato seguente: 10 colonne e 2 righe. Il passaggio successivo consiste nel rendere flat gli schemi annidati con la funzione definita nel passaggio 1.

**Passaggio 4**: usare la funzione per rendere flat lo schema annidato del frame di dati **df_flat_explode** in un nuovo frame di dati **df_flat_explode_flat**:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

La funzione di visualizzazione deve visualizzare 13 colonne e 2 righe:

La funzione printSchema del frame di dati df_flat_explode_flat restituisce il risultato seguente:

[![Schema finale](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Leggi matrici e strutture annidate direttamente con SQL Server

L'esecuzione di query, la creazione di viste e tabelle su tali oggetti è possibile con SQL Server.

Prima di tutto, a seconda della modalità di archiviazione dei dati, gli utenti devono utilizzare la tassonomia riportata di seguito. Tutte le lettere maiuscole sono specifiche del caso d'uso:

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = AccountName; database = DATAbasename; Collection = CollectionName; Region = REGIONTOQUERY, Secret =' YOURSECRET ' |' CosmosDB ' (collegamento sinapsi)|



**SQL Server** supporterà il servizio collegato per il collegamento a sinapsi di Azure per Azure Cosmos DB e AAD passthrough. La funzionalità è attualmente disponibile in anteprima gestita per il collegamento sinapsi.

Sostituire di seguito:
* ' YOUR BULK over ' con la stringa di connessione dell'origine dati a cui ci si connette
* ' Il tipo precedente ' in base al formato usato per la connessione all'origine

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

Sono stati eseguiti due tipi diversi di operazioni:
* Nella riga di codice seguente viene definita la colonna denominata contextdataeventTime che fa riferimento all'elemento annidato: context. Data. eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Questa riga definirà la colonna denominata contextdataeventTime che fa riferimento all'elemento Nest: context>data>eventTime

* **Cross Apply** viene usato per creare nuove righe per ogni elemento sotto la matrice e quindi con definisce ciascun oggetto annidato simile al primo punto elenco: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Se nella matrice erano presenti 5 elementi con 4 strutture annidate, SQL senza server restituirebbe 5 righe e 4 colonne.

SQL Server non può eseguire query sul posto, eseguire il mapping della matrice in 2 righe e visualizzare tutte le strutture annidate in colonne.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come eseguire query sul collegamento a sinapsi di Azure per Azure Cosmos DB con Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Tipi annidati parquet di query](./sql/query-parquet-nested-types.md) 