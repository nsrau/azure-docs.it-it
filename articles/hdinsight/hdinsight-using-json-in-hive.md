---
title: Analizzare ed elaborare documenti JSON con Hive in HDInsight | Documentazione Microsoft
description: Informazioni su come usare e analizzare i documenti JSON usando Hive in HDInsight.
services: hdinsight
documentationcenter: 
author: rashimg
manager: mwinkle
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/22/2015
ms.author: rashimg
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: c292772cb21c90bf4373803bfcaa47787c3980b5
ms.lasthandoff: 03/06/2017


---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Elaborare e analizzare documenti JSON usando Hive in HDInsight
Informazioni su come elaborare e analizzare file JSON usando Hive in HDInsight. Nell'esercitazione verrà usato il documento JSON seguente.

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

Il file è disponibile in wasbs://processjson@hditutorialdata.blob.core.windows.net/. Per altre informazioni sull'uso dell'archivio BLOB di Azure con HDInsight, vedere [Usare un archivio BLOB di Azure compatibile con HDFS con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md). È possibile copiare il file nel contenitore predefinito del cluster, se necessario.

In questa esercitazione si userà la console di Hive.  Per istruzioni sull'apertura della console Hive, vedere [Usare Hive con Hadoop in HDInsight con Desktop remoto](hdinsight-hadoop-use-hive-remote-desktop.md).

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

Il file JSON non elaborato è disponibile in **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. La tabella Hive *StudentsRaw* punta al documento JSON non elaborato e non flat.

La tabella Hive *StudentsOneLine* archivierà i dati nel file system predefinito di HDInsight nel percorso */json/students/*.

L'istruzione INSERT popola la tabella StudentOneLine con i dati JSON flat.

L'istruzione SELECT restituirà solo una riga.

Ecco l'output dell'istruzione SELECT:

![Rendere flat il documento JSON.][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analizzare i documenti JSON in Hive
Hive offre tre meccanismi diversi per l'esecuzione di query nei documenti JSON:

* Usare la funzione definita dall'utente GET\_JSON\_OBJECT
* Usare la funzione definita dall'utente JSON_TUPLE.
* Usare l'interfaccia SerDe personalizzata.
* Scrivere una funzione definita dall'utente personalizzata usando Python o altri linguaggi. Vedere [questo articolo][hdinsight-python] sull'esecuzione del codice Python personalizzato con Hive.

### <a name="use-the-getjsonobject-udf"></a>Usare la funzione definita dall'utente GET\_JSON_OBJECT
Hive offre una funzione definita dall'utente predefinita denominata [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) , in grado di eseguire query JSON in fase di esecuzione. Questo metodo accetta due argomenti, ovvero il nome della tabella e il nome del metodo che include il documento JSON flat e il campo JSON da analizzare. L'esempio seguente illustra il funzionamento di questa funzione definita dall'utente.

Ottenere il nome e il cognome di ogni studente

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Questo è l'output ottenuto quando si esegue la query nella finestra della console.

![Funzione definita dall'utente get_json_object][image-hdi-hivejson-getjsonobject]

La funzione definita dall'utente get-json_object presenta alcune limitazioni.

* Poiché ogni campo della query richiede una nuova analisi della query, influisce sulle prestazioni.
* GET\_JSON_OBJECT() restituisce la rappresentazione di stringa di una matrice. Per convertirla in una matrice Hive, si dovranno usare espressioni regolari per sostituire le parentesi quadre ('[' e ']') e quindi chiamare anche una suddivisione per ottenere la matrice.

Il wiki relativo a Hive consiglia quindi di usare json_tuple, come illustrato più avanti.  

### <a name="use-the-jsontuple-udf"></a>Usare la funzione definita dall'utente JSON_TUPLE
L'altra funzione definita dall'utente fornita da Hive è denominata [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) e offre prestazioni migliori rispetto a [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Questo metodo accetta un insieme di chiavi e una stringa JSON e restituisce una tupla di valori usando una funzione. La query seguente restituisce l'ID dello studente e il livello dal documento JSON:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Output dello script nella console di Hive:

![Funzione definita dall'utente json_tuple][image-hdi-hivejson-jsontuple]

JSON\_TUPLE usa la sintassi di tipo [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in Hive, che consente a json\_tuple di creare una tabella virtuale applicando la funzione UDT a ogni riga della tabella originale.  I documenti JSON complessi diventano troppo difficili da gestire a causa dell'uso ripetuto di LATERAL VIEW. JSON_TUPLE non è in grado di gestire documenti JSON annidati.

### <a name="use-custom-serde"></a>Usare l'interfaccia SerDe personalizzata
SerDe è la scelta migliore per l'analisi di documenti JSON annidati, perché consente di definire lo schema JSON e di usarlo per analizzare i documenti. In questa esercitazione si userà una delle SerDe più diffusi, sviluppata da [rcongiu](https://github.com/rcongiu).

**Per usare l'interfaccia SerDe personalizzata:**

1. Installare [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Se si usa la distribuzione Windows di HDInsight, scegliere la versione Windows X64 del JDK.
   
   > [!WARNING]
   > JDK 1.8 non funziona con questa SerDe.
   > 
   > 
   
    Al termine dell'installazione, aggiungere una nuova variabile di ambiente utente:
   
   1. Aprire **Visualizza impostazioni di sistema avanzate** dalla schermata di Windows.
   2. Fare clic su **Variabili di ambiente**.  
   3. Aggiungere una nuova variabile di ambiente **JAVA_HOME** che punta a **C:\Programmi\Java\jdk1.7.0_55** o al percorso di installazione del JDK.
      
      ![Configurazione dei valori di configurazione corretti per JDK][image-hdi-hivejson-jdk]
2. Installare [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)
   
    Aggiungere la cartella Bin al percorso scegliendo Pannello di controllo-->Modifica le variabili di ambiente relative al sistema per l'account, quindi scegliere Variabili di ambiente.  La schermata seguente mostra come procedere.
   
    ![Configurazione di Maven][image-hdi-hivejson-maven]
3. Clonare il progetto dal sito GitHub [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) . Per eseguire questa operazione, fare clic sul pulsante "Download Zip", come mostrato nella schermata seguente.
   
    ![Clonazione del progetto][image-hdi-hivejson-serde]

4: Passare alla cartella in cui è stato scaricato il pacchetto, quindi digitare "mvn package". Verranno creati i file jar necessari, che verranno quindi copiati nel cluster.

5: Passare alla cartella di destinazione nella cartella radice in cui è stato scaricato il pacchetto. Caricare il file json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar nel nodo head del cluster. È in genere consigliabile inserirlo sotto la cartella dei file binari di Hive, ad esempio C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin o in una cartella analoga.

6: Al prompt di Hive digitare "add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Poiché in questo esempio il file jar si trova nella cartella C:\apps\dist\hive-0.13.x\bin, è possibile aggiungere direttamente il file jar con il nome, come illustrato di seguito:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

   ![Aggiunta di JAR al progetto][image-hdi-hivejson-addjar]

A questo punto è possibile usare SerDe per eseguire le query sul documento JSON.

L'istruzione seguente crea una tabella con uno schema definito.

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Per elencare il nome e il cognome dello studente

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Questo è il risultato ottenuto dalla console di Hive.

![Query SerDe 1][image-hdi-hivejson-serde_query1]

Per calcolare la somma dei punteggi del documento JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

La query precedente usa la funzione definita dall'utente di tipo [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) per espandere la matrice di punteggi, in modo che sia possibile riepilogarli.

Questo è l'output ottenuto dalla console di Hive.

![Query SerDe 2][image-hdi-hivejson-serde_query2]

Per trovare le materie in cui un determinato studente ha ottenuto un punteggio superiore a 80 SELECT  
      jt.StudentClassCollection.ClassId FROM json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score  where score > 80;

La query precedente restituisce una matrice Hive, a differenza di get\_json\_object che restituisce una stringa.

![Query SerDe 3][image-hdi-hivejson-serde_query3]

Se si vogliono ignorare documenti JSON non validi, come illustrato nella [pagina wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) di questo SerDe, è possibile ottenere questo risultato digitando il codice seguente:  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




## <a name="summary"></a>Riepilogo
In conclusione, il tipo di operatore JSON in Hive scelto dipende dallo scenario. Se è disponibile un documento JSON semplice ed è necessario eseguire ricerche in un solo campo, è possibile scegliere di usare la funzione Hive definita dall'utente get\_json\_object. Se è necessario cercare più di una chiave, sarà possibile usare json_tuple. Se è disponibile un documento annidato, è consigliabile usare il SerDe JSON.

Per altri articoli correlati, vedere

* [Usare Hive e HiveQL con Hadoop in HDInsight per analizzare un file Apache log4j di esempio](hdinsight-use-hive.md)
* [Analizzare i dati sui ritardi dei voli mediante Hive in HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analizzare i dati Twitter mediante Hive in HDInsight](hdinsight-analyze-twitter-data.md)
* [Eseguire un processo Hadoop usando DocumentDB e HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

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

