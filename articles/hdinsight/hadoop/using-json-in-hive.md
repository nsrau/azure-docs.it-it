---
title: Analizzare & processo JSON con Apache Hive - Azure HDInsightAnalyze a process JSON with Apache Hive - Azure HDInsight
description: Informazioni su come usare i documenti JSON e analizzarli usando Apache Hive in Azure HDInsight.Learn how to use JSON documents and analyze them by using Apache Hive in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: db7c7ae9889d26479f51a7714e7e9fb04b444628
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757120"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Elaborare e analizzare documenti JSON tramite apache Hive in HDInsight di Azure

Informazioni su come elaborare e analizzare i file JavaScript Object Notation (JSON) tramite Apache Hive in HDInsight di Azure. Questo articolo usa il documento JSON seguente:This article uses the following JSON document:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Il file è disponibile in `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Per altre informazioni su come usare l'archivio BLOB di Azure con HDInsight, vedere [Usare un archivio BLOB di Azure compatibile con Hadoop Distributed File System con Apache Hadoop in HDInsight](../hdinsight-hadoop-use-blob-storage.md). È possibile copiare il file nel contenitore predefinito del cluster.

In questo articolo si usa la console Apache Hive. Per istruzioni su come aprire la console Hive, vedere Usare la vista [Apache Ambari Hive con Apache Hadoop in HDInsight.](apache-hadoop-use-hive-ambari-view.md)

> [!NOTE]  
> La vista Hive non è più disponibile in HDInsight 4.0.

## <a name="flatten-json-documents"></a>Rendere flat i documenti JSON

I metodi elencati nella sezione successiva richiedono che il documento JSON sia composto da una singola riga. È quindi necessario rendere flat il documento JSON in una stringa. Se il documento JSON è già flat, è possibile saltare questo passaggio e passare alla sezione successiva relativa all'analisi dei dati JSON. Per rendere flat il documento JSON, eseguire lo script seguente:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Il file JSON non elaborato è disponibile in `wasb://processjson@hditutorialdata.blob.core.windows.net/`. La tabella **StudentsRaw** Hive punta al documento JSON non elaborato che non è appiattito.

La tabella Hive **StudentsOneLine** archivia i dati nel file system predefinito di HDInsight nel percorso **/json/students/**.

L'istruzione **INSERT** popola la tabella **StudentOneLine** con i dati JSON flat.

L'istruzione **SELECT** restituisce solo una riga.

Ecco l'output dell'istruzione **SELECT**:

![HDInsight appiattimento del documento JSONHDInsight flattening the JSON document](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analizzare i documenti JSON in Hive

Hive offre tre meccanismi diversi per l'esecuzione di query nei documenti JSON, oppure è possibile scrivere:

* Usare la funzione definita dall'utente get_json_object.
* Usare la funzione definita dall'utente json_tuple.
* Usare il Serializzatore/Deserializzatore personalizzato (SerDe).
* Scrivere una funzione definita dall'utente personalizzata tramite Python o altri linguaggi. Per altre informazioni su come eseguire il codice Python con Hive, vedere [Funzione definita dall'utente Python con Apache Hive e Apache Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Usare la funzione definita dall'utente get_json_object

Hive fornisce una funzione definita dall'utente incorporata denominata [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) che esegue la query JSON durante il runtime. Questo metodo accetta due argomenti: il nome della tabella e il nome del metodo. Il nome del metodo contiene il documento JSON appiattito e il campo JSON che deve essere analizzato. Diamo un'occhiata a un esempio per vedere come funziona questa funzione UDF.

La query seguente restituisce il nome e il cognome di ogni studente:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Questo è l'output ottenuto quando si esegue la query nella finestra della console:

![Apache Hive ottiene l'oggetto json UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

La funzione definita dall'utente get-json_object presenta delle limitazioni:

* Poiché ogni campo della query richiede una nuova analisi della query, si ha un impatto sulle prestazioni.
* **GET\_JSON_OBJECT()** restituisce la rappresentazione di stringa di una matrice. Per convertirla in una matrice Hive, è necessario usare espressioni regolari per sostituire le parentesi quadre "[" and "]" e quindi è necessario anche chiamare anche una suddivisione per ottenere la matrice.

Questa conversione è il motivo per cui il wiki Hive consiglia di utilizzare **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Usare la funzione definita dall'utente json_tuple

Un'altra funzione definita dall'utente fornita da Hive viene chiamata [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), che fa meglio di [get_ _object json](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Questo metodo accetta un set di chiavi e una stringa JSON. Quindi restituisce una tupla di valori. La query seguente restituisce l'ID dello studente e il livello dal documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Output dello script nella console di Hive:

![Risultati della query json Apache HiveApache Hive json query results](./media/using-json-in-hive/hdinsight-json-tuple.png)

La `json_tuple` funzione definita dall'utente usa la sintassi di [visualizzazione laterale](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in Hive, che consente a una tupla json\_di creare una tabella virtuale applicando la funzione UDT a ogni riga della tabella originale. I documenti JSON complessi diventano troppo difficili da gestire a causa dell'uso ripetuto di **LATERAL VIEW**. Inoltre, **JSON_TUPLE** non è in grado di gestire JSON annidati.

### <a name="use-a-custom-serde"></a>Usare un'interfaccia SerDe personalizzata

SerDe è la scelta migliore per l'analisi dei documenti JSON nidificati. Consente di definire lo schema JSON e quindi è possibile usare lo schema per analizzare i documenti. Per istruzioni, vedere [Come usare un Serde JSON personalizzato con Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Summary

Il tipo di operatore JSON in Hive scelto dipende dallo scenario. Con un semplice documento JSON e un campo da cercare, scegliere **l'get_json_object**HIVe UDF . Se hai più di una chiave su cui cercare, puoi utilizzare **json_tuple**. Per i documenti nidificati, utilizzare **JSON SerDe**.

## <a name="next-steps"></a>Passaggi successivi

Per gli articoli correlati, vedere:

* [Usare Apache Hive e HiveQL con Apache Hadoop in HDInsight per analizzare un file Apache log4j di esempio](../hdinsight-use-hive.md)
* [Analizzare i dati di ritardo di volo usando Interactive Query in HDInsightAnalyze flight delay data by using Interactive Query in HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analizzare i dati Twitter mediante Apache Hive in HDInsight](../hdinsight-analyze-twitter-data-linux.md)
