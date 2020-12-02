---
title: Indici iperspaziali per Apache Spark
description: Ottimizzazione delle prestazioni per Apache Spark con indici Hyperspace
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/12/2020
ms.author: euang
ms.reviewer: euang
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 6795828f4fd3a77a7bc6d8de208848d5f4ee3bee
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501040"
---
# <a name="hyperspace-an-indexing-subsystem-for-apache-spark"></a>Iperspazio: un sottosistema di indicizzazione per Apache Spark

L'iperspazio offre la possibilità per gli utenti Apache Spark di creare indici sui set di impostazioni, ad esempio CSV, JSON e parquet, e di usarli per la potenziale accelerazione di query e carichi di lavoro.

In questo articolo vengono evidenziate le nozioni di base dell'iperspazio, ne viene enfatizzata la semplicità e viene illustrato come usarlo solo da chiunque.

Dichiarazione di non responsabilità: l'iperspazio consente di accelerare i carichi di lavoro o le query in due circostanze:

* Le query contengono filtri su predicati con selettività elevata. Ad esempio, potrebbe essere necessario selezionare 100 righe corrispondenti da un milione di righe candidate.
* Le query contengono un join che richiede shuffle pesanti. Ad esempio, potrebbe essere necessario aggiungere un set di dati da 100 GB a un set di dati da 10 GB.

È possibile monitorare attentamente i carichi di lavoro e determinare se l'indicizzazione è utile per caso.

Questo documento è disponibile anche in formato notebook per [Python](https://github.com/microsoft/hyperspace/blob/master/notebooks/python/Hitchhikers%20Guide%20to%20Hyperspace.ipynb), [C#](https://github.com/microsoft/hyperspace/blob/master/notebooks/csharp/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)e [scala](https://github.com/microsoft/hyperspace/blob/master/notebooks/scala/Hitchhikers%20Guide%20to%20Hyperspace.ipynb).

## <a name="setup"></a>Configurazione

Per iniziare, avviare una nuova sessione di Spark. Poiché questo documento è un'esercitazione semplicemente per illustrare l'offerta dell'iperspazio, è possibile apportare una modifica alla configurazione che consenta di evidenziare l'attività dell'iperspazio su set di dati di piccole dimensioni. 

Per impostazione predefinita, Spark usa il broadcast join per ottimizzare le query di join quando le dimensioni dei dati per un lato di join sono ridotte, ovvero il caso per i dati di esempio usati in questa esercitazione. Pertanto, i join broadcast vengono disabilitati in modo che, in un secondo momento, quando si eseguono query di join, Spark utilizzi sort-merge join. In particolare, viene illustrato come usare indici iperspazi su larga scala per accelerare le query di join.

L'output dell'esecuzione della cella seguente mostra un riferimento alla sessione Spark creata correttamente e stampa '-1' come valore per la configurazione join modificata, che indica che broadcast join è stato disabilitato.

:::zone pivot = "programming-language-scala"

```scala
// Start your Spark session
spark

// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

// Verify that BroadcastHashJoin is set correctly
println(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))

```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Start your Spark session.
spark

# Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

# Verify that BroadcastHashJoin is set correctly 
print(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.Conf().Set("spark.sql.autoBroadcastJoinThreshold", -1);

// Verify that BroadcastHashJoin is set correctly.
Console.WriteLine(spark.Conf().Get("spark.sql.autoBroadcastJoinThreshold"));
```

::: zone-end

Il risultato è il seguente:

```console
res3: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@297e957d
-1
```

## <a name="data-preparation"></a>Preparazione dei dati

Per preparare l'ambiente, è necessario creare record di dati di esempio e salvarli come file di dati parquet. Per illustrazione è usato parquet, ma è anche possibile usare altri formati, ad esempio CSV. Nelle celle successive si vedrà come creare diversi indici dell'iperspazio in questo set di dati di esempio e fare in modo che Spark li usi durante l'esecuzione delle query.

I record di esempio corrispondono a due set di dati: Department e Employee. È necessario configurare i percorsi "empLocation" e "deptLocation" in modo che nell'account di archiviazione puntino alla posizione desiderata per salvare i file di dati generati.

L'output dell'esecuzione della cella seguente mostra il contenuto dei set di dati come elenchi di tripletti seguiti dai riferimenti ai dataframe creati per salvare il contenuto di ogni set di dati nel percorso preferito.

:::zone pivot = "programming-language-scala"

```scala
import org.apache.spark.sql.DataFrame

// Sample department records
val departments = Seq(
      (10, "Accounting", "New York"),
      (20, "Research", "Dallas"),
      (30, "Sales", "Chicago"),
      (40, "Operations", "Boston"))

// Sample employee records
val employees = Seq(
      (7369, "SMITH", 20),
      (7499, "ALLEN", 30),
      (7521, "WARD", 30),
      (7566, "JONES", 20),
      (7698, "BLAKE", 30),
      (7782, "CLARK", 10),
      (7788, "SCOTT", 20),
      (7839, "KING", 10),
      (7844, "TURNER", 30),
      (7876, "ADAMS", 20),
      (7900, "JAMES", 30),
      (7934, "MILLER", 10),
      (7902, "FORD", 20),
      (7654, "MARTIN", 30))

// Save sample data in the Parquet format
import spark.implicits._
val empData: DataFrame = employees.toDF("empId", "empName", "deptId")
val deptData: DataFrame = departments.toDF("deptId", "deptName", "location")

val empLocation: String = "/<yourpath>/employees.parquet"       //TODO ** customize this location path **
val deptLocation: String = "/<yourpath>/departments.parquet"     //TODO ** customize this location path **
empData.write.mode("overwrite").parquet(empLocation)
deptData.write.mode("overwrite").parquet(deptLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql.types import StructField, StructType, StringType, IntegerType

# Sample department records
departments = [(10, "Accounting", "New York"), (20, "Research", "Dallas"), (30, "Sales", "Chicago"), (40, "Operations", "Boston")]

# Sample employee records
employees = [(7369, "SMITH", 20), (7499, "ALLEN", 30), (7521, "WARD", 30), (7566, "JONES", 20), (7698, "BLAKE", 30)]

# Create a schema for the dataframe
dept_schema = StructType([StructField('deptId', IntegerType(), True), StructField('deptName', StringType(), True), StructField('location', StringType(), True)])
emp_schema = StructType([StructField('empId', IntegerType(), True), StructField('empName', StringType(), True), StructField('deptId', IntegerType(), True)])

departments_df = spark.createDataFrame(departments, dept_schema)
employees_df = spark.createDataFrame(employees, emp_schema)

#TODO ** customize this location path **
emp_Location = "/<yourpath>/employees.parquet"
dept_Location = "/<yourpath>/departments.parquet"

employees_df.write.mode("overwrite").parquet(emp_Location)
departments_df.write.mode("overwrite").parquet(dept_Location)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Sql.Types;

// Sample department records
var departments = new List<GenericRow>()
{
    new GenericRow(new object[] {10, "Accounting", "New York"}),
    new GenericRow(new object[] {20, "Research", "Dallas"}),
    new GenericRow(new object[] {30, "Sales", "Chicago"}),
    new GenericRow(new object[] {40, "Operations", "Boston"})
};

// Sample employee records
var employees = new List<GenericRow>() {
      new GenericRow(new object[] {7369, "SMITH", 20}),
      new GenericRow(new object[] {7499, "ALLEN", 30}),
      new GenericRow(new object[] {7521, "WARD", 30}),
      new GenericRow(new object[] {7566, "JONES", 20}),
      new GenericRow(new object[] {7698, "BLAKE", 30}),
      new GenericRow(new object[] {7782, "CLARK", 10}),
      new GenericRow(new object[] {7788, "SCOTT", 20}),
      new GenericRow(new object[] {7839, "KING", 10}),
      new GenericRow(new object[] {7844, "TURNER", 30}),
      new GenericRow(new object[] {7876, "ADAMS", 20}),
      new GenericRow(new object[] {7900, "JAMES", 30}),
      new GenericRow(new object[] {7934, "MILLER", 10}),
      new GenericRow(new object[] {7902, "FORD", 20}),
      new GenericRow(new object[] {7654, "MARTIN", 30})
};

// Save sample data in the Parquet format
var departmentSchema = new StructType(new List<StructField>()
{
    new StructField("deptId", new IntegerType()),
    new StructField("deptName", new StringType()),
    new StructField("location", new StringType())
});
var employeeSchema = new StructType(new List<StructField>()
{
    new StructField("empId", new IntegerType()),
    new StructField("empName", new StringType()),
    new StructField("deptId", new IntegerType())
});

DataFrame empData = spark.CreateDataFrame(employees, employeeSchema); 
DataFrame deptData = spark.CreateDataFrame(departments, departmentSchema); 

string empLocation = "/<yourpath>/employees.parquet";       //TODO ** customize this location path **
string deptLocation = "/<yourpath>/departments.parquet";     //TODO ** customize this location path **
empData.Write().Mode("overwrite").Parquet(empLocation);
deptData.Write().Mode("overwrite").Parquet(deptLocation);

```

::: zone-end

Il risultato è il seguente:

```console
departments: Seq[(Int, String, String)] = List((10,Accounting,New York), (20,Research,Dallas), (30,Sales,Chicago), (40,Operations,Boston))  
employees: Seq[(Int, String, Int)] = List((7369,SMITH,20), (7499,ALLEN,30), (7521,WARD,30), (7566,JONES,20), (7698,BLAKE,30), (7782,CLARK,10), (7788,SCOTT,20), (7839,KING,10), (7844,TURNER,30), (7876,ADAMS,20), (7900,JAMES,30), (7934,MILLER,10), (7902,FORD,20), (7654,MARTIN,30))  

empData: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
empLocation: String = /your-path/employees.parquet  
deptLocation: String = /your-path/departments.parquet  
```

Verificare il contenuto dei file parquet creati per assicurarsi che contengano i record previsti nel formato corretto. In seguito, questi file di dati verranno usati per creare indici iperspaziali ed eseguire query di esempio.

L'esecuzione della cella seguente produce e restituisce un output che visualizza le righe nei dataframe dei dipendenti e dei Department in un formato tabulare. Dovrebbero essere presenti 14 dipendenti e 4 reparti, ognuno dei quali corrisponde a uno dei tripletti creati nella cella precedente.

:::zone pivot = "programming-language-scala"

```scala
// empLocation and deptLocation are the user defined locations above to save parquet files
val empDF: DataFrame = spark.read.parquet(empLocation)
val deptDF: DataFrame = spark.read.parquet(deptLocation)

// Verify the data is available and correct
empDF.show()
deptDF.show()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# emp_Location and dept_Location are the user-defined locations above to save parquet files
emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

# Verify the data is available and correct
emp_DF.show()
dept_DF.show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// empLocation and deptLocation are the user-defined locations above to save parquet files
DataFrame empDF = spark.Read().Parquet(empLocation);
DataFrame deptDF = spark.Read().Parquet(deptLocation);

// Verify the data is available and correct
empDF.Show();
deptDF.Show();

```

::: zone-end

Il risultato è il seguente:

```console
empDF: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDF: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

```console
|EmpId|EmpName|DeptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
| 7900|  JAMES|    30|
| 7934| MILLER|    10|
| 7839|   KING|    10|
| 7566|  JONES|    20|
| 7698|  BLAKE|    30|
| 7782|  CLARK|    10|
| 7788|  SCOTT|    20|
| 7902|   FORD|    20|
| 7654| MARTIN|    30|  
```

&nbsp; &nbsp;

```console
|DeptId|  DeptName|Location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="indexes"></a>Indici

L'iperspazio consente di creare indici nei record analizzati da file di dati salvati in modo permanente. Una volta creati correttamente, viene aggiunta una voce che corrisponde all'indice ai metadati dell'iperspazio. Questi metadati vengono usati in un secondo momento dall'ottimizzatore di Apache Spark (con le estensioni) durante l'elaborazione della query per trovare e usare gli indici appropriati.

Dopo la creazione degli indici, è possibile eseguire diverse operazioni:

* **Aggiornare se i dati sottostanti vengono modificati.** È possibile aggiornare un indice esistente per acquisire le modifiche.
* **Eliminare se l'indice non è necessario.** È possibile eseguire un'eliminazione temporanea, ovvero l'indice non viene eliminato fisicamente, ma è contrassegnato come "eliminato" in modo che non venga più usato nei carichi di lavoro.
* **Vacuum se un indice non è più necessario.** È possibile svuotare un indice, che forza un'eliminazione fisica del contenuto dell'indice e dei metadati associati completamente dai metadati dell'iperspazio.

Aggiornare se i dati sottostanti cambiano, è possibile aggiornare un indice esistente per acquisirlo.
Elimina se l'indice non è necessario, è possibile eseguire un'eliminazione temporanea, ovvero l'indice non viene eliminato fisicamente, ma è contrassegnato come ' deleted ' in modo che non venga più usato nei carichi di lavoro.

Le sezioni seguenti illustrano come è possibile eseguire tali operazioni di gestione degli indici nell'iperspazio.

Prima di tutto, è necessario importare le librerie necessarie e creare un'istanza di iperspazio. Successivamente, si userà questa istanza per richiamare diverse API dell'iperspazio per creare indici sui dati di esempio e modificare tali indici.

L'output dell'esecuzione della cella seguente mostra un riferimento all'istanza creata di iperspazio.

:::zone pivot = "programming-language-scala"

```scala
// Create an instance of Hyperspace
import com.microsoft.hyperspace._

val hyperspace: Hyperspace = Hyperspace()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
from hyperspace import *

# Create an instance of Hyperspace
hyperspace = Hyperspace(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create an instance of Hyperspace
using Microsoft.Spark.Extensions.Hyperspace;

Hyperspace hyperspace = new Hyperspace(spark);

```

::: zone-end

Il risultato è il seguente:

```console
hyperspace: com.microsoft.hyperspace.Hyperspace = com.microsoft.hyperspace.Hyperspace@1432f740
```

## <a name="create-indexes"></a>Creare indici

Per creare un indice iperspaziale, è necessario fornire due informazioni:

* Un frame di dati Spark che fa riferimento ai dati da indicizzare.
* Oggetto di configurazione dell'indice, IndexConfig, che specifica il nome dell'indice e le colonne indicizzate e incluse dell'indice.

Si inizia creando tre indici iperspaziali sui dati di esempio: due indici nel set di dati Department denominato "deptIndex1" e "deptIndex2" e un indice nel set di dati Employee denominato "empIndex". Per ogni indice è necessario un IndexConfig corrispondente per acquisire il nome insieme agli elenchi di colonne per le colonne indicizzate e incluse. L'esecuzione della cella seguente crea questi IndexConfigs e ne elenca l'output.

> [!Note]
> Una colonna di indice è una colonna visualizzata nei filtri o nelle condizioni di join. Una colonna inclusa è una colonna visualizzata in Select/Project.

Ad esempio, nella query seguente:

```sql
SELECT X
FROM T
WHERE Y = 2
```

Y può essere una colonna di indice e X può essere una colonna inclusa.

:::zone pivot = "programming-language-scala"

```scala
// Create index configurations
import com.microsoft.hyperspace.index.IndexConfig

val empIndexConfig: IndexConfig = IndexConfig("empIndex", Seq("deptId"), Seq("empName"))
val deptIndexConfig1: IndexConfig = IndexConfig("deptIndex1", Seq("deptId"), Seq("deptName"))
val deptIndexConfig2: IndexConfig = IndexConfig("deptIndex2", Seq("location"), Seq("deptName"))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create index configurations

emp_IndexConfig = IndexConfig("empIndex1", ["deptId"], ["empName"])
dept_IndexConfig1 = IndexConfig("deptIndex1", ["deptId"], ["deptName"])
dept_IndexConfig2 = IndexConfig("deptIndex2", ["location"], ["deptName"])

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Extensions.Hyperspace.Index;

var empIndexConfig = new IndexConfig("empIndex", new string[] {"deptId"}, new string[] {"empName"});
var deptIndexConfig1 = new IndexConfig("deptIndex1", new string[] {"deptId"}, new string[] {"deptName"});
var deptIndexConfig2 = new IndexConfig("deptIndex2", new string[] {"location"}, new string[] {"deptName"});

```

::: zone-end

Il risultato è il seguente:

```console
empIndexConfig: com.microsoft.hyperspace.index.IndexConfig = [indexName: empIndex; indexedColumns: deptid; includedColumns: empname]  
deptIndexConfig1: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex1; indexedColumns: deptid; includedColumns: deptname]  
deptIndexConfig2: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex2; indexedColumns: location; includedColumns: deptname]  
```
A questo punto, è possibile creare tre indici usando le configurazioni degli indici. A questo scopo, si richiama il comando "createIndex" nell'istanza di iperspazio. Questo comando richiede una configurazione dell'indice e il dataframe contenente le righe da indicizzare. L'esecuzione della cella seguente crea tre indici.

:::zone pivot = "programming-language-scala"

```scala
// Create indexes from configurations
import com.microsoft.hyperspace.index.Index

hyperspace.createIndex(empDF, empIndexConfig)
hyperspace.createIndex(deptDF, deptIndexConfig1)
hyperspace.createIndex(deptDF, deptIndexConfig2)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create indexes from configurations

hyperspace.createIndex(emp_DF, emp_IndexConfig)
hyperspace.createIndex(dept_DF, dept_IndexConfig1)
hyperspace.createIndex(dept_DF, dept_IndexConfig2)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create indexes from configurations
hyperspace.CreateIndex(empDF, empIndexConfig);
hyperspace.CreateIndex(deptDF, deptIndexConfig1);
hyperspace.CreateIndex(deptDF, deptIndexConfig2);

```

::: zone-end

## <a name="list-indexes"></a>Elencare gli indici

Il codice seguente illustra come è possibile elencare tutti gli indici disponibili in un'istanza di iperspazio. Usa l'API "Indexes" che restituisce informazioni sugli indici esistenti come dataframe di Spark, in modo da poter eseguire operazioni aggiuntive. 

Ad esempio, è possibile richiamare operazioni valide su questo dataframe per verificarne il contenuto o analizzarlo ulteriormente (ad esempio, filtrando gli indici specifici o raggruppando i dati in base a una proprietà desiderata).

La cella seguente usa l'azione ' Show ' di dataframe per stampare completamente le righe e visualizzare i dettagli degli indici in un formato tabulare. Per ogni indice, è possibile visualizzare tutte le informazioni che l'iperspazio ha archiviato nei metadati. Si noterà immediatamente quanto segue:

* config. IndexName, config. indexedColumns, config. includedColumns e status. status sono i campi a cui un utente fa normalmente riferimento.
* dfSignature viene generato automaticamente dall'iperspazio ed è univoco per ogni indice. L'iperspazio usa questa firma internamente per gestire l'indice e sfruttarlo in fase di query.


Nell'output seguente, tutti e tre gli indici devono essere "attivi" come stato e il nome, le colonne indicizzate e le colonne incluse devono corrispondere a quanto definito nelle configurazioni degli indici precedenti.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.Indexes().Show();

```

::: zone-end

Il risultato è il seguente:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="delete-indexes"></a>Elimina indici

È possibile eliminare un indice esistente usando l'API "deleteIndex" e specificando il nome dell'indice. L'eliminazione dell'indice esegue un'eliminazione temporanea: Aggiorna principalmente lo stato dell'indice nei metadati dell'iperspazio da "attivo" a "eliminato". Questa operazione escluderà l'indice eliminato da qualsiasi futura ottimizzazione delle query e l'iperspazio non sceglierà più l'indice per una query. 

Tuttavia, i file di indice per un indice eliminato rimangono ancora disponibili (poiché si tratta di un'eliminazione temporanea), in modo da poter ripristinare l'indice se richiesto dall'utente.

La cella seguente elimina l'indice con il nome "deptIndex2" e elenca i metadati dell'iperspazio dopo tale operazione. L'output dovrebbe essere simile alla cella precedente per "list Indexes", ad eccezione di "deptIndex2", che ora deve avere lo stato modificato in "DELETED".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
hyperspace.DeleteIndex("deptIndex2");

hyperspace.Indexes().Show();

```

::: zone-end

Il risultato è il seguente:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="restore-indexes"></a>Ripristina indici

È possibile usare l'API "restoreIndex" per ripristinare un indice eliminato. Verrà ripristinato lo stato attivo della versione più recente di index, che verrà nuovamente riutilizzabile per le query. La cella seguente mostra un esempio di utilizzo di "restoreIndex". Si elimina "deptIndex1" e lo si ripristina. L'output mostra che "deptIndex1" è stato inserito per la prima volta nello stato "eliminato" dopo aver richiamato il comando "deleteIndex" ed è stato restituito lo stato "attivo" dopo aver chiamato "restoreIndex".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex1")

hyperspace.indexes.show

hyperspace.restoreIndex("deptIndex1")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex1")
hyperspace.indexes().show()
hyperspace.restoreIndex("deptIndex1")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.DeleteIndex("deptIndex1");
hyperspace.Indexes().Show();
hyperspace.RestoreIndex("deptIndex1");
hyperspace.Indexes().Show();

```

::: zone-end

Il risultato è il seguente:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.indexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

&nbsp; &nbsp;

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="vacuum-indexes"></a>Indici sottovuoto

È possibile eseguire un'eliminazione hardware, ovvero rimuovere completamente i file e la voce di metadati per un indice eliminato usando il comando **vacuumIndex** . Questa azione è irreversibile. Elimina fisicamente tutti i file di indice, motivo per cui si tratta di un'eliminazione complessa.

La cella seguente consente di svuotare l'indice "deptIndex2" e di visualizzare i metadati dell'iperspazio dopo l'aspirapolvere. Verranno visualizzate le voci di metadati per due indici "deptIndex1" e "empIndex" con stato "attivo" e nessuna voce per "deptIndex2".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.vacuumIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.vacuumIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.VacuumIndex("deptIndex2");
hyperspace.Indexes().Show();

```

::: zone-end

Il risultato è il seguente:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="enable-or-disable-hyperspace"></a>Abilitare o disabilitare l'iperspazio

L'iperspazio fornisce API per abilitare o disabilitare l'utilizzo di indici con Spark.

* Usando il comando **enableHyperspace** , le regole di ottimizzazione dell'iperspazio diventano visibili a Spark Optimizer e sfruttano gli indici iperspaziali esistenti per ottimizzare le query utente.
* Utilizzando il comando **disableHyperspace** , le regole dell'iperspazio non vengono più applicate durante l'ottimizzazione della query. La disabilitazione dell'iperspazio non ha alcun effetto sugli indici creati perché rimangono intatti.

La cella seguente mostra come è possibile usare questi comandi per abilitare o disabilitare l'iperspazio. L'output Mostra un riferimento alla sessione Spark esistente la cui configurazione è aggiornata.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

// Disable Hyperspace
spark.disableHyperspace
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

# Disable Hyperspace
Hyperspace.disable(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

// Disable Hyperspace
spark.DisableHyperspace();

```

::: zone-end

Il risultato è il seguente:

```console
res48: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb  
res51: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb
```

## <a name="index-usage"></a>Utilizzo indici

Per fare in modo che Spark usi gli indici iperspaziali durante l'elaborazione della query, è necessario assicurarsi che l'iperspazio sia abilitato.

La cella seguente abilita l'iperspazio e crea due dataframe contenenti i record di dati di esempio, usati per l'esecuzione di query di esempio. Per ogni dataframe vengono stampate alcune righe di esempio.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

val empDFrame: DataFrame = spark.read.parquet(empLocation)
val deptDFrame: DataFrame = spark.read.parquet(deptLocation)

empDFrame.show(5)
deptDFrame.show(5)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

emp_DF.show(5)
dept_DF.show(5)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

DataFrame empDFrame = spark.Read().Parquet(empLocation);
DataFrame deptDFrame = spark.Read().Parquet(deptLocation);

empDFrame.Show(5);
deptDFrame.Show(5);

```

::: zone-end

Il risultato è il seguente:

```console
res53: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.Spark™Session@39fe1ddb  
empDFrame: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDFrame: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
```

&nbsp; &nbsp;

```console
|empId|empName|deptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
```

&nbsp;&nbsp;Mostra solo le prime 5 righe &nbsp;&nbsp;

```console
|deptId|  deptName|location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="index-types"></a>Tipi di indice

Attualmente, l'iperspazio ha regole per sfruttare gli indici per due gruppi di query:

* Query di selezione con predicati di filtro di selezione intervallo o ricerca.
* Join di query con un predicato di join di uguaglianza, ovvero equijoin.

## <a name="indexes-for-accelerating-filters"></a>Indici per accelerare i filtri

Nella prima query di esempio viene eseguita una ricerca nei record del reparto, come illustrato nella cella seguente. In SQL questa query è simile all'esempio seguente:

```sql
SELECT deptName
FROM departments
WHERE deptId = 20
```

L'output di esecuzione della cella seguente mostra:

* Risultato della query, che corrisponde a un singolo nome del reparto.
* Piano di query utilizzato da Spark per eseguire la query.

Nel piano di query, l'operatore **filescan** nella parte inferiore del piano indica l'origine dati da cui sono stati letti i record. Il percorso di questo file indica il percorso della versione più recente dell'indice "deptIndex1". Queste informazioni indicano che in base alla query e alle regole di ottimizzazione dell'iperspazio, Spark ha deciso di sfruttare l'indice appropriato in fase di esecuzione.

:::zone pivot = "programming-language-scala"

```scala
// Filter with equality predicate

val eqFilter: DataFrame = deptDFrame.filter("deptId = 20").select("deptName")
eqFilter.show()

eqFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with equality predicate

eqFilter = dept_DF.filter("""deptId = 20""").select("""deptName""")
eqFilter.show()

eqFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame eqFilter = deptDFrame.Filter("deptId = 20").Select("deptName");
eqFilter.Show();

eqFilter.Explain(true);

```

::: zone-end

Il risultato è il seguente:

```console
eqFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|DeptName|
|--------|
|Research|
```

&nbsp; &nbsp;

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), EqualTo(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

Il secondo esempio è una query di selezione dell'intervallo nei record del reparto. In SQL questa query è simile all'esempio seguente:

```sql
SELECT deptName
FROM departments
WHERE deptId > 20
```

Analogamente al primo esempio, l'output della cella seguente mostra i risultati della query (nomi di due reparti) e il piano di query. Il percorso del file di dati nell'operatore **filescan** indica che è stato utilizzato "deptIndex1" per eseguire la query.

:::zone pivot = "programming-language-scala"

```scala
// Filter with range selection predicate

val rangeFilter: DataFrame = deptDFrame.filter("deptId > 20").select("deptName")
rangeFilter.show()

rangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with range selection predicate

rangeFilter = dept_DF.filter("""deptId > 20""").select("deptName")
rangeFilter.show()

rangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Filter with range selection predicate
DataFrame rangeFilter = deptDFrame.Filter("deptId > 20").Select("deptName");
rangeFilter.Show();

rangeFilter.Explain(true);

```

::: zone-end

Il risultato è il seguente:

```console
rangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|  DeptName|
|----------|
|Operations|
|     Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```
Il terzo esempio è una query che unisce i record Department e Employee nell'ID del reparto. L'istruzione SQL equivalente è illustrata di seguito:

```sql
SELECT employees.deptId, empName, departments.deptId, deptName
FROM   employees, departments
WHERE  employees.deptId = departments.deptId
```
L'output dell'esecuzione della cella seguente mostra i risultati della query, ovvero i nomi di 14 dipendenti e il nome del reparto a cui lavorano ogni dipendente. Il piano di query viene inoltre incluso nell'output. Si noti che i percorsi dei file per due operatori **filescan** mostrano che Spark ha usato gli indici "empIndex" e "deptIndex1" per eseguire la query.

:::zone pivot = "programming-language-scala"

```scala
// Join

val eqJoin: DataFrame =
      empDFrame.
      join(deptDFrame, empDFrame("deptId") === deptDFrame("deptId")).
      select(empDFrame("empName"), deptDFrame("deptName"))

eqJoin.show()

eqJoin.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Join

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

eqJoin.show()

eqJoin.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Join
DataFrame eqJoin =
      empDFrame
      .Join(deptDFrame, empDFrame.Col("deptId") == deptDFrame.Col("deptId"))
      .Select(empDFrame.Col("empName"), deptDFrame.Col("deptName"));

eqJoin.Show();

eqJoin.Explain(true);

```

::: zone-end

Il risultato è il seguente:

```console
eqJoin: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534] parquet

== Physical Plan ==
*(3) Project [empName#528, deptName#534]
+- *(3) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>, SelectedBucketsCount: 200 out of 200
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>, SelectedBucketsCount: 200 out of 200
```

## <a name="support-for-sql-semantics"></a>Supporto per la semantica SQL

L'utilizzo dell'indice è trasparente se si usa l'API dataframe o Spark SQL. Nell'esempio seguente viene illustrato lo stesso esempio di join di prima, in SQL form, che mostra l'utilizzo di indici, se applicabile.

:::zone pivot = "programming-language-scala"

```scala
empDFrame.createOrReplaceTempView("EMP")
deptDFrame.createOrReplaceTempView("DEPT")

val joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql import SparkSession

emp_DF.createOrReplaceTempView("EMP")
dept_DF.createOrReplaceTempView("DEPT")

joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

empDFrame.CreateOrReplaceTempView("EMP");
deptDFrame.CreateOrReplaceTempView("DEPT");

var joinQuery = spark.Sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId");

joinQuery.Show();
joinQuery.Explain(true);

```

::: zone-end

Il risultato è il seguente:

```console

joinQuery: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
'Project ['EMP.empName, 'DEPT.deptName]
+- 'Filter ('EMP.deptId = 'DEPT.deptId)
   +- 'Join Inner
      :- 'UnresolvedRelation `EMP`
      +- 'UnresolvedRelation `DEPT`

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Filter (deptId#529 = deptId#533)
   +- Join Inner
      :- SubqueryAlias `emp`
      :  +- Relation[empId#527,empName#528,deptId#529] parquet
      +- SubqueryAlias `dept`
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empId#527,empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(5) Project [empName#528, deptName#534]
+- *(5) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="explain-api"></a>API explain

Gli indici sono molto interessanti, ma come si sa se vengono usati? L'iperspazio consente agli utenti di confrontare il piano originale rispetto al piano dipendente dall'indice aggiornato prima di eseguire la query. Per visualizzare l'output del comando, è possibile scegliere tra la modalità HTML, testo normale o console.

Nella cella seguente viene illustrato un esempio con HTML. La sezione evidenziata rappresenta la differenza tra i piani originali e quelli aggiornati insieme agli indici utilizzati.

:::zone pivot = "programming-language-scala"

```scala
spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin)(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin, True, displayHTML)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

spark.Conf().Set("spark.hyperspace.explain.displayMode", "html");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.beginTag", "<b style=\"background:LightGreen\">");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.endTag", "</b>");

hyperspace.Explain(eqJoin, false, input => DisplayHTML(input));

```

::: zone-end

Il risultato è il seguente:

### <a name="plan-with-indexes"></a>Pianificare con indici

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="plan-without-indexes"></a>Pianificare senza indici

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [empName#528,deptId#529] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/employees.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="indexes-used"></a>Indici utilizzati

```console
deptIndex1:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/deptIndex1/v__=0
empIndex:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/empIndex/v__=0
```

## <a name="refresh-indexes"></a>Aggiorna indici

Se vengono modificati i dati originali in cui è stato creato un indice, l'indice non acquisisce più lo stato più recente dei dati. È possibile aggiornare un indice non aggiornato tramite il comando **refreshIndex** . Questo comando fa in modo che l'indice venga ricompilato completamente e lo aggiorni in base ai record di dati più recenti. Verrà illustrato come aggiornare in modo incrementale l'indice in altri notebook.

Nelle due celle seguenti viene illustrato un esempio per questo scenario:

* La prima cella aggiunge altri due reparti ai dati dei reparti originali. Viene letto e stampato un elenco di reparti per verificare che i nuovi reparti siano stati aggiunti correttamente. L'output mostra sei reparti in totale: quattro vecchi e due nuovi. La chiamata di **refreshIndex** Aggiorna "deptIndex1" in modo che l'indice acquisisca i nuovi reparti.
* La seconda cella esegue l'esempio di query di selezione intervallo. I risultati dovrebbero ora contenere quattro reparti: due sono quelle rilevate prima quando è stata eseguita la query precedente e due sono i nuovi reparti aggiunti.

### <a name="specific-index-refresh"></a>Aggiornamento indice specifico

:::zone pivot = "programming-language-scala"

```scala
val extraDepartments = Seq(
      (50, "Inovation", "Seattle"),
      (60, "Human Resources", "San Francisco"))

val extraDeptData: DataFrame = extraDepartments.toDF("deptId", "deptName", "location")
extraDeptData.write.mode("Append").parquet(deptLocation)

val deptDFrameUpdated: DataFrame = spark.read.parquet(deptLocation)

deptDFrameUpdated.show(10)

hyperspace.refreshIndex("deptIndex1")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

extra_Departments = [(50, "Inovation", "Seattle"), (60, "Human Resources", "San Francisco")]

extra_departments_df = spark.createDataFrame(extra_Departments, dept_schema)
extra_departments_df.write.mode("Append").parquet(dept_Location)


dept_DFrame_Updated = spark.read.parquet(dept_Location)

dept_DFrame_Updated.show(10)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

var extraDepartments = new List<GenericRow>()
{
    new GenericRow(new object[] {50, "Inovation", "Seattle"}),
    new GenericRow(new object[] {60, "Human Resources", "San Francisco"})
};
    
DataFrame extraDeptData = spark.CreateDataFrame(extraDepartments, departmentSchema);
extraDeptData.Write().Mode("Append").Parquet(deptLocation);

DataFrame deptDFrameUpdated = spark.Read().Parquet(deptLocation);

deptDFrameUpdated.Show(10);

hyperspace.RefreshIndex("deptIndex1");

```

::: zone-end

Il risultato è il seguente:

```console
extraDepartments: Seq[(Int, String, String)] = List((50,Inovation,Seattle), (60,Human Resources,San Francisco))  
extraDeptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
deptDFrameUpdated: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

&nbsp; &nbsp;

```console  
|deptId|       deptName|     location|
|------|---------------|-------------|
|    60|Human Resources|San Francisco|
|    10|     Accounting|     New York|
|    50|      Inovation|      Seattle|
|    40|     Operations|       Boston|
|    20|       Research|       Dallas|
|    30|          Sales|      Chicago|
```

### <a name="range-selection"></a>Selezione intervallo

:::zone pivot = "programming-language-scala"

```scala
val newRangeFilter: DataFrame = deptDFrameUpdated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

newRangeFilter = dept_DFrame_Updated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame newRangeFilter = deptDFrameUpdated.Filter("deptId > 20").Select("deptName");
newRangeFilter.Show();

newRangeFilter.Explain(true);

```

::: zone-end

Il risultato è il seguente:

```console
newRangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|       DeptName|
|---------------|
|Human Resources|
|      Inovation|
|     Operations|
|          Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#675]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Optimized Logical Plan ==
Project [deptName#675]
+- Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Physical Plan ==
*(1) Project [deptName#675]
+- *(1) Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- *(1) FileScan parquet [deptId#674,deptName#675] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="hybrid-scan-for-mutable-datasets"></a>Analisi ibrida per i set di impostazioni di DataSet modificabili

Spesso, se ai dati di origine sottostanti sono stati aggiunti nuovi file o file esistenti eliminati, l'indice verrà aggiornato e l'iperspazio viene deciso di non utilizzarlo. Tuttavia, in alcuni casi è sufficiente utilizzare l'indice senza che sia necessario aggiornarlo ogni volta. Questa operazione può essere dovuta a diversi motivi:

- Non si vuole aggiornare continuamente l'indice, ma si vuole eseguire questa operazione periodicamente perché si comprende meglio i carichi di lavoro.
- Sono stati aggiunti o rimossi solo pochi file e non si desidera attendere il completamento di un altro processo di aggiornamento.

Per consentire all'utente di usare ancora un indice obsoleto, l'iperspazio introduce un'analisi ibrida, una tecnica nuova che consente agli utenti di utilizzare indici obsoleti o non aggiornati (ad esempio, i dati di origine sottostanti hanno aggiunto nuovi file o file esistenti eliminati) senza aggiornare gli indici.

A tale scopo, quando si imposta la configurazione appropriata per abilitare l'analisi ibrida, l'iperspazio modifica il piano di query per sfruttare le modifiche come segue:
* I file accodati possono essere Uniti per indicizzare i dati tramite Union o BucketUnion (per join). Se necessario, è anche possibile applicare il rimescolamento dei dati accodati prima dell'Unione.
* I file eliminati possono essere gestiti inserendo la condizione Filter-NOT-IN sulla colonna di derivazione dei dati di indice, in modo che le righe indicizzate dai file eliminati possano essere escluse in fase di query.

È possibile controllare la trasformazione del piano di query negli esempi seguenti.

> [!NOTE]
> Attualmente, l'analisi ibrida è supportata solo per i dati non partizionati.

### <a name="hybrid-scan-for-appended-files---non-partitioned-data"></a>Analisi ibrida per file accodati-dati non partizionati

Nell'esempio seguente vengono utilizzati dati non partizionati. In questo esempio si prevede che l'indice di join possa essere usato per la query e che BucketUnion sia stato introdotto per i file accodati.

:::zone pivot = "programming-language-scala"

```scala
val testData = Seq(
    ("orange", 3, "2020-10-01"),
    ("banana", 1, "2020-10-01"),
    ("carrot", 5, "2020-10-02"),
    ("beetroot", 12, "2020-10-02"),
    ("orange", 2, "2020-10-03"),
    ("banana", 11, "2020-10-03"),
    ("carrot", 3, "2020-10-03"),
    ("beetroot", 2, "2020-10-04"),
    ("cucumber", 7, "2020-10-05"),
    ("pepper", 20, "2020-10-06")
    ).toDF("name", "qty", "date")

val testDataLocation = s"$dataPath/productTable"

testData.write.mode("overwrite").parquet(testDataLocation)
val testDF = spark.read.parquet(testDataLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
testdata = [
    ("orange", 3, "2020-10-01"),
    ("banana", 1, "2020-10-01"),
    ("carrot", 5, "2020-10-02"),
    ("beetroot", 12, "2020-10-02"),
    ("orange", 2, "2020-10-03"),
    ("banana", 11, "2020-10-03"),
    ("carrot", 3, "2020-10-03"),
    ("beetroot", 2, "2020-10-04"),
    ("cucumber", 7, "2020-10-05"),
    ("pepper", 20, "2020-10-06")
]

testdata_location = data_path + "/productTable"
from pyspark.sql.types import StructField, StructType, StringType, IntegerType
testdata_schema = StructType([
    StructField('name', StringType(), True),
    StructField('qty', IntegerType(), True),
    StructField('date', StringType(), True)])

test_df = spark.createDataFrame(testdata, testdata_schema)
test_df.write.mode("overwrite").parquet(testdata_location)
test_df = spark.read.parquet(testdata_location)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Sql.Types;

var products = new List<GenericRow>() {
    new GenericRow(new object[] {"orange", 3, "2020-10-01"}),
    new GenericRow(new object[] {"banana", 1, "2020-10-01"}),
    new GenericRow(new object[] {"carrot", 5, "2020-10-02"}),
    new GenericRow(new object[] {"beetroot", 12, "2020-10-02"}),
    new GenericRow(new object[] {"orange", 2, "2020-10-03"}),
    new GenericRow(new object[] {"banana", 11, "2020-10-03"}),
    new GenericRow(new object[] {"carrot", 3, "2020-10-03"}),
    new GenericRow(new object[] {"beetroot", 2, "2020-10-04"}),
    new GenericRow(new object[] {"cucumber", 7, "2020-10-05"}),
    new GenericRow(new object[] {"pepper", 20, "2020-10-06"})
};
var productsSchema = new StructType(new List<StructField>()
{
    new StructField("name", new StringType()),
    new StructField("qty", new IntegerType()),
    new StructField("date", new StringType())
});

DataFrame testData = spark.CreateDataFrame(products, productsSchema); 
string testDataLocation = $"{dataPath}/productTable";
testData.Write().Mode("overwrite").Parquet(testDataLocation);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
// CREATE INDEX
hyperspace.createIndex(testDF, IndexConfig("productIndex2", Seq("name"), Seq("date", "qty")))

spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)
val filter1 = testDF.filter("name = 'banana'")
val filter2 = testDF.filter("qty > 10")
val query = filter1.join(filter2, "name")

// Check Join index rule is applied properly.
hyperspace.explain(query)(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# CREATE INDEX
hyperspace.createIndex(test_df, IndexConfig("productIndex2", ["name"], ["date", "qty"]))

spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)
filter1 = test_df.filter("name = 'banana'")
filter2 = test_df.filter("qty > 10")
query = filter1.join(filter2, "name")

# Check Join index rule is applied properly.
hyperspace.explain(query, True, displayHTML)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// CREATE INDEX
DataFrame testDF = spark.Read().Parquet(testDataLocation);
var productIndex2Config = new IndexConfig("productIndex", new string[] {"name"}, new string[] {"date", "qty"});
hyperspace.CreateIndex(testDF, productIndex2Config);

// Check Join index rule is applied properly.
DataFrame filter1 = testDF.Filter("name = 'banana'");
DataFrame filter2 = testDF.Filter("qty > 10");
DataFrame query = filter1.Join(filter2, filter1.Col("name") == filter2.Col("name"));

query.Show();

hyperspace.Explain(query, true, input => DisplayHTML(input));
```

::: zone-end

Risultato:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#607, qty#608, date#609, qty#632, date#633]
+- SortMergeJoin [name#607], [name#631], Inner
   :- *(1) Project [name#607, qty#608, date#609]
   :  +- *(1) Filter (isnotnull(name#607) && (name#607 = banana))
   :     +- *(1) FileScan parquet [name#607,date#609,qty#608] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Project [name#631, qty#632, date#633]
      +- *(2) Filter (((isnotnull(qty#632) && (qty#632 > 10)) && isnotnull(name#631)) && (name#631 = banana))
         +- *(2) FileScan parquet [name#631,date#633,qty#632] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#607, qty#608, date#609, qty#632, date#633]
+- SortMergeJoin [name#607], [name#631], Inner
   :- *(2) Sort [name#607 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#607, 200), [id=#453]
   :     +- *(1) Project [name#607, qty#608, date#609]
   :        +- *(1) Filter (isnotnull(name#607) && (name#607 = banana))
   :           +- *(1) FileScan parquet [name#607,qty#608,date#609] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#631 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#631, 200), [id=#459]
         +- *(3) Project [name#631, qty#632, date#633]
            +- *(3) Filter (((isnotnull(qty#632) && (qty#632 > 10)) && isnotnull(name#631)) && (name#631 = banana))
               +- *(3) FileScan parquet [name#631,qty#632,date#633] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=0

```

:::zone pivot = "programming-language-scala"

```scala
// Append new files.
val appendData = Seq(
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")).toDF("name", "qty", "date")
appendData.write.mode("append").parquet(testDataLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Append new files.
append_data = [
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")
]
append_df = spark.createDataFrame(append_data, testdata_schema)
append_df.write.mode("append").parquet(testdata_location)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Append new files.
var appendProducts = new List<GenericRow>()
{
    new GenericRow(new object[] {"orange", 13, "2020-11-01"}),
    new GenericRow(new object[] {"banana", 5, "2020-11-01"})
};
    
DataFrame appendData = spark.CreateDataFrame(appendProducts, productsSchema);
appendData.Write().Mode("Append").Parquet(testDataLocation);

```

::: zone-end

Per impostazione predefinita, l'analisi ibrida è disabilitata. Pertanto, si noterà che, poiché i nuovi dati sono stati accodati, l'iperspazio si decide di *non* usare l'indice.

Nell'output non viene visualizzata alcuna differenza di piano (pertanto, Nessuna evidenziazione).

:::zone pivot = "programming-language-scala"

```scala
// Hybrid Scan configs are false by default.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "false")
spark.conf.set("spark.hyperspace.index.hybridscan.delete.enabled", "false")

val testDFWithAppend = spark.read.parquet(testDataLocation)
val filter1 = testDFWithAppend.filter("name = 'banana'")
val filter2 = testDFWithAppend.filter("qty > 10")
val query = filter1.join(filter2, "name")
hyperspace.explain(query)(displayHTML(_))
query.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Hybrid Scan configs are false by default.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "false")
spark.conf.set("spark.hyperspace.index.hybridscan.delete.enabled", "false")

test_df_with_append = spark.read.parquet(testdata_location)
filter1 = test_df_with_append.filter("name = 'banana'")
filter2 = test_df_with_append.filter("qty > 10")
query = filter1.join(filter2, "name")
hyperspace.explain(query, True, displayHTML)
query.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Hybrid Scan configs are false by default.
spark.Conf().Set("spark.hyperspace.index.hybridscan.enabled", "false");
spark.Conf().Set("spark.hyperspace.index.hybridscan.delete.enabled", "false");

DataFrame testDFWithAppend = spark.Read().Parquet(testDataLocation);
DataFrame filter1 = testDFWithAppend.Filter("name = 'banana'");
DataFrame filter2 = testDFWithAppend.Filter("qty > 10");
DataFrame query = filter1.Join(filter2, filter1.Col("name") == filter2.Col("name"));

query.Show();

hyperspace.Explain(query, true, input => DisplayHTML(input));
```

::: zone-end

Risultato:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#685, date#686]
+- SortMergeJoin [name#678], [name#684], Inner
   :- *(2) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#678, 200), [id=#589]
   :     +- *(1) Project [name#678, qty#679, date#680]
   :        +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :           +- *(1) FileScan parquet [name#678,qty#679,date#680] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#684 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#684, 200), [id=#595]
         +- *(3) Project [name#684, qty#685, date#686]
            +- *(3) Filter (((isnotnull(qty#685) && (qty#685 > 10)) && (name#684 = banana)) && isnotnull(name#684))
               +- *(3) FileScan parquet [name#684,qty#685,date#686] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct

=============================================================
Plan without indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#685, date#686]
+- SortMergeJoin [name#678], [name#684], Inner
   :- *(2) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#678, 200), [id=#536]
   :     +- *(1) Project [name#678, qty#679, date#680]
   :        +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :           +- *(1) FileScan parquet [name#678,qty#679,date#680] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#684 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#684, 200), [id=#542]
         +- *(3) Project [name#684, qty#685, date#686]
            +- *(3) Filter (((isnotnull(qty#685) && (qty#685 > 10)) && (name#684 = banana)) && isnotnull(name#684))
               +- *(3) FileScan parquet [name#684,qty#685,date#686] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct

+------+---+----------+---+----------+
|  name|qty|      date|qty|      date|
+------+---+----------+---+----------+
|banana| 11|2020-10-03| 11|2020-10-03|
|banana|  5|2020-11-01| 11|2020-10-03|
|banana|  1|2020-10-01| 11|2020-10-03|
+------+---+----------+---+----------
```

### <a name="enable-hybrid-scan"></a>Abilita analisi ibrida

In piano con indici, è possibile visualizzare il partizionamento hash di Exchange necessario solo per i file accodati, in modo che sia ancora possibile utilizzare i dati di indice "shuffled" con i file accodati. BucketUnion viene utilizzato per unire i file accodati "shuffled" con i dati dell'indice.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hybrid Scan config. "delete" config is not necessary since we only appended data.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "true")
spark.enableHyperspace
// Need to redefine query to recalculate the query plan.
val query = filter1.join(filter2, "name")
hyperspace.explain(query)(displayHTML(_))
query.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Enable Hybrid Scan config. "delete" config is not necessary.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "true")

# Need to redefine query to recalculate the query plan.
query = filter1.join(filter2, "name")
hyperspace.explain(query, True, displayHTML)
query.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Enable Hybrid Scan config. "delete" config is not necessary.
spark.Conf().Set("spark.hyperspace.index.hybridscan.enabled", "true");
spark.EnableHyperspace();
// Need to redefine query to recalculate the query plan.
DataFrame query = filter1.Join(filter2, filter1.Col("name") == filter2.Col("name"));

query.Show();

hyperspace.Explain(query, true, input => DisplayHTML(input));
```

::: zone-end

Risultato:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#732, date#733]
+- SortMergeJoin [name#678], [name#731], Inner
   :- *(3) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- BucketUnion 200 buckets, bucket columns: [name]
   :     :- *(1) Project [name#678, qty#679, date#680]
   :     :  +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :     :     +- *(1) FileScan parquet [name#678,date#680,qty#679] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   :     +- Exchange hashpartitioning(name#678, 200), [id=#775]
   :        +- *(2) Project [name#678, qty#679, date#680]
   :           +- *(2) Filter (isnotnull(name#678) && (name#678 = banana))
   :              +- *(2) FileScan parquet [name#678,date#680,qty#679] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(6) Sort [name#731 ASC NULLS FIRST], false, 0
      +- BucketUnion 200 buckets, bucket columns: [name]
         :- *(4) Project [name#731, qty#732, date#733]
         :  +- *(4) Filter (((isnotnull(qty#732) && (qty#732 > 10)) && isnotnull(name#731)) && (name#731 = banana))
         :     +- *(4) FileScan parquet [name#731,date#733,qty#732] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
         +- Exchange hashpartitioning(name#731, 200), [id=#783]
            +- *(5) Project [name#731, qty#732, date#733]
               +- *(5) Filter (((isnotnull(qty#732) && (qty#732 > 10)) && isnotnull(name#731)) && (name#731 = banana))
                  +- *(5) FileScan parquet [name#731,date#733,qty#732] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Plan without indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#732, date#733]
+- SortMergeJoin [name#678], [name#731], Inner
   :- *(2) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#678, 200), [id=#701]
   :     +- *(1) Project [name#678, qty#679, date#680]
   :        +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :           +- *(1) FileScan parquet [name#678,qty#679,date#680] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#731 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#731, 200), [id=#707]
         +- *(3) Project [name#731, qty#732, date#733]
            +- *(3) Filter (((isnotnull(qty#732) && (qty#732 > 10)) && isnotnull(name#731)) && (name#731 = banana))
               +- *(3) FileScan parquet [name#731,qty#732,date#733] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=0


+------+---+----------+---+----------+
|  name|qty|      date|qty|      date|
+------+---+----------+---+----------+
|banana|  1|2020-10-01| 11|2020-10-03|
|banana| 11|2020-10-03| 11|2020-10-03|
|banana|  5|2020-11-01| 11|2020-10-03|
+------+---+----------+---+----------+
```

## <a name="incremental-index-refresh"></a>Aggiornamento indice incrementale

Quando si è pronti ad aggiornare gli indici, ma non si vuole ricompilare l'intero indice, l'iperspazio supporta l'aggiornamento degli indici in modo incrementale usando l' `hs.refreshIndex("name", "incremental")` API. In questo modo si elimina la necessità di una ricompilazione completa dell'indice da zero, utilizzando i file di indice creati in precedenza e aggiornando gli indici solo sui dati appena aggiunti.

Naturalmente, assicurarsi di usare periodicamente l'API complementare `optimizeIndex` (mostrata di seguito) per assicurarsi di non visualizzare le regressioni delle prestazioni. Si consiglia di chiamare Optimize almeno una volta ogni 10 volte che si chiama `refreshIndex(..., "incremental")` , presupponendo che i dati aggiunti/rimossi siano < 10% del set di dati originale. Se, ad esempio, il set di dati originale è 100 GB e sono stati aggiunti o rimossi dati con incrementi/decrementi di 1 GB, è possibile chiamare `refreshIndex` 10 volte prima di chiamare `optimizeIndex` . Si noti che questo esempio viene usato semplicemente per illustrazione ed è necessario adattarlo per i carichi di lavoro.

Nell'esempio seguente si noti l'aggiunta di un nodo di ordinamento nel piano di query quando vengono utilizzati gli indici. Ciò è dovuto al fatto che gli indici parziali vengono creati nei file di dati accodati, facendo in modo che Spark introduca un `Sort` . Si noti anche che `Shuffle` , ad esempio, Exchange è ancora stato eliminato dal piano, garantendo l'accelerazione appropriata.

:::zone pivot = "programming-language-scala"

```scala
def query(): DataFrame = {
    val testDFWithAppend = spark.read.parquet(testDataLocation)
    val filter1 = testDFWithAppend.filter("name = 'banana'")
    val filter2 = testDFWithAppend.filter("qty > 10")
    filter1.join(filter2, "name")
}

hyperspace.refreshIndex("productIndex2", "incremental")

hyperspace.explain(query())(displayHTML(_))
query().show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
def query():
    test_df_with_append = spark.read.parquet(testdata_location)
    filter1 = test_df_with_append.filter("name = 'banana'")
    filter2 = test_df_with_append.filter("qty > 10")
    return filter1.join(filter2, "name")

hyperspace.refreshIndex("productIndex2", "incremental")

hyperspace.explain(query(), True, displayHTML)
query().show()
```

::: zone-end

Risultato:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#820, qty#821, date#822, qty#827, date#828]
+- SortMergeJoin [name#820], [name#826], Inner
   :- *(1) Sort [name#820 ASC NULLS FIRST], false, 0
   :  +- *(1) Project [name#820, qty#821, date#822]
   :     +- *(1) Filter (isnotnull(name#820) && (name#820 = banana))
   :        +- *(1) FileScan parquet [name#820,date#822,qty#821] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Sort [name#826 ASC NULLS FIRST], false, 0
      +- *(2) Project [name#826, qty#827, date#828]
         +- *(2) Filter (((isnotnull(qty#827) && (qty#827 > 10)) && (name#826 = banana)) && isnotnull(name#826))
            +- *(2) FileScan parquet [name#826,date#828,qty#827] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#820, qty#821, date#822, qty#827, date#828]
+- SortMergeJoin [name#820], [name#826], Inner
   :- *(2) Sort [name#820 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#820, 200), [id=#927]
   :     +- *(1) Project [name#820, qty#821, date#822]
   :        +- *(1) Filter (isnotnull(name#820) && (name#820 = banana))
   :           +- *(1) FileScan parquet [name#820,qty#821,date#822] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#826 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#826, 200), [id=#933]
         +- *(3) Project [name#826, qty#827, date#828]
            +- *(3) Filter (((isnotnull(qty#827) && (qty#827 > 10)) && (name#826 = banana)) && isnotnull(name#826))
               +- *(3) FileScan parquet [name#826,qty#827,date#828] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct

+------+---+----------+---+----------+
|  name|qty|      date|qty|      date|
+------+---+----------+---+----------+
|banana|  1|2020-10-01| 11|2020-10-03|
|banana| 11|2020-10-03| 11|2020-10-03|
|banana|  5|2020-11-01| 11|2020-10-03|
+------+---+----------+---+----------+
```

## <a name="optimize-index-layout"></a>Ottimizza layout Indice

Dopo la chiamata di aggiornamenti incrementali più volte sui dati appena accodati (ad esempio, se l'utente scrive i dati in piccoli batch o in caso di scenari di flusso), il numero di file di indice tende a diventare grande influisce sulle prestazioni dell'indice (numero elevato di problemi di file piccoli). L'iperspazio fornisce `hyperspace.optimizeIndex("indexName")` API per ottimizzare il layout degli indici e ridurre il problema dei file di grandi dimensioni.

Nel piano riportato di seguito, si noti che l'iperspazio ha rimosso il nodo di ordinamento aggiuntivo nel piano di query. Optimize consente di evitare l'ordinamento per qualsiasi bucket di indice che contenga un solo file. Tuttavia, questo sarà vero solo se tutti i bucket di indice hanno al massimo 1 file per bucket, dopo `optimizeIndex` .

:::zone pivot = "programming-language-scala"

```scala
// Append some more data and call refresh again.
val appendData = Seq(
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")).toDF("name", "qty", "date")
appendData.write.mode("append").parquet(testDataLocation)

hyperspace.refreshIndex("productIndex2", "incremental")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Append some more data and call refresh again.
append_data = [
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")
]
append_df = spark.createDataFrame(append_data, testdata_schema)
append_df.write.mode("append").parquet(testdata_location)

hyperspace.refreshIndex("productIndex2", "incremental"
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
// Call optimize. Ensure that Sort is removed after optimization (This is possible here because after optimize, in this case, every bucket contains only 1 file.).
hyperspace.optimizeIndex("productIndex2")

hyperspace.explain(query())(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Call optimize. Ensure that Sort is removed after optimization (This is possible here because after optimize, in this case, every bucket contains only 1 file.).
hyperspace.optimizeIndex("productIndex2")

hyperspace.explain(query(), True, displayHTML)
```

::: zone-end

Risultato:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#954, qty#955, date#956, qty#961, date#962]
+- SortMergeJoin [name#954], [name#960], Inner
   :- *(1) Project [name#954, qty#955, date#956]
   :  +- *(1) Filter (isnotnull(name#954) && (name#954 = banana))
   :     +- *(1) FileScan parquet [name#954,date#956,qty#955] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Project [name#960, qty#961, date#962]
      +- *(2) Filter (((isnotnull(qty#961) && (qty#961 > 10)) && isnotnull(name#960)) && (name#960 = banana))
         +- *(2) FileScan parquet [name#960,date#962,qty#961] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#954, qty#955, date#956, qty#961, date#962]
+- SortMergeJoin [name#954], [name#960], Inner
   :- *(2) Sort [name#954 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#954, 200), [id=#1070]
   :     +- *(1) Project [name#954, qty#955, date#956]
   :        +- *(1) Filter (isnotnull(name#954) && (name#954 = banana))
   :           +- *(1) FileScan parquet [name#954,qty#955,date#956] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#960 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#960, 200), [id=#1076]
         +- *(3) Project [name#960, qty#961, date#962]
            +- *(3) Filter (((isnotnull(qty#961) && (qty#961 > 10)) && isnotnull(name#960)) && (name#960 = banana))
               +- *(3) FileScan parquet [name#960,qty#961,date#962] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=3
```

### <a name="optimize-modes"></a>Ottimizza modalità

La modalità predefinita per l'ottimizzazione è la modalità "rapida" in cui i file più piccoli rispetto a una soglia predefinita vengono selezionati per l'ottimizzazione. Per ottimizzare l'effetto dell'ottimizzazione, l'iperspazio consente un'altra modalità di ottimizzazione "completa", come illustrato di seguito. Questa modalità Seleziona tutti i file di indice per l'ottimizzazione indipendentemente dalle dimensioni del file e crea il migliore layout possibile dell'indice. Questo è anche più lento rispetto alla modalità di ottimizzazione predefinita, perché sono in corso l'elaborazione di più dati.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.optimizeIndex("productIndex2", "full")

hyperspace.explain(query())(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
hyperspace.optimizeIndex("productIndex2", "full")

hyperspace.explain(query(), True, displayHTML)
```

::: zone-end

Risultato:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#1000, qty#1001, date#1002, qty#1007, date#1008]
+- SortMergeJoin [name#1000], [name#1006], Inner
   :- *(1) Project [name#1000, qty#1001, date#1002]
   :  +- *(1) Filter (isnotnull(name#1000) && (name#1000 = banana))
   :     +- *(1) FileScan parquet [name#1000,date#1002,qty#1001] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Project [name#1006, qty#1007, date#1008]
      +- *(2) Filter (((isnotnull(qty#1007) && (qty#1007 > 10)) && isnotnull(name#1006)) && (name#1006 = banana))
         +- *(2) FileScan parquet [name#1006,date#1008,qty#1007] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#1000, qty#1001, date#1002, qty#1007, date#1008]
+- SortMergeJoin [name#1000], [name#1006], Inner
   :- *(2) Sort [name#1000 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#1000, 200), [id=#1160]
   :     +- *(1) Project [name#1000, qty#1001, date#1002]
   :        +- *(1) Filter (isnotnull(name#1000) && (name#1000 = banana))
   :           +- *(1) FileScan parquet [name#1000,qty#1001,date#1002] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#1006 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#1006, 200), [id=#1166]
         +- *(3) Project [name#1006, qty#1007, date#1008]
            +- *(3) Filter (((isnotnull(qty#1007) && (qty#1007 > 10)) && isnotnull(name#1006)) && (name#1006 = banana))
               +- *(3) FileScan parquet [name#1006,qty#1007,date#1008] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=4
```

## <a name="next-steps"></a>Passaggi successivi

* [Iperspazio del progetto](https://microsoft.github.io/hyperspace/)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
