---
title: Chiamare i programmi Spark da Azure Data Factory
description: "È possibile chiamare i programmi Spark da una data factory di Azure usando l&quot;attività MapReduce."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 8be5e1525a7c481de5cb02edd26da305af2d4798
ms.lasthandoff: 03/18/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>Chiamare i programmi Spark da Data Factory
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

## <a name="introduction"></a>Introduzione
L'attività Spark di HDInsight in una [pipeline](data-factory-create-pipelines.md) di Data Factory esegue i programmi Spark nel cluster [personale](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) HDInsight. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

## <a name="hdinsight-linked-service"></a>Servizio collegato HDInsight
Prima di usare un'attività Spark in una pipeline di Data Factory, creare un servizio collegato HDInsight (personale). Il frammento JSON seguente mostra come un servizio collegato HDInsight è definito in modo da puntare al cluster Spark personale di Azure HDInsight.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://MyHdinsightSparkcluster.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> Attualmente l'attività Spark non supporta i cluster Spark che usano Data Lake Store come archiviazione primaria o il servizio collegato HDInsight su richiesta. 

Per conoscere i dettagli del servizio collegato HDInsight e di altri servizi collegati di calcolo, vedere l'articolo [Servizi collegati di calcolo di Data Factory](data-factory-compute-linked-services.md). 

## <a name="spark-activity-json"></a>JSON dell'attività Spark
Di seguito è riportata la definizione JSON di esempio di un'attività Spark:    

```json
{
    "name": "MySparkActivity",
    "description": "This activity invokes the Spark program",
    "type": "HDInsightSpark",
    "outputs": [
        {
            "name": "PlaceholderDataset"
        }
    ],
    "linkedServiceName": "HDInsightLinkedService",
    "typeProperties": {
        "rootPath": "mycontainer\\myfolder",
        "entryFilePath": "main.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
              "spark.python.worker.memory": "512m"
        }
    }
}
```
La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON: 

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| name | Nome dell'attività nella pipeline. | Sì |
| Descrizione | Testo che descrive l'attività. | No |
| type | Questa proprietà deve essere impostata su HDInsightSpark. | Sì |
| linkedServiceName | Riferimento a un servizio collegato HDInsight in cui viene eseguito il programma Spark. | Sì |
| rootPath | Contenitore BLOB di Azure e cartella che contiene il file Spark. Il nome del file distingue tra maiuscole e minuscole. | Sì |
| entryFilePath | Percorso relativo alla cartella radice del pacchetto/codice Spark | Sì |
| className | Classe principale Java/Spark dell'applicazione | No | 
| arguments | Elenco di argomenti della riga di comando del programma Spark. | No | 
| proxyUser | Account utente da rappresentare per eseguire il programma Spark | No | 
| sparkConfig | Proprietà di configurazione di Spark | No | 
| getDebugInfo | Specifica quando i file di log di Spark vengono copiati nell'archiviazione di Azure usata dal cluster HDInsight (o) specificata da sparkJobLinkedService. Valori consentiti: None, Always o Failure. Valore predefinito: None. | No | 
| sparkJobLinkedService | Il servizio collegato di archiviazione di Azure che contiene il file di processo, le dipendenze e i log di Spark.  Se non si specifica un valore per questa proprietà, viene usato lo spazio di archiviazione associato al cluster HDInsight. | No |

## <a name="folder-structure"></a>Struttura di cartelle
L'attività Spark non supporta uno script inline come invece fanno le attività Pig e Hive. I processi Spark sono anche più estendibili dei processi Pig/Hive. Per i processi Spark è possibile offrire più dipendenze, ad esempio pacchetti jar (posizionati in CLASSPATH di java), file python (posizionati in PYTHONPATH) e qualsiasi altro file.

Creare la struttura seguente di cartelle nell'archivio BLOB di Azure a cui fa riferimento il servizio collegato HDInsight. Caricare i file dipendenti nelle sottocartelle appropriate all'interno della cartella radice rappresentata da **entryFilePath**. Ad esempio, caricare i file python nella sottocartella pyFiles e i file jar nella sottocartella jars della cartella radice. In fase di esecuzione, il servizio Data Factory prevede la struttura di cartelle seguente nell'archivio BLOB di Azure:     

| Path | Descrizione | Obbligatorio | Tipo |
| ---- | ----------- | -------- | ---- | 
| .    | Percorso radice del processo Spark nel servizio collegato di archiviazione    | Sì | Cartella |
| &lt;definito dall'utente &gt; | Percorso che punta al file di ingresso del processo Spark | Sì | File | 
| ./jars | Tutti i file in questa cartella vengono caricati e inseriti nel classpath java del cluster | No | Cartella |
| ./pyFiles | Tutti i file in questa cartella vengono caricati e inseriti nel PYTHONPATH del cluster | No | Cartella |
| ./files | Tutti i file in questa cartella vengono caricati e inseriti nella directory di lavoro executor | No | Cartella |
| ./archives | Tutti i file in questa cartella sono decompressi | No | Cartella |
| ./logs | Cartella in cui sono archiviati i log del cluster Spark.| No | Cartella |

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

> [!IMPORTANT]
> Per conoscere la procedura dettagliata completa per creare una pipeline con un'attività di trasformazione, vedere l'articolo [Creare una pipeline per trasformare i dati](data-factory-build-your-first-pipeline-using-editor.md). 



## <a name="see-also"></a>Vedere anche
* [Attività Hive](data-factory-hive-activity.md)
* [Attività di Pig](data-factory-pig-activity.md)
* [Attività MapReduce](data-factory-map-reduce.md)
* [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
* [Chiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


