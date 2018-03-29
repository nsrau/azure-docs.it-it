---
title: Analizzare ed elaborare documenti JSON con Apache Hive in HDInsight di Azure | Microsoft Docs
description: Informazioni su come usare e analizzare i documenti JSON tramite apache Hive in HDInsight di Azure
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 04c3a8262e52a630012a0a70e4b1ccb0ade76449
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Elaborare e analizzare documenti JSON tramite apache Hive in HDInsight di Azure

Informazioni su come elaborare e analizzare i file JavaScript Object Notation (JSON) tramite Apache Hive in HDInsight di Azure. Questa esercitazione usa il seguente documento JSON:

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

Il file è disponibile in **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Per altre informazioni su come usare l'archivio BLOB di Azure con HDInsight, vedere [Usare un archivio BLOB di Azure compatibile con Hadoop Distributed File System con Hadoop in HDInsight](../hdinsight-hadoop-use-blob-storage.md). È possibile copiare il file nel contenitore predefinito del cluster.

In questa esercitazione viene usata la console di Hive. Per istruzioni su come aprire la console Hive, vedere [Usare Hive con Hadoop in HDInsight con Desktop remoto](apache-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Rendere flat i documenti JSON
I metodi elencati nella sezione seguente presuppongono che il documento JSON sia composto da una singola riga. È quindi necessario rendere flat il documento JSON in una stringa. Se il documento JSON è già flat, è possibile saltare questo passaggio e passare alla sezione successiva relativa all'analisi dei dati JSON. Per rendere flat il documento JSON, eseguire lo script seguente:

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

Il file JSON non elaborato è disponibile in **wasb://processjson@hditutorialdata.blob.core.windows.net/**. La tabella Hive **StudentsRaw** punta al documento JSON non elaborato che non è reso flat.

La tabella Hive **StudentsOneLine** archivia i dati nel file system predefinito di HDInsight nel percorso **/json/students/**.

L'istruzione **INSERT** popola la tabella **StudentOneLine** con i dati JSON flat.

L'istruzione **SELECT** restituisce solo una riga.

Ecco l'output dell'istruzione **SELECT**:

![Rendere flat il documento JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analizzare i documenti JSON in Hive
Hive offre tre meccanismi diversi per l'esecuzione di query nei documenti JSON, oppure è possibile scrivere:

* Usare la funzione definita dall'utente get_json_object.
* Usare la funzione definita dall'utente json_tuple.
* Usare il Serializzatore/Deserializzatore personalizzato (SerDe).
* Scrivere una funzione definita dall'utente personalizzata tramite Python o altri linguaggi. Per altre informazioni su come eseguire il codice Python con Hive, vedere [Funzione definita dall'utente Python con Apache Hive e Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Usare la funzione definita dall'utente get_json_object
Hive offre una funzione definita dall'utente predefinita denominata [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) in grado di eseguire query JSON in fase di esecuzione. Questo metodo accetta due argomenti, ovvero il nome della tabella e il nome del metodo che include il documento JSON flat e il campo JSON da analizzare. L'esempio seguente illustra il funzionamento di questa funzione definita dall'utente.

La query seguente restituisce il nome e il cognome di ogni studente:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Questo è l'output ottenuto quando si esegue la query nella finestra della console:

![Funzione definita dall'utente get_json_object][image-hdi-hivejson-getjsonobject]

La funzione definita dall'utente get-json_object presenta delle limitazioni:

* Poiché ogni campo della query richiede una nuova analisi della query, si ha un impatto sulle prestazioni.
* **GET\_JSON_OBJECT()** restituisce la rappresentazione di stringa di una matrice. Per convertirla in una matrice Hive, è necessario usare espressioni regolari per sostituire le parentesi quadre "[" and "]" e quindi è necessario anche chiamare anche una suddivisione per ottenere la matrice.

Il wiki relativo a Hive consiglia quindi di usare json_tuple, come illustrato più avanti.  

### <a name="use-the-jsontuple-udf"></a>Usare la funzione definita dall'utente json_tuple
L'altra funzione definita dall'utente disponibile in Hive è denominata [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) e offre prestazioni migliori rispetto a [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Questo metodo accetta un insieme di chiavi e una stringa JSON e restituisce una tupla di valori tramite una funzione. La query seguente restituisce l'ID dello studente e il livello dal documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Output dello script nella console di Hive:

![Funzione definita dall'utente json_tuple][image-hdi-hivejson-jsontuple]

La funzione definita dall'utente json_tuple usa la sintassi di tipo [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in Hive, che consente a json\_tuple di creare una tabella virtuale applicando la funzione UDT a ogni riga della tabella originale. I documenti JSON complessi diventano troppo difficili da gestire a causa dell'uso ripetuto di **LATERAL VIEW**. **JSON_TUPLE** non è in grado di gestire documenti JSON annidati.

### <a name="use-a-custom-serde"></a>Usare un'interfaccia SerDe personalizzata
SerDe è la scelta migliore per l'analisi dei documenti JSON nidificati. Consente di definire lo schema JSON e quindi è possibile usare lo schema per analizzare i documenti. Per istruzioni, vedere [Come usare un Serde JSON personalizzato con Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Summary
In conclusione, il tipo di operatore JSON in Hive scelto dipende dallo scenario. Se è disponibile un documento JSON semplice ed è necessario eseguire ricerche in un solo campo, è possibile scegliere di usare la funzione Hive definita dall'utente get_json_object. Se è necessario cercare più di una chiave, è possibile usare json_tuple. Se è disponibile un documento annidato, è consigliabile usare il SerDe JSON.

## <a name="next-steps"></a>Passaggi successivi

Per gli articoli correlati, vedere:

* [Usare Hive e HiveQL con Hadoop in HDInsight per analizzare un file Apache log4j di esempio](../hdinsight-use-hive.md)
* [Analizzare i dati sui ritardi dei voli mediante Hive in HDInsight](../hdinsight-analyze-flight-delay-data.md)
* [Analizzare i dati Twitter tramite Hive in HDInsight](../hdinsight-analyze-twitter-data.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

