---
title: Panoramica dell'uso di Linux Foundation Delta Lake in Apache Spark per Azure Synapse Analytics
description: Informazioni su come usare Delta Lake in Apache Spark per Azure Synapse Analytics, per creare e usare tabelle con le proprietà ACID.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 655daeb0149228d78d5288b0e5d0d705a5743d28
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89008642"
---
# <a name="linux-foundation-delta-lake-overview"></a>Panoramica di Linux Foundation Delta Lake

Questo articolo è stato adattato rispetto alla controparte originale disponibile [qui](https://docs.delta.io/latest/quick-start.html) per offrire maggiore chiarezza. Questo articolo consente di esplorare rapidamente le funzionalità principali di [Delta Lake](https://delta.io). L'articolo contiene frammenti di codice che illustrano come eseguire la lettura e la scrittura in tabelle Delta Lake da query interattive, in batch e di streaming. I frammenti di codice sono disponibili anche in un set di notebook [PySpark](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb), [Scala](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb) e [C#](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb).

Verranno illustrati gli argomenti seguenti:

* Creare una tabella
* Leggere i dati
* Aggiornare i dati della tabella
* Sovrascrivere i dati della tabella
* Eseguire l'aggiornamento condizionale senza sovrascrittura
* Leggere versioni precedenti dei dati con lo spostamento cronologico
* Scrivere un flusso di dati in una tabella
* Leggere un flusso di modifiche da una tabella
* Supporto di SQL

## <a name="configuration"></a>Configurazione

Assicurarsi di modificare quanto segue in base all'ambiente in uso.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Il risultato è il seguente:

'/delta/delta-table-335323'

## <a name="create-a-table"></a>Creare una tabella

Per creare una tabella Delta Lake, scrivere un dataframe in formato delta. È possibile modificare il formato da Parquet, CSV, JSON e così via in delta.

Il codice seguente illustra come creare una nuova tabella Delta Lake usando lo schema dedotto dal dataframe.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Il risultato è il seguente:

| ID|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Leggere i dati

Per leggere i dati nella tabella Delta Lake, specificare il percorso dei file e il formato delta.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Il risultato è il seguente:

| ID|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

L'ordine dei risultati è diverso rispetto ai precedenti perché non è stato specificato esplicitamente alcun ordine prima della restituzione dei risultati.

## <a name="update-table-data"></a>Aggiornare i dati della tabella

Delta Lake supporta diverse operazioni per modificare le tabelle con API per dataframe standard. È uno dei principali miglioramenti introdotti dal formato delta. Questo esempio esegue un processo batch per sovrascrivere i dati nella tabella.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Il risultato è il seguente:

| ID|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

Come si può osservare, tutti e cinque i record sono stati aggiornati con nuovi valori.

## <a name="save-as-catalog-tables"></a>Eseguire il salvataggio come tabelle di catalogo

Delta Lake consente la scrittura in tabelle di catalogo gestite o esterne.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Il risultato è il seguente:

|database|         tableName|isTemporary|
|--------|------------------|-----------|
| default|externaldeltatable|      false|
| default| manageddeltatable|      false|

Con questo codice si è creata una nuova tabella nel catalogo da un dataframe esistente. Tale tabella è detta tabella gestita. Si è quindi definita una nuova tabella esterna nel catalogo che usa una posizione esistente. Tale tabella è detta tabella esterna. Nell'output si può osservare che nel catalogo sono elencate entrambe le tabelle, indipendentemente dal modo in cui sono state create.

È ora possibile esaminare le proprietà estese di entrambe le tabelle.

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Il risultato è il seguente:

|col_name                    |data_type                                                                                                    |comment|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |bigint                                                                                                       |Null   |
|                            |                                                                                                             |       |
|Detailed Table Information  |                                                                                                             |       |
|Database                    |default                                                                                                      |       |
|Tabella                       |manageddeltatable                                                                                            |       |
|Proprietario                       |trusted-service-user                                                                                         |       |
|Data e ora creazione                |Sat Apr 25 00:35:34 UTC 2020                                                                                 |       |
|Last Access                 |Thu Jan 01 00:00:00 UTC 1970                                                                                 |       |
|Created By (Creato da)                  |Spark 2.4.4.2.6.99.201-11401300                                                                              |       |
|Tipo                        |MANAGED                                                                                                      |       |
|Provider                    |delta                                                                                                        |       |
|Table Properties            |[transient_lastDdlTime=1587774934]                                                                           |       |
|Statistiche                  |2407 bytes                                                                                                   |       |
|Posizione                    |abfss://data@<data lake>.dfs.core.windows.net/synapse/workspaces/<workspace name>/warehouse/manageddeltatable|       |
|Serde Library               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                                                           |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                                                             |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat                                                    |       |
|Storage Properties          |[serialization.format=1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Il risultato è il seguente:

|col_name                    |data_type                                                             |comment|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |bigint                                                                |Null   |
|                            |                                                                      |       |
|Detailed Table Information  |                                                                      |       |
|Database                    |default                                                               |       |
|Tabella                       |externaldeltatable                                                    |       |
|Proprietario                       |trusted-service-user                                                  |       |
|Data e ora creazione                |Sat Apr 25 00:35:38 UTC 2020                                          |       |
|Last Access                 |Thu Jan 01 00:00:00 UTC 1970                                          |       |
|Created By (Creato da)                  |Spark 2.4.4.2.6.99.201-11401300                                       |       |
|Tipo                        |EXTERNAL                                                              |       |
|Provider                    |DELTA                                                                 |       |
|Table Properties            |[transient_lastDdlTime=1587774938]                                    |       |
|Posizione                    |abfss://data@<data lake>.dfs.core.windows.net/delta/delta-table-587152|       |
|Serde Library               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                    |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                      |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat             |       |
|Storage Properties          |[serialization.format=1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Eseguire l'aggiornamento condizionale senza sovrascrittura

Delta Lake offre API programmatiche per eseguire operazioni (comunemente definite upsert) di aggiornamento, eliminazione e unione condizionale dei dati nelle tabelle.

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Il risultato è il seguente:

| ID|
|---|
|106|
|108|
|  5|
|  7|
|  9|

In questo caso si è semplicemente aggiunto 100 a ogni ID pari.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Il risultato è il seguente:

| ID|
|---|
|  5|
|  7|
|  9|

Si noti che sono state eliminate tutte le righe pari.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Il risultato è il seguente:

| ID|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

In questo caso si è ottenuta una combinazione dei dati esistenti. Ai dati esistenti è stato assegnato il valore -1 nel percorso del codice di aggiornamento (WhenMatched) e sono stati aggiunti anche i nuovi dati precedentemente creati nella parte superiore del frammento e aggiunti tramite il percorso del codice di inserimento (WhenNotMatched).

### <a name="history"></a>Cronologia

Delta Lake offre la possibilità di esaminare la cronologia di una tabella, ossia le modifiche apportate alla tabella Delta sottostante. La cella seguente illustra quanto sia semplice controllare la cronologia.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Il risultato è il seguente:

|version|          timestamp|userId|userName|operation|                                                operationParameters| processo|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  Null|    Null|    MERGE|                       [predicate -> (oldData.`ID` = newData.`ID`)]|Null|    Null|     Null|          3|          Null|        false|
|      3|2020-04-25 00:36:08|  Null|    Null|   DELETE|[predicate -> ["((`ID` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|Null|    Null|     Null|          2|          Null|        false|
|      2|2020-04-25 00:35:51|  Null|    Null|   UPDATE| [predicate -> ((ID#744L % cast(2 as bigint)) = cast(0 as bigint))]|Null|    Null|     Null|          1|          Null|        false|
|      1|2020-04-25 00:35:05|  Null|    Null|    WRITE|                             [mode -> Overwrite, partitionBy -> []]|Null|    Null|     Null|          0|          Null|        false|
|      0|2020-04-25 00:34:34|  Null|    Null|    WRITE|                         [mode -> ErrorIfExists, partitionBy -> []]|Null|    Null|     Null|       Null|          Null|         true|

È così possibile visualizzare tutte le modifiche apportate nei frammenti di codice precedenti.

## <a name="read-older-versions-of-data-using-time-travel"></a>Leggere versioni precedenti dei dati con lo spostamento cronologico

È possibile eseguire query su snapshot precedenti della tabella Delta Lake usando una funzionalità denominata spostamento cronologico. Se si vuole accedere ai dati sovrascritti, si può eseguire una query su uno snapshot della tabella precedente alla sovrascrittura del primo set di dati con l'opzione versionAsOf.

Dopo l'esecuzione della cella seguente verrà visualizzato il primo set di dati nella versione precedente alla sovrascrittura. Lo spostamento cronologico è una funzionalità estremamente avanzata che sfrutta la potenza del log delle transazioni Delta Lake per accedere ai dati che non sono più presenti nella tabella. Rimuovendo l'opzione della versione 0 (o specificando la versione 1) verranno visualizzati nuovamente i dati più recenti. Per altre informazioni, vedere [Eseguire query su uno snapshot precedente di una tabella](https://docs.delta.io/latest/delta-batch.html#deltatimetravel).

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Il risultato è il seguente:

| ID|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

Come si può osservare, si è tornati alla prima versione dei dati.

## <a name="write-a-stream-of-data-to-a-table"></a>Scrivere un flusso di dati in una tabella

È anche possibile scrivere in una tabella Delta Lake usando lo streaming strutturato di Spark. Il log delle transazioni Delta Lake garantisce che l'elaborazione venga effettuata esattamente una volta, anche in presenza di altri flussi o query in batch eseguite simultaneamente sulla tabella. Per impostazione predefinita, i flussi vengono eseguiti in modalità Append, aggiungendo nuovi record alla tabella.

Per altre informazioni sull'integrazione di Delta Lake con lo streaming strutturato, vedere [Letture e scritture di tabelle in streaming](https://docs.delta.io/latest/delta-streaming.html).

Nelle celle seguenti vengono effettuate queste operazioni:

* La cella 30 mostra i dati appena aggiunti in modalità Append.
* La cella 31 controlla la cronologia.
* La cella 32 arresta il processo di streaming strutturato.
* La cella 33 controlla la cronologia. Si noterà che le aggiunte sono state interrotte.

Per prima cosa, si configurerà un semplice processo di streaming Spark per generare una sequenza ed eseguire la scrittura nella tabella Delta.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Leggere un flusso di modifiche da una tabella

Mentre il flusso esegue la scrittura nella tabella Delta Lake, è anche possibile leggere da tale tabella come origine del flusso. Ad esempio, si può avviare un'altra query di streaming che stampa tutte le modifiche apportate alla tabella Delta Lake.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Il risultato è il seguente:

| ID|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Il risultato è il seguente:

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       Null|

In questo caso si rimuovono alcune delle colonne meno interessanti per semplificare l'esperienza di visualizzazione della cronologia.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Il risultato è il seguente:

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       Null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Eseguire la conversione da Parquet a Delta

È possibile eseguire una conversione sul posto dal formato Parquet a Delta.

Il codice seguente consente di verificare se la tabella esistente è in formato delta o meno:
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Il risultato è il seguente:

Falso

Si convertiranno quindi i dati nel formato delta e si verificherà se l'operazione è riuscita.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Il risultato è il seguente:

Vero

## <a name="sql-support"></a>Supporto di SQL

Delta supporta comandi di utilità per tabelle tramite SQL. È possibile usare SQL per:

* Ottenere la cronologia di una tabella Delta
* Eseguire un'operazione vacuum su una tabella Delta
* Convertire un file Parquet in Delta

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Il risultato è il seguente:

|version|          timestamp|userId|userName|       operation| operationParameters| processo|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  Null|    Null|STREAMING UPDATE|[outputMode -> Ap...|Null|    Null|     Null|          4|          Null|         true|
|      4|2020-04-25 00:36:27|  Null|    Null|           MERGE|[predicate -> (ol...|Null|    Null|     Null|          3|          Null|        false|
|      3|2020-04-25 00:36:08|  Null|    Null|          DELETE|[predicate -> ["(...|Null|    Null|     Null|          2|          Null|        false|
|      2|2020-04-25 00:35:51|  Null|    Null|          UPDATE|[predicate -> ((i...|Null|    Null|     Null|          1|          Null|        false|
|      1|2020-04-25 00:35:05|  Null|    Null|           WRITE|[mode -> Overwrit...|Null|    Null|     Null|          0|          Null|        false|
|      0|2020-04-25 00:34:34|  Null|    Null|           WRITE|[mode -> ErrorIfE...|Null|    Null|     Null|       Null|          Null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Il risultato è il seguente:

|                path|
|--------------------|
|abfss://data@arca...|

A questo punto si verificherà che una tabella non sia in formato delta, quindi si convertirà tale tabella in formato delta con Spark SQL e si verificherà che sia stata convertita correttamente.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Il risultato è il seguente:

Vero

Per la documentazione completa, vedere la [pagina della documentazione di Delta Lake](https://docs.delta.io/latest/delta-intro.html).

Per altre informazioni, vedere il [progetto Delta Lake](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di .NET per Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
