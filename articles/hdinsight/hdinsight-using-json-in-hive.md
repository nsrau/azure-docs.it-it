---
title: Analizzare ed elaborare documenti JSON con Hive in HDInsight | Microsoft Docs
description: Informazioni su come usare e analizzare i documenti JSON usando Hive in HDInsight.
services: hdinsight
documentationcenter: 
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
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: ee7d40d2ff0ae1ac10b54f4c1f1dd704a70eb70c
ms.contentlocale: it-it
ms.lasthandoff: 09/06/2017

---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Elaborare e analizzare documenti JSON usando Hive in HDInsight

Informazioni su come elaborare e analizzare file JSON usando Hive in HDInsight. Nell'esercitazione viene usato il documento JSON seguente:

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

Il file è disponibile in wasb://processjson@hditutorialdata.blob.core.windows.net/. Per altre informazioni sull'uso dell'archivio BLOB di Azure con HDInsight, vedere [Usare un archivio BLOB di Azure compatibile con HDFS con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md). È possibile copiare il file nel contenitore predefinito del cluster.

In questa esercitazione viene usata la console di Hive.  Per istruzioni sull'apertura della console Hive, vedere [Usare Hive con Hadoop in HDInsight con Desktop remoto](hdinsight-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Rendere flat i documenti JSON
I metodi elencati nella sezione seguente presuppongono che il documento JSON sia in una singola riga. È quindi necessario rendere flat il documento JSON in una stringa. Se il documento JSON è già flat, è possibile saltare questo passaggio e passare alla sezione successiva relativa all'analisi dei dati JSON.

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

Il file JSON non elaborato è disponibile in **wasb://processjson@hditutorialdata.blob.core.windows.net/**. La tabella Hive *StudentsRaw* punta al documento JSON non elaborato e non flat.

La tabella Hive *StudentsOneLine* archivia i dati nel file system predefinito di HDInsight nel percorso */json/students/*.

L'istruzione INSERT popola la tabella StudentOneLine con i dati JSON flat.

L'istruzione SELECT restituirà solo una riga.

Ecco l'output dell'istruzione SELECT:

![Rendere flat il documento JSON.][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analizzare i documenti JSON in Hive
Hive offre tre meccanismi diversi per l'esecuzione di query nei documenti JSON:

* Usare la funzione definita dall'utente GET\_JSON\_OBJECT UDF.
* Usare la funzione definita dall'utente JSON_TUPLE.
* Usare l'interfaccia SerDe personalizzata.
* Scrivere una funzione definita dall'utente personalizzata usando Python o altri linguaggi. Vedere [questo articolo][hdinsight-python] sull'esecuzione del codice Python personalizzato con Hive.

### <a name="use-the-getjsonobject-udf"></a>Usare la funzione definita dall'utente GET\_JSON_OBJECT
Hive offre una funzione definita dall'utente predefinita denominata [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) in grado di eseguire query JSON in fase di esecuzione. Questo metodo accetta due argomenti, ovvero il nome della tabella e il nome del metodo che include il documento JSON flat e il campo JSON da analizzare. L'esempio seguente illustra il funzionamento di questa funzione definita dall'utente.

Ottenere il nome e il cognome di ogni studente

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Questo è l'output ottenuto quando si esegue la query nella finestra della console.

![Funzione definita dall'utente get_json_object][image-hdi-hivejson-getjsonobject]

La funzione definita dall'utente get-json_object presenta alcune limitazioni.

* Poiché ogni campo della query richiede una nuova analisi della query, si ha un impatto sulle prestazioni.
* GET\_JSON_OBJECT() restituisce la rappresentazione di stringa di una matrice. Per convertirla in una matrice Hive, è necessario usare espressioni regolari per sostituire le parentesi quadre ('[' e ']') e quindi chiamare anche una suddivisione per ottenere la matrice.

Il wiki relativo a Hive consiglia quindi di usare json_tuple, come illustrato più avanti.  

### <a name="use-the-jsontuple-udf"></a>Usare la funzione definita dall'utente JSON_TUPLE
L'altra funzione definita dall'utente disponibile in Hive è denominata [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) e offre prestazioni migliori rispetto a [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Questo metodo accetta un insieme di chiavi e una stringa JSON e restituisce una tupla di valori usando una funzione. La query seguente restituisce l'ID dello studente e il livello dal documento JSON:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Output dello script nella console di Hive:

![Funzione definita dall'utente json_tuple][image-hdi-hivejson-jsontuple]

JSON\_TUPLE usa la sintassi di tipo [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in Hive, che consente a json\_tuple di creare una tabella virtuale applicando la funzione UDT a ogni riga della tabella originale.  I documenti JSON complessi diventano troppo difficili da gestire a causa dell'uso ripetuto di LATERAL VIEW. JSON_TUPLE non è in grado di gestire documenti JSON annidati.

### <a name="use-custom-serde"></a>Usare l'interfaccia SerDe personalizzata
SerDe è la scelta migliore per l'analisi di documenti JSON annidati, perché consente di definire lo schema JSON e di usarlo per analizzare i documenti. Per istruzioni, vedere [Come usare un Serde JSON personalizzato con Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Riepilogo
In conclusione, il tipo di operatore JSON in Hive scelto dipende dallo scenario. Se è disponibile un documento JSON semplice ed è necessario eseguire ricerche in un solo campo, è possibile scegliere di usare la funzione Hive definita dall'utente get\_json\_object. Se è necessario cercare più di una chiave, è possibile usare json_tuple. Se è disponibile un documento annidato, è consigliabile usare il SerDe JSON.

## <a name="next-steps"></a>Passaggi successivi

Per altri articoli correlati, vedere

* [Usare Hive e HiveQL con Hadoop in HDInsight per analizzare un file Apache log4j di esempio](hdinsight-use-hive.md)
* [Analizzare i dati sui ritardi dei voli mediante Hive in HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analizzare i dati Twitter mediante Hive in HDInsight](hdinsight-analyze-twitter-data.md)
* [Eseguire un processo Hadoop usando Azure Cosmos DB e HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

