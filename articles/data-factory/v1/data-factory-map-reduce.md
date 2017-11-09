---
title: Richiamare il programma MapReduce da Data factory di Azure
description: Informazioni su come elaborare i dati eseguendo programmi MapReduce in un cluster Azure HDInsight da un'istanza di Data factory di Azure.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: e5fd49c6b269b5f247440c2bc91680fc77fc296c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Richiamare i programmi MapReduce da Data factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Attività Hive](data-factory-hive-activity.md) 
> * [Attività di Pig](data-factory-pig-activity.md)
> * [Attività MapReduce](data-factory-map-reduce.md)
> * [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Attività Spark](data-factory-spark.md)
> * [Attività di esecuzione batch di Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Attività della risorsa di aggiornamento di Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Attività stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata di .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Trasformare dati tramite l'attività MapReduce in Data Factory versione 2](../transform-data-using-hadoop-map-reduce.md).


L'attività HDInsight MapReduce in una [pipeline](data-factory-create-pipelines.md) di Data Factory esegue i programmi di MapReduce nei cluster HDInsight [personalizzati](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) basati su Windows/Linux. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

> [!NOTE] 
> Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo leggere [Introduzione ad Azure Data Factory](data-factory-introduction.md) ed eseguire l'esercitazione [Creare la prima pipeline di dati](data-factory-build-your-first-pipeline.md) .  

## <a name="introduction"></a>Introduzione
Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. In questo articolo viene descritto l'utilizzo dell'attività MapReduce di HDInsight.

Vedere [Pig](data-factory-pig-activity.md) e [Hive](data-factory-hive-activity.md) per informazioni dettagliate sull'esecuzione di script Pig/Hive in un cluster HDInsight basato su Windows/Linux da una pipeline usando le attività Pig e Hive di HDInsight. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON per attività MapReduce di HDInsight
Nella definizione JSON per l'attività HDInsight: 

1. Impostare l'oggetto **type** di **activity** su **HDInsight**.
2. Specificare il nome della classe per la proprietà **className** .
3. Specificare il percorso del file JAR, incluso il nome di file per la proprietà **jarFilePath** .
4. Specificare il servizio collegato che fa riferimento all'archivio BLOB di Azure contenente il file JAR per la proprietà **jarLinkedService** .   
5. Specificare gli eventuali argomenti per il programma MapReduce nella sezione **arguments** . In fase di esecuzione, vengono visualizzati alcuni argomenti aggiuntivi (ad esempio: mapreduce.job.tags) dal framework di MapReduce. Per differenziare gli argomenti con gli argomenti di MapReduce, è consigliabile usare sia l'opzione che il valore come argomenti, come illustrato nell'esempio seguente (- s, --input - output e così via sono opzioni immediatamente seguite dai valori).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
È possibile usare l’attività MapReduce di HDInsight per l'esecuzione di file JAR di MapReduce in un cluster HDInsight. Nella definizione JSON seguente di una pipeline di esempio l'attività HDInsight è configurata per eseguire un file JAR di Mahout.

## <a name="sample-on-github"></a>Esempio in GitHub
È possibile scaricare un esempio relativo all'uso dell'attività MapReduce di HDInsight da: [esempi di Data factory in GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Esecuzione del programma di conteggio delle parole
La pipeline in questo esempio esegue il programma di mapping e riduzione del conteggio delle parole sul cluster HDInsight di Azure.   

### <a name="linked-services"></a>Servizi collegati
In primo luogo, si crea un servizio collegato per collegare l'archiviazione di Azure utilizzata dal cluster HDInsight di Azure per la factory di dati di Azure. Se si copia e incolla il codice seguente, non dimenticare di sostituire il **nome account** e la **chiave account** con il nome e la chiave di archiviazione di Azure. 

#### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Servizio collegato Azure HDInsight
Successivamente, si crea un servizio collegato per collegare il cluster HDInsight di Azure alla factory di dati di Azure. Se si copia e incolla il codice seguente, sostituire **nome del cluster HDInsight** con il nome del cluster HDInsight e modificare i valori nome utente e password.   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Set di dati
#### <a name="output-dataset"></a>Set di dati di output
La pipeline in questo esempio non accetta alcun input. Specificare un set di dati di output per l'attività MapReduce di HDInsight. Questo è solo un set di dati fittizio necessario per la pianificazione della pipeline.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
La pipeline in questo esempio contiene una sola attività che è di tipo: HDInsightMapReduce. Alcune delle proprietà importanti in JSON sono: 

| Proprietà | Note |
|:--- |:--- |
| type |Il tipo deve essere impostato su **HDInsightMapReduce**. |
| className |Il nome della classe è: **wordcount** |
| jarFilePath |Percorso del file jar contenente la classe. Se si copia e incolla il codice seguente, non dimenticare di modificare il nome del cluster. |
| jarLinkedService |Servizio collegato di Archiviazione di Azure che contiene il file jar. Questo servizio collegato fa riferimento allo spazio di archiviazione associato al cluster HDInsight. |
| arguments |Il programma wordcount accetta due argomenti, un input e un output. Il file di input è il file davinci.txt. |
| frequenza/intervallo |I valori per queste proprietà corrispondono al set di dati di output. |
| linkedServiceName |fa riferimento al servizio collegato di HDInsight creato in precedenza. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Esecuzione dei programmi Spark
È possibile usare l'attività MapReduce per eseguire i programmi Spark nel cluster HDInsight Spark. Per i dettagli, vedere [Chiamare i programmi Spark da Azure Data Factory](data-factory-spark.md) .  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

## <a name="see-also"></a>Vedere anche
* [Attività Hive](data-factory-hive-activity.md)
* [Attività di Pig](data-factory-pig-activity.md)
* [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
* [Chiamare i programmi Spark](data-factory-spark.md)
* [Chiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

