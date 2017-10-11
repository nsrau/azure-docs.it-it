---
title: "Trasformare dati tramite l'attività Spark in Azure Data Factory | Microsoft Docs"
description: "Informazioni su come trasformare dati tramite l'esecuzione di programmi Spark da una pipeline di Azure Data Factory con l'attività Spark."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shengc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ecef890d26aa097d9a5db63555ae59e16e80c9e1
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Trasformare dati tramite l'attività Spark in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-spark.md)
> * [Versione 2 - Anteprima](transform-data-using-spark.md)


L'attività Spark è una delle [attività di trasformazione dei dati](transform-data.md) supportate da Azure Data Factory. Questa attività esegue il programma Spark specificato nel cluster Apache Spark in Azure HDInsight.    

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività Spark nella versione 1](v1/data-factory-spark.md).

> [!IMPORTANT]
> L'attività Spark non supporta i cluster Spark HDInsight che usano Azure Data Lake Store come risorsa di archiviazione primaria.

## <a name="spark-activity-properties"></a>Proprietà dell'attività Spark
Ecco la definizione JSON di esempio di un'attività Spark:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

| Proprietà              | Descrizione                              | Obbligatorio |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Nome dell'attività nella pipeline.    | Sì      |
| Descrizione           | Testo che descrive l'attività.  | No       |
| type                  | Per l'attività Spark il tipo corrisponde a HDInsightSpark. | Sì      |
| linkedServiceName     | Nome del servizio collegato di HDInsight Spark in cui viene eseguito il programma Spark. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Sì      |
| SparkJobLinkedService | Il servizio collegato di archiviazione di Azure che contiene il file di processo, le dipendenze e i log di Spark.  Se non si specifica un valore per questa proprietà, viene usato lo spazio di archiviazione associato al cluster HDInsight. | No       |
| rootPath              | Contenitore BLOB di Azure e cartella che contiene il file Spark. Il nome del file distingue tra maiuscole e minuscole. Per informazioni dettagliate sulla struttura della cartella, fare riferimento alla prossima sezione, relativa alla struttura delle cartelle. | Sì      |
| entryFilePath         | Percorso relativo alla cartella radice del pacchetto/codice Spark. | Sì      |
| className             | Classe principale Java/Spark dell'applicazione      | No       |
| arguments             | Elenco di argomenti della riga di comando del programma Spark. | No       |
| proxyUser             | Account utente da rappresentare per eseguire il programma Spark | No       |
| sparkConfig           | Specificare i valori delle proprietà di configurazione di Spark elencati nell'argomento: [Configurazione di SparK: proprietà dell'applicazione](https://spark.apache.org/docs/latest/configuration.html#available-properties). | No       |
| getDebugInfo          | Specifica quando i file di log di Spark vengono copiati nell'archiviazione di Azure usata dal cluster HDInsight (o) specificata da sparkJobLinkedService. Valori consentiti: None, Always o Failure. Valore predefinito: None. | No       |

## <a name="folder-structure"></a>Struttura di cartelle
I processi Spark sono più estendibili dei processi Pig/Hive. Per i processi Spark è possibile offrire più dipendenze, ad esempio pacchetti jar (posizionati in CLASSPATH di java), file python (posizionati in PYTHONPATH) e qualsiasi altro file.

Creare la struttura seguente di cartelle nell'archivio BLOB di Azure a cui fa riferimento il servizio collegato HDInsight. Caricare i file dipendenti nelle sottocartelle appropriate all'interno della cartella radice rappresentata da **entryFilePath**. Ad esempio, caricare i file python nella sottocartella pyFiles e i file jar nella sottocartella jars della cartella radice. In fase di esecuzione, il servizio Data Factory prevede la struttura di cartelle seguente nell'archivio BLOB di Azure:     

| Path                  | Descrizione                              | Obbligatorio | Tipo   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (radice)            | Percorso radice del processo Spark nel servizio collegato di archiviazione | Sì      | Cartella |
| &lt;definito dall'utente &gt; | Percorso che punta al file di ingresso del processo Spark | Sì      | File   |
| ./jars                | Tutti i file in questa cartella vengono caricati e inseriti nel classpath java del cluster | No       | Cartella |
| ./pyFiles             | Tutti i file in questa cartella vengono caricati e inseriti nel PYTHONPATH del cluster | No       | Cartella |
| ./files               | Tutti i file in questa cartella vengono caricati e inseriti nella directory di lavoro executor | No       | Cartella |
| ./archives            | Tutti i file in questa cartella sono decompressi | No       | Cartella |
| ./logs                | Cartella contenente i log del cluster Spark. | No       | Cartella |

Di seguito è riportato un esempio per una risorsa di archiviazione che contiene due file di processo Spark nell'archivio BLOB di Azure a cui fa riferimento il servizio collegato HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati: 

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)

