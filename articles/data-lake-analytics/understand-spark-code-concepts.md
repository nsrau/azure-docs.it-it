---
title: Informazioni sui concetti Apache Spark codice per gli sviluppatori di Azure Data Lake Analytics U-SQL.
description: Questo articolo descrive Apache Spark concetti per aiutare gli sviluppatori U-SQL a comprendere i concetti di codice Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424015"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Informazioni sul codice Apache Spark per sviluppatori U-SQL

Questa sezione fornisce indicazioni generali sulla trasformazione degli script U-SQL in Apache Spark.

- Inizia con un [confronto tra i paradigmi di elaborazione dei due linguaggi](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Fornisce suggerimenti su come:
   - [Script di trasformazione](#transform-u-sql-scripts) , incluse le [espressioni del set di righe](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) U-SQL
   - [Codice .NET](#transform-net-code)
   - [Tipi di dati](#transform-typed-values)
   - [Oggetti catalogo](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Informazioni sui paradigmi di elaborazione e linguaggio U-SQL e Spark

Prima di iniziare la migrazione di Azure Data Lake Analytics gli script U-SQL in Spark, è utile comprendere le filosofie del linguaggio generale e di elaborazione dei due sistemi.

U-SQL è un linguaggio di query dichiarativo simile a SQL che usa un paradigma del flusso di dati e consente di incorporare e scalare facilmente il codice utente scritto in C#.NET (ad esempio), Python e R. Le estensioni utente possono implementare espressioni semplici o funzioni definite dall'utente, ma possono anche fornire all'utente la possibilità di implementare gli operatori definiti dall'utente che implementano operatori personalizzati per eseguire trasformazioni a livello di set di righe, estrazioni e scrittura dell'output.

Spark è un Framework con scalabilità orizzontale che offre diverse associazioni di linguaggio in scala, Java, Python, .NET e così via, in cui si scrive principalmente il codice in uno di questi linguaggi, si creano astrazioni di dati denominati RDD (Resilient Distributed DataSet), dataframe e set di dati e usare quindi un linguaggio specifico di dominio (DSL) simile a LINQ per trasformarli. Fornisce anche SparkSQL come sottolingua dichiarativa nelle astrazioni dataframe e DataSet. Il linguaggio DSL fornisce due categorie di operazioni, trasformazioni e azioni. L'applicazione delle trasformazioni alle astrazioni dei dati non comporta l'esecuzione della trasformazione, bensì la compilazione del piano di esecuzione che verrà inviato per la valutazione con un'azione, ad esempio la scrittura del risultato in una tabella o un file temporaneo o la stampa del risultato).

Quando si converte uno script U-SQL in un programma Spark, sarà quindi necessario decidere quale lingua si vuole usare per generare almeno l'astrazione del frame di dati (che attualmente è l'astrazione dei dati usata più di frequente) e se si vuole scrivere la dichiarativa trasformazioni del flusso di flussi usando DSL o SparkSQL. In alcuni casi più complessi, potrebbe essere necessario suddividere lo script U-SQL in una sequenza di Spark e altri passaggi implementati con Azure Batch o funzioni di Azure.

Inoltre, Azure Data Lake Analytics offre U-SQL in un ambiente del servizio processi senza server, mentre Azure Databricks e Azure HDInsight offrono Spark in forma di servizio cluster. Quando si trasforma l'applicazione, è necessario prendere in considerazione le implicazioni dell'ora di creazione, ridimensionamento, ridimensionamento e rimozione delle autorizzazioni per i cluster.

## <a name="transform-u-sql-scripts"></a>Trasformare gli script U-SQL

Gli script U-SQL seguono il modello di elaborazione seguente:

1. I dati vengono letti da file non strutturati, utilizzando l'istruzione `EXTRACT`, un percorso o una specifica del set di file e l'estrazione incorporata o l'estrazione definita dall'utente, lo schema desiderato o dalle tabelle U-SQL (tabelle gestite o esterne). Viene rappresentato come set di righe.
2. I set di righe vengono trasformati in più istruzioni U-SQL che applicano espressioni U-SQL ai set di righe e producono nuovi set di righe.
3. Infine, i set di righe risultanti vengono restituiti in entrambi i file usando l'istruzione `OUTPUT` che specifica le posizioni e un outputter incorporato o definito dall'utente o in una tabella U-SQL.

Lo script viene valutato in modo differito, vale a dire che ogni passaggio di estrazione e trasformazione viene composto in un albero delle espressioni e valutato a livello globale (il flusso di elementi).

I programmi Spark sono simili in quanto è possibile usare i connettori Spark per leggere i dati e creare i frame di dati, quindi applicare le trasformazioni ai frame di dati usando DSL o SparkSQL di tipo LINQ, quindi scrivere il risultato in file, tabelle Spark temporanee, alcuni tipi di linguaggio di programmazione o la console.

## <a name="transform-net-code"></a>Trasforma codice .NET

Il linguaggio delle espressioni di U- C# SQL è e offre diversi modi per scalare in orizzontale il codice .NET personalizzato.

Poiché Spark non supporta attualmente il codice .NET in modo nativo, sarà necessario riscrivere le espressioni in un'espressione Spark, scala, Java o Python equivalente o trovare un modo per eseguire chiamate nel codice .NET. Se lo script usa le librerie .NET, sono disponibili le opzioni seguenti:

- Tradurre il codice .NET in scala o Python.
- Suddividere lo script U-SQL in diversi passaggi, in cui si usano Azure Batch processi per applicare le trasformazioni .NET (se è possibile ottenere la scalabilità accettabile)
- Usare un'associazione di linguaggio .NET disponibile in Open Source denominata Moebius. Lo stato di questo progetto non è supportato.

In ogni caso, se si dispone di una grande quantità di logica .NET negli script U-SQL, contattare Microsoft tramite il rappresentante dell'account Microsoft per altre informazioni.

Di seguito sono riportati i dettagli relativi ai diversi casi di C# .NET e agli utilizzi negli script U-SQL.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Trasforma espressioni U-SQL C# inline scalari

Il linguaggio delle espressioni di U- C#SQL è. Molte delle espressioni U-SQL inline scalari vengono implementate in modo nativo per migliorare le prestazioni, mentre le espressioni più complesse possono essere eseguite tramite la chiamata a .NET Framework.

Spark ha il proprio linguaggio di espressioni scalari (come parte del DSL o in SparkSQL) e consente di chiamare funzioni definite dall'utente scritte nel linguaggio di hosting.

Se si dispone di espressioni scalari in U-SQL, è innanzitutto necessario trovare l'espressione scalare Spark riconosciuta in modo nativo per ottenere le migliori prestazioni, quindi eseguire il mapping delle altre espressioni in una funzione definita dall'utente del linguaggio di hosting di Spark scelto.

Tenere presente che .NET e C# hanno una semantica dei tipi diversa rispetto ai linguaggi di hosting Spark e al linguaggio DSL di Spark. Per ulteriori informazioni sulle differenze del sistema di tipi, vedere di [seguito](#transform-typed-values) .

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Trasformare funzioni .NET scalari definite dall'utente e aggregatori definiti dall'utente

U-SQL fornisce modi per chiamare funzioni .NET scalari arbitrarie e per chiamare aggregatori definiti dall'utente scritti in .NET.

Spark offre anche il supporto per le funzioni definite dall'utente e gli aggregatori definiti dall'utente scritti nella maggior parte dei linguaggi di hosting che possono essere chiamati da DSL e SparkSQL di Spark.

### <a name="transform-user-defined-operators-udos"></a>Trasformare gli operatori definiti dall'utente (Udo)

U-SQL offre diverse categorie di operatori definiti dall'utente (Udo), ad esempio estrattori, outputter, riduttori, processori, applicatori e combinatori, che possono essere scritti in .NET (e, in un certo senso, in Python e R).

Spark non offre lo stesso modello di estendibilità per gli operatori, ma ha funzionalità equivalenti per alcuni.

L'equivalente di Spark per gli estrattori e gli outputter è il connettore Spark. Per molti estrattori U-SQL è possibile trovare un connettore equivalente nella community di Spark. Per gli altri, sarà necessario scrivere un connettore personalizzato. Se il Extractor U-SQL è complesso e usa diverse librerie .NET, può essere preferibile creare un connettore in scala che usa l'interoperabilità per chiamare la libreria .NET che esegue l'effettiva elaborazione dei dati. In tal caso, sarà necessario distribuire il runtime di .NET Core nel cluster Spark e assicurarsi che le librerie .NET a cui viene fatto riferimento siano conformi a .NET Standard 2,0.

Gli altri tipi di Udo U-SQL dovranno essere riscritti usando funzioni definite dall'utente e aggregatori e l'espressione Spark DLS o SparkSQL semanticamente appropriata. È ad esempio possibile eseguire il mapping di un processore a una selezione di una serie di chiamate UDF, in pacchetto come funzione che accetta un dataframe come argomento e restituisce un dataframe.

### <a name="transform-u-sqls-optional-libraries"></a>Trasformare le librerie facoltative di U-SQL

U-SQL fornisce un set di librerie opzionali e demo che offrono il supporto per [Python](data-lake-analytics-u-sql-python-extensions.md), [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, avro](https://github.com/Azure/usql/tree/master/Examples/DataFormats)e alcune [funzionalità di servizi cognitivi](data-lake-analytics-u-sql-cognitive.md).

Spark offre rispettivamente la propria integrazione Python e R, pySpark e Sparkr, e fornisce connettori per la lettura e la scrittura di JSON, XML e AVRO.

Se è necessario trasformare uno script che fa riferimento alle librerie di servizi cognitivi, è consigliabile contattare Microsoft tramite il rappresentante dell'account Microsoft.

## <a name="transform-typed-values"></a>Trasforma valori tipizzati

Poiché il sistema di tipi U-SQL è basato sul sistema di tipi .NET e Spark ha un proprio sistema di tipi, che è influenzato dall'associazione del linguaggio host, sarà necessario assicurarsi che i tipi in uso siano vicini e per determinati tipi, gli intervalli di tipi , la precisione e/o la scala possono essere leggermente diverse. Inoltre, U-SQL e Spark trattano i valori `null` in modo diverso.

### <a name="data-types"></a>Tipi di dati

La tabella seguente fornisce i tipi equivalenti in Spark, scala e PySpark per i tipi U-SQL specificati.

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

- [org. Apache. Spark. SQL. Types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Tipi Spark SQL e dataframes](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Tipi di valore scala](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark. SQL. Types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Trattamento del valore NULL

In Spark, i tipi per impostazione predefinita consentono i valori NULL in U-SQL, contrassegnando in modo esplicito come nullable il valore scalare non oggetto. Mentre Spark consente di definire una colonna come non nullable, non impone il vincolo e [può causare un risultato errato](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

In Spark, NULL indica che il valore è sconosciuto. Un valore NULL di Spark è diverso da qualsiasi valore, incluso se stesso. I confronti tra due valori NULL Spark o tra un valore NULL e qualsiasi altro valore restituiscono unknown perché il valore di ogni NULL è sconosciuto.  

Questo comportamento è diverso da U-SQL, che segue C# la semantica in cui `null` è diverso da qualsiasi valore, ma uguale a se stesso.  

Pertanto, un'istruzione SparkSQL `SELECT` che usa `WHERE column_name = NULL` restituisce zero righe anche se sono presenti valori NULL in `column_name`, mentre in U-SQL restituiscono le righe in cui `column_name` è impostato su `null`. Analogamente, un'istruzione Spark `SELECT` che usa `WHERE column_name != NULL` restituisce zero righe anche se sono presenti valori non null in `column_name`, mentre in U-SQL restituiscono le righe con valori non null. Pertanto, se si desidera la semantica di controllo null U-SQL, è necessario utilizzare rispettivamente [IsNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) e [IsNotNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) (o l'equivalente DSL).

## <a name="transform-u-sql-catalog-objects"></a>Trasformazione di oggetti del Catalogo U-SQL

Una delle principali differenze consiste nel fatto che gli script U-SQL possono usare gli oggetti del catalogo, molti dei quali non hanno un equivalente Spark diretto.

Spark fornisce il supporto per i concetti relativi all'archivio metadati hive, principalmente database e tabelle, in modo che sia possibile eseguire il mapping di database e schemi U-SQL ai database hive e alle tabelle U-SQL alle tabelle Spark (vedere lo schema di [dati archiviati nelle tabelle u-SQL](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)), ma senza supporto per le visualizzazioni. funzioni con valori di tabella (funzioni con valori), stored procedure, assembly U-SQL, origini dati esterne e così via.

Gli oggetti di codice U-SQL come viste, funzioni con valori, stored procedure e assembly possono essere modellati tramite funzioni di codice e librerie in Spark e a cui viene fatto riferimento tramite la funzione del linguaggio host e i meccanismi di astrazione procedurale (ad esempio, tramite l'importazione di Moduli Python o che fanno riferimento a funzioni scala).

Se il Catalogo U-SQL è stato usato per condividere dati e oggetti di codice tra progetti e team, è necessario usare meccanismi equivalenti per la condivisione (ad esempio, Maven per la condivisione di oggetti di codice).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Trasformare le espressioni del set di righe U-SQL e le espressioni scalari basate su SQL

Il linguaggio di base di U-SQL sta trasformando set di righe ed è basato su SQL. Di seguito è riportato un elenco non esaustivo delle espressioni di set di righe più comuni offerte in U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ aggregazioni +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/espressioni di `SEMI` `JOIN`
- espressioni `CROSS`/`OUTER` `APPLY`
- espressioni `PIVOT`/`UNPIVOT`
- Costruttore del set di righe `VALUES`

- Set Expressions `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Inoltre, U-SQL offre un'ampia gamma di espressioni scalari basate su SQL, ad esempio

- espressioni finestra `OVER`
- una varietà di aggregatori e funzioni di rango predefiniti (`SUM`, `FIRST` e così via)
- Alcune delle espressioni scalari SQL più note: `CASE`, `LIKE`, (`NOT`) `IN`, `AND`, `OR` e così via.

Spark offre espressioni equivalenti nel formato DSL e SparkSQL per la maggior parte di queste espressioni. Alcune espressioni non supportate in modo nativo in Spark dovranno essere riscritte usando una combinazione delle espressioni Spark native e dei modelli semanticamente equivalenti. Ad esempio, `OUTER UNION` dovranno essere convertite nella combinazione equivalente di proiezioni e unioni.

A causa della diversa gestione dei valori NULL, un join U-SQL corrisponderà sempre a una riga se entrambe le colonne da confrontare contengono un valore NULL, mentre un join in Spark non corrisponderà a tali colonne, a meno che non vengano aggiunti controlli espliciti null.

## <a name="transform-other-u-sql-concepts"></a>Trasformare altri concetti di U-SQL

U-SQL offre inoltre un'ampia gamma di altri concetti e funzionalità, ad esempio query federate su database SQL Server, parametri, variabili di espressione scalare e lambda, variabili di sistema, `OPTION` hint.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Query federate su database SQL Server/tabelle esterne

U-SQL fornisce origini dati e tabelle esterne, oltre a query dirette sul database SQL di Azure. Mentre Spark non offre le stesse astrazioni di oggetti, fornisce il [connettore Spark per il database SQL di Azure](../sql-database/sql-database-spark-connector.md) che può essere usato per eseguire query sui database SQL.

### <a name="u-sql-parameters-and-variables"></a>Variabili e parametri U-SQL

I parametri e le variabili utente hanno concetti equivalenti in Spark e nei rispettivi linguaggi di hosting.

Ad esempio, in scala è possibile definire una variabile con la parola chiave `var`:

```
var x = 2 * 3;
println(x)
```

Le variabili di sistema di U-SQL (variabili che iniziano con `@@`) possono essere suddivise in due categorie:

- Variabili di sistema impostabili che possono essere impostate su valori specifici per influenzare il comportamento degli script
- Variabili di sistema informative che chiedono informazioni a livello di sistema e di processo

La maggior parte delle variabili di sistema configurabili non ha equivalenti diretti in Spark. Alcune variabili di sistema informative possono essere modellate passando le informazioni come argomenti durante l'esecuzione del processo, altre possono avere una funzione equivalente nel linguaggio di hosting di Spark.

### <a name="u-sql-hints"></a>Hint U-SQL

U-SQL offre diversi modi sintattici per fornire suggerimenti all'Query Optimizer e al motore di esecuzione:  

- Impostazione di una variabile di sistema U-SQL
- clausola `OPTION` associata all'espressione del set di righe per fornire un hint per i dati o il piano
- hint di join nella sintassi dell'espressione di join, ad esempio `BROADCASTLEFT`

Query Optimizer basati sui costi di Spark offre funzionalità proprie per fornire suggerimenti e ottimizzare le prestazioni delle query. Consultare la documentazione corrispondente.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui formati di dati Spark per sviluppatori U-SQL](understand-spark-data-formats.md)
- [.NET per Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Aggiornare le soluzioni di analisi di Big Data da Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Trasformare i dati usando l'attività Spark in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Trasformare i dati usando l'attività hive di Hadoop in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Che cos'è Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
