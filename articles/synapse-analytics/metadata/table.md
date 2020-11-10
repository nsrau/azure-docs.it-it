---
title: Tabelle di metadati condivisi
description: Azure Synapse Analytics offre un modello di metadati condivisi per cui la creazione di una tabella nel pool di Apache Spark serverless la renderà accessibile dal pool SQL serverless (anteprima) e dal pool SQL dedicato senza duplicazione dei dati.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: f269217908bea4b5e8ef3c0004a9cec9d5d682c7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314544"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Tabelle di metadati condivisi di Azure Synapse Analytics

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Azure Synapse Analytics consente ai diversi motori di calcolo delle aree di lavoro di condividere database e tabelle basate su Parquet tra i pool di Apache Spark (anteprima) e il pool SQL serverless (anteprima).

Dopo la creazione di un database tramite un processo Spark, è possibile aggiungervi tabelle create con Spark che usano Parquet come formato di archiviazione. Queste tabelle diventeranno immediatamente disponibili per l'esecuzione di query da parte di qualsiasi pool di Spark dell'area di lavoro di Azure Synapse. Possono essere usate anche da qualsiasi processo Spark che disponga delle autorizzazioni necessarie.

Le tabelle Spark create, gestite ed esterne vengono rese disponibili anche come tabelle esterne con lo stesso nome nel database sincronizzato corrispondente nel pool SQL serverless. La sezione [Esposizione di una tabella di Spark in SQL](#expose-a-spark-table-in-sql) fornisce informazioni dettagliate sulla sincronizzazione delle tabelle.

Poiché le tabelle vengono sincronizzate con il pool SQL serverless in modo asincrono, vengono visualizzate con un ritardo.

## <a name="manage-a-spark-created-table"></a>Gestire una tabella creata in Spark

Usare Spark per gestire i database creati in Spark. Ad esempio, è possibile eliminare un database tramite un processo del pool di Apache Spark serverless creare al suo interno tabelle da Spark.

Se si creano oggetti in un database del pool SQL serverless o si prova a eliminare un database, l'operazione riesce, ma il database Spark originale rimarrà inalterato.

## <a name="expose-a-spark-table-in-sql"></a>Esposizione di una tabella Spark in SQL

### <a name="shared-spark-tables"></a>Tabelle Spark condivise

Spark fornisce due tipi di tabelle che vengono esposte automaticamente da Azure Synapse in SQL:

- Tabelle gestite

  Spark offre diverse opzioni per l'archiviazione dei dati in tabelle gestite, come TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA e LIBSVM. Questi file sono normalmente archiviati nella directory `warehouse` in cui sono archiviati i dati delle tabelle gestite.

- Tabelle esterne

  Spark consente anche di creare tabelle esterne su dati esistenti, tramite l'opzione `LOCATION` o il formato Hive. Queste tabelle esterne possono essere su diversi formati di dati, tra cui Parquet.

Azure Synapse attualmente condivide solo le tabelle di Spark gestite ed esterne che archiviano i dati in formato Parquet con i motori SQL. Le tabelle basate su altri formati non vengono sincronizzate automaticamente. Se il motore SQL supporta il formato sottostante delle tabelle, si dovrebbe essere in grado di sincronizzarle in modo esplicito come tabelle esterne nel proprio database SQL.

### <a name="share-spark-tables"></a>Condivisione di tabelle Spark

Le tabelle di Spark gestite ed esterne condivisibili vengono esposte nel motore SQL come tabelle esterne con le proprietà seguenti:

- L'origine dati della tabella esterna SQL è l'origine dati che rappresenta la cartella in cui si trova la tabella di Spark.
- Il formato di file della tabella esterna SQL è Parquet.
- Le credenziali di accesso della tabella esterna SQL sono pass-through.

Poiché tutti i nomi di tabella Spark sono nomi di tabella SQL validi e tutti i nomi di colonna Spark sono nomi di colonna SQL validi, i nomi della tabella Spark e delle sue colonne verranno usati per la tabella esterna SQL.

Le tabelle di Spark forniscono tipi di dati diversi rispetto ai motori Synapse SQL. La tabella seguente mostra il mapping dei tipi di dati delle tabelle di Spark ai tipi SQL:

| Tipo di dati Spark | Tipo di dati SQL | Commenti |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Con regole di confronto SQL `Latin1_General_100_BIN2_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serializzazione in JSON con regole di confronto `Latin1_General_100_BIN2_UTF8` |
| `map`       |    `varchar(max)`   | Serializzazione in JSON con regole di confronto `Latin1_General_100_BIN2_UTF8` |
| `struct`    |    `varchar(max)`   | Serializzazione in JSON con regole di confronto `Latin1_General_100_BIN2_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Modello di protezione

Le tabelle e i database Spark, così come le relative rappresentazioni sincronizzate nel motore SQL, vengono protetti al livello di archiviazione sottostante. Poiché attualmente non hanno autorizzazioni sugli oggetti stessi, gli oggetti possono essere visualizzati in Esplora oggetti.

L'entità di sicurezza che crea una tabella gestita è considerata il proprietario di tale tabella e dispone di tutti i diritti sulla tabella e sulle cartelle e i file sottostanti. Inoltre, il proprietario del database diventa automaticamente co-proprietario della tabella.

Se si crea una tabella esterna di Spark o SQL con autenticazione pass-through, i dati vengono protetti solo a livello di cartella e file. Se un utente esegue una query su questo tipo di tabella esterna, l'identità di sicurezza dell'utente che ha inviato la query viene passata al file system, che verificherà i diritti di accesso.

Per altre informazioni su come impostare le autorizzazioni su cartelle e file, vedere [Database condiviso di Azure Synapse Analytics](database.md).

## <a name="examples"></a>Esempi

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Creare una tabella gestita basata su Parquet in Spark ed eseguire query dal pool SQL serverless

In questo scenario si ha un database Spark denominato `mytestdb`. Vedere [Creare e connettersi a un database Spark con il pool SQL serverless](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Creare una tabella di Spark gestita con SparkSQL eseguendo il comando seguente:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Questo comando crea la tabella `myParquetTable` nel database `mytestdb`. Dopo una breve attesa, la tabella viene visualizzata nel pool SQL serverless. Ad esempio, eseguire l'istruzione seguente dal pool SQL serverless.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Verificare che `myParquetTable` sia incluso nei risultati.

>[!NOTE]
>Una tabella il cui formato di archiviazione non è Parquet non verrà sincronizzata.

Inserire quindi alcuni valori nella tabella da Spark, ad esempio con le istruzioni Spark C# seguenti in un notebook C#:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

È ora possibile leggere i dati dal pool SQL serverless come segue:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Si dovrebbe ottenere la riga seguente come risultato:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Creare una tabella esterna basata su Parquet in Spark ed eseguire query dal pool SQL serverless

In questo esempio si crea una tabella Spark esterna sui file di dati in formato Parquet creati nell'esempio precedente per la tabella gestita.

Ad esempio, con SparkSQL eseguire:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Sostituire il segnaposto `<fs>` con il nome del file system predefinito dell'area di lavoro e il segnaposto `<synapse_ws>` con il nome dell'area di lavoro di Synapse usata per eseguire questo esempio.

L'esempio precedente crea la tabella `myExtneralParquetTable` nel database `mytestdb`. Dopo una breve attesa, la tabella viene visualizzata nel pool SQL serverless. Ad esempio, eseguire l'istruzione seguente dal pool SQL serverless.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Verificare che `myExternalParquetTable` sia incluso nei risultati.

È ora possibile leggere i dati dal pool SQL serverless come segue:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Si dovrebbe ottenere la riga seguente come risultato:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui metadati condivisi di Azure Synapse Analytics](overview.md)
- [Altre informazioni sul database dei metadati condivisi di Azure Synapse Analytics](database.md)


