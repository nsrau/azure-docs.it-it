---
title: Informazioni sui concetti di codice Apache Spark per gli sviluppatori U-SQL di Azure Data Lake Analytics.Understand Apache Spark code concepts for Azure Data Lake Analytics U-SQL developers.
description: In questo articolo vengono descritti i concetti di Apache Spark che consentono agli sviluppatori U-SQL di comprendere i concetti di codice Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74424015"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Informazioni sul codice Apache Spark per gli sviluppatori U-SQLUnderstand Apache Spark code for U-SQL developers

In questa sezione vengono fornite indicazioni generali sulla trasformazione degli script U-SQL in Apache Spark.This section provides high-level guidance on transforming U-SQL Scripts to Apache Spark.

- Si inizia con un [confronto tra i paradigmi di elaborazione dei due linguaggi](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Fornisce suggerimenti su come:
   - [Trasformare gli script,](#transform-u-sql-scripts) incluse le espressioni del set di righe di U-SQLTransform scripts including U-SQL's [rowset expressions](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [Codice .NET](#transform-net-code)
   - [Tipi di dati](#transform-typed-values)
   - [Oggetti catalogo](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Comprendere i paradigmi di elaborazione e linguaggio U-SQL e Spark

Prima di iniziare a eseguire la migrazione degli script U-SQL di Azure Data Lake Analytics a Spark, è utile comprendere il linguaggio generale e le filosofie di elaborazione dei due sistemi.

U-SQL è un linguaggio di query dichiarativo di tipo SQL che usa un paradigma del flusso di dati e consente di incorporare e scalare facilmente il codice utente scritto in .NET (ad esempio, C , Python e R.U-SQL is a SQL-like declarative query language that uses a data-flow paradigm and allows you to easily embed and scale out user-code written in .NET (for example C'è), Python, and R. Le estensioni utente possono implementare espressioni semplici o funzioni definite dall'utente, ma possono anche fornire all'utente la possibilità di implementare i cosiddetti operatori definiti dall'utente che implementano operatori personalizzati per eseguire trasformazioni a livello di set di righe, estrazioni e scrittura dell'output.

Spark è un framework con scalabilità orizzontale che offre diverse associazioni di linguaggio in Scala, Java, Python, .NET e così via in cui si scrive principalmente il codice in uno di questi linguaggi, si creano astrazioni di dati denominate set di dati distribuiti resilienti (RDD), dataframe e set di dati e quindi utilizzare un linguaggio specifico di dominio (DSL) di tipo LINQ per trasformarli. Fornisce inoltre SparkSQL come sottolingua dichiarativa nelle astrazioni del frame di dati e del set di dati. Il linguaggio DSL fornisce due categorie di operazioni, trasformazioni e azioni. L'applicazione di trasformazioni alle astrazioni dei dati non eseguirà la trasformazione, ma costituirà il piano di esecuzione che verrà inviato per la valutazione con un'azione (ad esempio, scrivendo il risultato in una tabella o file temporaneo o stampando il risultato positivo).

Pertanto, quando si traduce uno script U-SQL in un programma Spark, è necessario decidere quale linguaggio si desidera utilizzare per generare almeno l'astrazione del frame di dati (che è attualmente l'astrazione dei dati utilizzata più di frequente) e se si desidera scrivere la procedura dichiarativa trasformazioni del flusso di dati utilizzando Il linguaggio DSL o SparkSQL. In some more complex cases, you may need to split your U-SQL script into a sequence of Spark and other steps implemented with Azure Batch or Azure Functions.

Inoltre, Azure Data Lake Analytics offre U-SQL in un ambiente del servizio di processo senza server, mentre sia Azure Databricks che Azure HDInsight offrono Spark sotto forma di servizio cluster. Quando si trasforma l'applicazione, è necessario prendere in considerazione le implicazioni di ora la creazione, il ridimensionamento, il ridimensionamento e la rimozione delle autorizzazioni dei cluster.

## <a name="transform-u-sql-scripts"></a>Trasformare gli script U-SQLTransform U-SQL scripts

Gli script U-SQL seguono il modello di elaborazione seguente:U-SQL scripts follow the following processing pattern:

1. I dati vengono letti da file `EXTRACT` non strutturati, utilizzando l'istruzione, una specifica del percorso o del set di file e l'estrattore incorporato o definito dall'utente e lo schema desiderato oppure dalle tabelle U-SQL (tabelle gestite o esterne). È rappresentato come un set di righe.
2. I set di righe vengono trasformati in più istruzioni U-SQL che applicano espressioni U-SQL ai set di righe e producono nuovi set di righe.
3. Infine, i set di righe risultanti `OUTPUT` vengono restituiti in entrambi i file utilizzando l'istruzione che specifica i percorsi e un outputter incorporato o definito dall'utente o in una tabella U-SQL.

Lo script viene valutato in modo lato, il che significa che ogni istruzione di estrazione e trasformazione è composta in un albero delle espressioni e valutata globalmente (il flusso di dati).

I programmi Spark sono simili in quanto si utilizzerebbero i connettori Spark per leggere i dati e creare i frame di dati, quindi applicare le trasformazioni sui frame di dati utilizzando il linguaggio DSL di tipo LINQ o SparkSQL e quindi scrivere il risultato in file, tabelle Spark temporanee, alcuni tipi di linguaggio di programmazione o la console.

## <a name="transform-net-code"></a>Trasformare il codice .NETTransform .NET code

Il linguaggio delle espressioni di U-SQL è in linguaggio C, e offre diversi modi per scalare orizzontalmente il codice .NET personalizzato.

Poiché Spark attualmente non supporta l'esecuzione di codice .NET, sarà necessario riscrivere le espressioni in un'espressione Spark, Scala, Java o Python equivalente o trovare un modo per chiamare il codice .NET. Se lo script utilizza librerie .NET, sono disponibili le opzioni seguenti:

- Tradurre il codice .NET in Scala o Python.Translate your .NET code into Scala or Python.
- Suddividere lo script U-SQL in diversi passaggi, in cui si usano i processi batch di Azure per applicare le trasformazioni .NET (se è possibile ottenere una scalabilità accettabile)Split your U-SQL script into several steps, where you use Azure Batch processes to apply the .NET transformations (if you can get acceptable scale)
- Usare un'associazione del linguaggio .NET disponibile in Open Source denominata Moebius.Use a .NET language binding available in Open Source called Moebius. Questo progetto non è in uno stato supportato.

In ogni caso, se si dispone di una grande quantità di logica .NET negli script U-SQL, contattarci tramite il rappresentante dell'account Microsoft per ulteriori indicazioni.

I dettagli seguenti riguardano i diversi casi di utilizzo di .NET e C.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Trasformare le espressioni U-SQL scalabiliinU-SQL C

Il linguaggio delle espressioni di U-SQL è in C. Molte delle espressioni U-SQL inline scalari vengono implementate in modo nativo per migliorare le prestazioni, mentre le espressioni più complesse possono essere eseguite tramite la chiamata nel framework .NET.

Spark ha il proprio linguaggio di espressione scalare (come parte del linguaggio DSL o in SparkSQL) e consente di chiamare in funzioni definite dall'utente scritte nel linguaggio di hosting.

Se si dispone di espressioni scalari in U-SQL, è necessario innanzitutto trovare l'espressione scalare Spark in grado di comprendere in modo nativo più appropriata per ottenere il massimo delle prestazioni e quindi eseguire il mapping delle altre espressioni in una funzione definita dall'utente del linguaggio di hosting Spark di propria scelta.

Tenere presente che .NET e C , hanno una semantica dei tipi diversa rispetto ai linguaggi di hosting Spark e DSL di Spark. Per ulteriori informazioni sulle differenze di sistema di tipo, vedere di [seguito.](#transform-typed-values)

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Trasformare le funzioni .NET scalari definite dall'utente e gli aggregatori definiti dall'utenteTransform user-defined scalar .NET functions and user-defined aggregators

U-SQL fornisce modi per chiamare funzioni .NET scalari arbitrarie e per chiamare aggregatori definiti dall'utente scritti in .NET.

Spark offre anche il supporto per le funzioni definite dall'utente e gli aggregatori definiti dall'utente scritti nella maggior parte dei linguaggi di hosting che possono essere chiamati da DSL e SparkSQL di Spark.

### <a name="transform-user-defined-operators-udos"></a>Trasformare gli operatori definiti dall'utente (UDO)Transform user-defined operators (UDOs)

U-SQL fornisce diverse categorie di operatori definiti dall'utente (UDO), ad esempio estrattori, outputter, riduttori, processori, applier e combinatori che possono essere scritti in .NET (e - in una certa misura - in Python e R).

Spark non offre lo stesso modello di estendibilità per gli operatori, ma ha funzionalità equivalenti per alcuni.

Lo spark equivalente a estrattori ed outputter è connettori Spark. Per molti estrattori U-SQL, è possibile trovare un connettore equivalente nella comunità Spark. Per altri, sarà necessario scrivere un connettore personalizzato. Se l'estrattore U-SQL è complesso e utilizza diverse librerie .NET, potrebbe essere preferibile compilare un connettore in Scala che utilizza l'interoperabilità per chiamare nella libreria .NET che esegue l'elaborazione effettiva dei dati. In tal caso, sarà necessario distribuire il runtime di .NET Core nel cluster Spark e assicurarsi che le librerie .NET di riferimento siano conformi a .NET Standard 2.0.

Gli altri tipi di UDO U-SQL dovranno essere riscritti utilizzando funzioni e aggregatori definiti dall'utente e l'espressione Spark DLS o SparkSQL sandalargaa dal punto di vista semantico. Ad esempio, un processore può essere mappato a un'istruzione SELECT di una varietà di chiamate UDF, incluso in un pacchetto come funzione che accetta un frame di dati come argomento e restituisce un frame di dati.

### <a name="transform-u-sqls-optional-libraries"></a>Trasformare le librerie facoltative di U-SQLTransform U-SQL's optional libraries

U-SQL fornisce un set di librerie facoltative e demo che [offrono](data-lake-analytics-u-sql-python-extensions.md)funzionalità Python , [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, AVRO](https://github.com/Azure/usql/tree/master/Examples/DataFormats)e alcune funzionalità dei [servizi cognitivi.](data-lake-analytics-u-sql-cognitive.md)

Spark offre rispettivamente l'integrazione Python e R, pySpark e SparkR e fornisce connettori per leggere e scrivere JSON, XML e AVRO.

Se è necessario trasformare uno script che fa riferimento alle librerie dei servizi cognitivi, è consigliabile contattarci tramite il rappresentante dell'account Microsoft.

## <a name="transform-typed-values"></a>Trasformare i valori digitatiTransform typed values

Poiché il sistema di tipi di U-SQL è basato sul sistema di tipi .NET e Spark ha un proprio sistema di tipi, che è influenzato dall'associazione del linguaggio host, è necessario assicurarsi che i tipi su cui si sta operando siano vicini e per alcuni tipi, gli intervalli di tipi, la precisione e/o la scala potrebbero essere leggermente diversi. Inoltre, U-SQL e `null` Spark trattano i valori in modo diverso.

### <a name="data-types"></a>Tipi di dati

Nella tabella seguente vengono forniti i tipi equivalenti in Spark, Scala e PySpark per i tipi U-SQL specificati.

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Per altre informazioni, vedere:

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Tipi di sql e frame di dati SparkSspark SQL and DataFrames Types](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Tipi di valore ScalaScala value types](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Trattamento di NULL

In Spark, i tipi per impostazione predefinita consentono valori NULL in U-SQL, contrassegnare in modo esplicito scalare, non oggetto come nullable. Sebbene Spark consenta di definire una colonna come non nullable, non applicherà il vincolo e [potrebbe generare un risultato errato.](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)

In Spark, NULL indica che il valore è sconosciuto. Un valore Spark NULL è diverso da qualsiasi valore, incluso se stesso. I confronti tra due valori Spark NULL o tra un valore NULL e qualsiasi altro valore restituiscono unknown perché il valore di ogni NULL è sconosciuto.  

Questo comportamento è diverso da U-SQL, che `null` segue la semantica di C, dove è diverso da qualsiasi valore ma uguale a se stesso.  

Pertanto, `SELECT` un'istruzione `WHERE column_name = NULL` SparkSQL che utilizza restituisce `column_name`zero righe anche se sono presenti `column_name` valori NULL `null`in , mentre in U-SQL, restituirebbe le righe in cui è impostato su . Analogamente, un'istruzione `SELECT` `WHERE column_name != NULL` Spark che utilizza restituisce zero `column_name`righe anche se sono presenti valori non null in , mentre in U-SQL, restituirebbe le righe con non null. Pertanto, se si desidera che la semantica di controllo null U-SQL, è necessario utilizzare [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) e [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) rispettivamente (o il relativo equivalente DSL).

## <a name="transform-u-sql-catalog-objects"></a>Trasformare gli oggetti del catalogo U-SQLTransform U-SQL catalog objects

Una differenza importante è che gli script U-SQL possono utilizzare i relativi oggetti catalogo, molti dei quali non hanno un equivalente Diretto di Spark.

Spark fornisce il supporto per i concetti di Hive Meta store, principalmente database e tabelle, pertanto è possibile eseguire il mapping di database e schemi U-SQL adatabase Hive e di tabelle U-SQL alle tabelle Spark (vedere Spostamento di [dati archiviati in tabelle U-SQL),](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)ma non dispone di alcun supporto per viste, funzioni con valori di tabella (TVF), stored procedure, assembly U-SQL, origini dati esterne e così via.

Gli oggetti di codice U-SQL, ad esempio viste, TVF, stored procedure e assembly, possono essere modellati tramite funzioni e librerie di codice in Spark e su cui viene fatto riferimento utilizzando la funzione del linguaggio host e i meccanismi di astrazione procedurale (ad esempio, tramite l'importazione moduli Python o riferimenti alle funzioni Scala).

Se il catalogo U-SQL è stato utilizzato per condividere dati e oggetti di codice tra progetti e team, è necessario utilizzare meccanismi equivalenti per la condivisione (ad esempio, Maven per la condivisione di oggetti di codice).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Trasformare espressioni di set di righe U-SQL ed espressioni scalari basate su SQLTransform U-SQL rowset expressions and SQL-based scalar expressions

Il linguaggio principale di U-SQL è la trasformazione dei set di righe ed è basato su SQL. The following is a non-exhaustive list of the most common rowset expressions offered in U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`"Aggregati"`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN` espressioni
- `CROSS`/`OUTER``APPLY` espressioni
- `PIVOT`/`UNPIVOT`Espressioni
- `VALUES`costruttore del set di righeRowset constructor

- Impostare le espressioni`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Inoltre, U-SQL fornisce una varietà di espressioni scalari basate su SQL, come

- `OVER`espressioni di windowing
- una varietà di aggregatori incorporati e`SUM` `FIRST` funzioni di classificazione ( , ecc.)
- Alcune delle espressioni scalari SQL `CASE` `LIKE`più`NOT`note: `AND` `OR` , , ( ), `IN`, e così via.

Spark offre espressioni equivalenti sia nel formato DSL che SparkSQL per la maggior parte di queste espressioni. Alcune delle espressioni non supportate in modo nativo in Spark dovranno essere riscritte utilizzando una combinazione delle espressioni Spark native e dei modelli semanticamente equivalenti. Ad esempio, `OUTER UNION` dovrà essere tradotto nella combinazione equivalente di proiezioni e unioni.

A causa della diversa gestione dei valori NULL, un join U-SQL corrisponderà sempre a una riga se entrambe le colonne confrontate contengono un valore NULL, mentre un join in Spark non corrisponderà a tali colonne a meno che non vengano aggiunti controlli null espliciti.

## <a name="transform-other-u-sql-concepts"></a>Trasformare altri concetti di U-SQLTransform other U-SQL concepts

U-SQL offre anche una varietà di altre funzionalità e concetti, ad esempio query federate su database `OPTION` DI SQL Server, parametri, variabili di espressione scalari e lambda, variabili di sistema, suggerimenti.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Query federate su database/tabelle esterne di SQL Server

U-SQL provides data source and external tables as well as direct queries against Azure SQL Database. Mentre Spark non offre le stesse astrazioni di oggetti, fornisce [il connettore Spark per](../sql-database/sql-database-spark-connector.md) il database SQL di Azure che può essere usato per eseguire query sui database SQL.

### <a name="u-sql-parameters-and-variables"></a>Parametri e variabili U-SQL

I parametri e le variabili utente hanno concetti equivalenti in Spark e nelle relative lingue di hosting.

Ad esempio, in Scala, è `var` possibile definire una variabile con la parola chiave:For example in Scala, you can define a variable with the keyword:

```
var x = 2 * 3;
println(x)
```

Le variabili di sistema di U-SQL (variabili che iniziano con `@@`) possono essere suddivise in due categorie:

- Impostare le variabili di sistema che possono essere impostate su valori specifici per influire sul comportamento degli script
- Variabili di sistema informative che indagulino informazioni sul sistema e sul livello di lavoro

La maggior parte delle variabili di sistema impostabili non hanno un equivalente diretto in Spark. Alcune delle variabili di sistema informative possono essere modellate passando le informazioni come argomenti durante l'esecuzione del processo, altre possono avere una funzione equivalente nel linguaggio di hosting di Spark.

### <a name="u-sql-hints"></a>Suggerimenti U-SQL

U-SQL offre diversi modi sintattici per fornire suggerimenti per Query Optimizer e motore di esecuzione:  

- Impostazione di una variabile di sistema U-SQLSetting a U-SQL system variable
- una `OPTION` clausola associata all'espressione del set di righe per fornire un suggerimento dati o piano
- un suggerimento di join nella sintassi `BROADCASTLEFT`dell'espressione di join (ad esempio, )

Query Optimizer basato sui costi di Spark dispone di proprie funzionalità per fornire suggerimenti e ottimizzare le prestazioni delle query. Si prega di fare riferimento alla documentazione corrispondente.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui formati di dati Spark per gli sviluppatori U-SQLUnderstand Spark data formats for U-SQL developers](understand-spark-data-formats.md)
- [.NET per Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Aggiornare le soluzioni di analisi dei Big Data da Azure Data Lake Storage Gen1 ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Trasformare dati tramite l'attività Spark in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Trasformare i dati usando l'attività Hive di Hadoop in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Informazioni su Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
