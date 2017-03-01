---
title: "Trasformare dati usando l&quot;attività di streaming di Hadoop - Azure | Documentazione Microsoft"
description: "Informazioni su come usare l&quot;attività di Hadoop Streaming in una Data factory di Azure per trasformare i dati eseguendo i programmi di Hadoop Streaming in un cluster HDInsight personalizzato o su richiesta."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/24/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 49545648a069f25820f6a6e483072be8410b091e
ms.openlocfilehash: 56754bde961708f04477be74e1ae80acddf2c0cd


---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Trasformare dati usando l'attività di streaming di Hadoop in Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata .NET](data-factory-use-custom-activities.md)

È possibile usare l'attività HDInsightStreamingActivity per richiamare un processo di Hadoop Streaming da una pipeline di Data factory di Azure. Il frammento JSON seguente illustra la sintassi per l'uso di HDInsightStreamingActivity in un file JSON della pipeline. 

L'attività HDInsight Streaming Activity in una [pipeline](data-factory-create-pipelines.md) di Data Factory esegue i programmi di Hadoop Streaming nei cluster HDInsight [personalizzati](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) basati su Windows/Linux. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

## <a name="json-sample"></a>Esempio JSON
Il cluster HDInsight viene popolato automaticamente con programmi di esempio (wc.exe e cat.exe) e con i dati (davinci.txt). Per impostazione predefinita, il nome del contenitore che viene utilizzato dal cluster HDInsight è il nome del cluster stesso. Ad esempio, se il nome del cluster è myhdicluster, il nome del contenitore BLOB associato sarebbe myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Tenere presente quanto segue:

1. Impostare **linkedServiceName** sul nome del servizio collegato che punta al cluster HDInsight in cui viene eseguito il processo di streaming mapreduce.
2. Impostare il tipo di attività su **HDInsightStreaming**.
3. Per la proprietà **mapper** specificare il nome dell'eseguibile del mapper. Nell'esempio cat.exe è l'eseguibile del mapper.
4. Per la proprietà **reducer** specificare il nome dell'eseguibile del reducer. Nell'esempio wc.exe è l'eseguibile del mapper.
5. Per la proprietà di tipo **input** specificare il file di input (incluso il percorso) per il mapper. Nell'esempio: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample è il contenitore BLOB, example/data/Gutenberg è la cartella e davinci.txt è il BLOB.
6. Per la proprietà di tipo **output** specificare il file di output (incluso il percorso) per il reducer. L'output del processo di Hadoop Streaming viene scritto nel percorso specificato per questa proprietà.
7. Nella sezione **filePaths** specificare i percorsi dei file eseguibili del mapper e del reducer. Nell'esempio: "adfsample/example/apps/wc.exe", adfsample è il contenitore BLOB, example/apps è la cartella e wc.exe è l'eseguibile.
8. Per la proprietà **fileLinkedService** specificare il servizio collegato Archiviazione di Azure che rappresenta l'archivio di Azure contenente i file specificati nella sezione filePaths.
9. Per la proprietà **arguments** specificare gli argomenti per il processo di streaming.
10. La proprietà **getDebugInfo** è un elemento facoltativo. Quando viene impostata su Failure, i log vengono scaricati solo in caso di errore. Quando viene impostata su All, i log vengono sempre scaricati indipendentemente dallo stato dell'esecuzione.

> [!NOTE]
> Come illustrato nell'esempio, è necessario specificare un set di dati di output per l'attività di Hadoop Streaming per la proprietà **output** . Questo è solo un set di dati fittizio necessario per la pianificazione della pipeline. Non è necessario specificare alcun set di dati di input per l'attività per la proprietà **input** .  
> 
> 

## <a name="example"></a>Esempio
La pipeline in questa procedura dettagliata esegue il programma di mapping e riduzione dello streaming del conteggio delle parole sul cluster HDInsight di Azure. 

### <a name="linked-services"></a>Servizi collegati
#### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
In primo luogo, si crea un servizio collegato per collegare l'archiviazione di Azure utilizzata dal cluster HDInsight di Azure per la factory di dati di Azure. Se si copia e incolla il codice seguente, non dimenticare di sostituire il nome account e la chiave account con il nome e la chiave di archiviazione di Azure. 

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
Successivamente, si crea un servizio collegato per collegare il cluster HDInsight di Azure alla factory di dati di Azure. Se si copia e incolla il codice seguente, sostituire il nome del cluster HDInsight con il nome del cluster HDInsight e modificare i valori nome utente e password. 

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
La pipeline in questo esempio non accetta alcun input. È necessario specificare un set di dati di output per l'attività Streaming di HDInsight. Questo è solo un set di dati fittizio necessario per la pianificazione della pipeline. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
La pipeline in questo esempio contiene una sola attività che è di tipo: **HDInsightStreaming**. 

Il cluster HDInsight viene popolato automaticamente con programmi di esempio (wc.exe e cat.exe) e con i dati (davinci.txt). Per impostazione predefinita, il nome del contenitore che viene utilizzato dal cluster HDInsight è il nome del cluster stesso. Ad esempio, se il nome del cluster è myhdicluster, il nome del contenitore BLOB associato sarebbe myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Vedere anche
* [Attività Hive](data-factory-hive-activity.md)
* [Attività di Pig](data-factory-pig-activity.md)
* [Attività MapReduce](data-factory-map-reduce.md)
* [Chiamare i programmi Spark](data-factory-spark.md)
* [Chiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)




<!--HONumber=Jan17_HO4-->


