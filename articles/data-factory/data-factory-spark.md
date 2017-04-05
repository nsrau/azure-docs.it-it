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
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 3c7d109ec7fd92859cad4ded7422105e8094485c
ms.lasthandoff: 03/30/2017


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

> [!IMPORTANT]
> Se non si ha familiarità con Azure Data Factory, si consiglia di consultare l'esercitazione [Creare la prima pipeline](data-factory-build-your-first-pipeline.md) prima di leggere questo articolo. Per una panoramica del servizio Data Factory, vedere [Introduzione ad Azure Data Factory](data-factory-introduction.md). 

## <a name="apache-spark-cluster-in-azure-hdinsight"></a>Cluster Apache Spark in HDInsight di Azure
Innanzitutto, creare un cluster Apache Spark in HDInsight di Azure seguendo le istruzioni riportate nell'esercitazione: [Creare il cluster Apache Spark in HDInsight di Azure](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="create-data-factory"></a>Creare un'istanza di Data Factory 
Di seguito viene illustrata la procedura usata in generale per creare una pipeline di Data Factory con un'attività Spark.  

1. Creare una data factory.
2. Creare un servizio collegato che colleghi il cluster Apache Spark in HDInsight di Azure alla data factory.
3. Attualmente, è necessario specificare un set di dati di output per un'attività anche se non viene prodotto alcun output. Per creare un set di dati del BLOB di Azure, seguire questa procedura:
    1. Creare un servizio collegato che colleghi l'account di archiviazione di Azure alla data factory.
    2. Creare un set di dati che faccia riferimento al servizio collegato di Archiviazione di Azure.  
3. Creare una pipeline con l'attività Spark che faccia riferimento al servizio collegato HDInsight di Apache creato in #2. L'attività è configurata con il set di dati creato nel passaggio precedente come un set di dati di output. Il set di dati di output è ciò su cui si basa la pianificazione (oraria, giornaliera e così via). Pertanto, è necessario specificare il set di dati di output anche se l'attività non ha prodotto alcun output.

Per istruzioni dettagliate sulla procedura per creare una data factory, vedere l'esercitazione: [Creare la prima pipeline](data-factory-build-your-first-pipeline.md). In questa esercitazione viene usata un'attività Hive con un cluster Hadoop di HDInsight, ma la procedura è simile a quella per l'uso di un'attività Spark con un cluster HDInsight Spark.   

Le sezioni seguenti contengono informazioni sulle entità di Data Factory e sull'uso del cluster Apache Spark e dell'attività di Spark nella data factory.   

## <a name="hdinsight-linked-service"></a>Servizio collegato HDInsight
Prima di usare un'attività Spark in una pipeline di Data Factory, creare un servizio collegato HDInsight (personale). Il frammento JSON seguente mostra come un servizio collegato HDInsight è definito in modo da puntare al cluster HDInsight Spark di Azure.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<nameofyoursparkcluster>.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> Attualmente l'attività Spark non supporta i cluster Spark che usano Azure Data Lake Store come risorsa di archiviazione primaria o il servizio collegato HDInsight su richiesta. 

Per conoscere i dettagli del servizio collegato HDInsight e di altri servizi collegati di calcolo, vedere l'articolo [Servizi collegati di calcolo di Data Factory](data-factory-compute-linked-services.md). 

## <a name="spark-activity-json"></a>JSON dell'attività Spark
Di seguito è riportata la definizione JSON di esempio di una pipeline con attività Spark:    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

Tenere presente quanto segue: 
- La proprietà type è impostata su HDInsightSpark. 
- Il rootPath è impostato su dfspark\\pyFiles dove adfspark è il contenitore BLOB di Azure e pyFiles è cartella sottile in tale contenitore. In questo esempio, l'archivio BLOB di Azure è quello associato al cluster Spark. È possibile caricare il file in un archivio di Azure diverso. In tal caso, creare un servizio collegato Archiviazione di Azure per collegare l'account di archiviazione alla data factory. Quindi, specificare il nome del servizio collegato come valore per la proprietà sparkJobLinkedService. Vedere [Proprietà dell'attività Spark](#spark-activity-properties) per informazioni dettagliate su questa e altre proprietà supportate dall'attività Spark.  
- La proprietà entryFilePath è impostata su test.py, ovvero il file di Python. 
- Usando la proprietà arguments vengono passati i valori dei parametri per il programma Spark. In questo esempio, sono disponibili due argomenti: arg1 e arg2. 
- La proprietà getDebugInfo è impostata su Sempre e indica che i file di log vengono sempre generati (con esito positivo o negativo). 
- La sezione sparkConfig specifica un'impostazione dell'ambiente Python: worker.memory. 
- La sezione di output presenta un set di dati di output. Anche se il programma Spark non genera alcun output, è necessario specificare un set di dati di output. Il set di dati di output è ciò su cui si basa la pianificazione della pipeline (oraria, giornaliera e così via).     

Le proprietà type (nella sezione typeProperties) sono descritte più avanti in questo articolo nella sezione [Proprietà dell'attività Spark](#spark-activity-properties). 

Come accennato in precedenza, è necessario specificare un set di dati di output per l'attività poiché è su questo che si basa la pianificazione della pipeline (oraria, giornaliera e così via). In questo esempio viene usato un set di dati del BLOB di Azure. Per creare un set di dati del BLOB di Azure, è necessario creare innanzitutto un servizio collegato Archiviazione di Azure. 

Ecco le definizioni di esempio del servizio collegato Archiviazione di Azure e del set di dati del BLOB di Azure: 

**Servizio collegato Archiviazione di Azure:**
```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>"
        }
    }
}
```
 

**Set di dati del BLOB di Azure:** 
```json
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "sparkoutput.txt",
            "folderPath": "spark/output/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Questo set di dati è più di un set di dati fittizi. Data Factory usa le impostazioni di frequenza e intervallo ed esegue la pipeline ogni giorno entro gli orari di inizio e fine di una pipeline. Nella definizione della pipeline di esempio, l'ora di inizio e fine sono separate da un giorno, pertanto la pipeline viene eseguita una sola volta. 

## <a name="spark-activity-properties"></a>Proprietà dell'attività Spark

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON: 

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| name | Nome dell'attività nella pipeline. | Sì |
| Descrizione | Testo che descrive l'attività. | No |
| type | Questa proprietà deve essere impostata su HDInsightSpark. | Sì |
| linkedServiceName | Riferimento a un servizio collegato HDInsight in cui viene eseguito il programma Spark. | Sì |
| rootPath | Contenitore BLOB di Azure e cartella che contiene il file Spark. Il nome del file distingue tra maiuscole e minuscole. | Sì |
| entryFilePath | Percorso relativo alla cartella radice del pacchetto/codice Spark. | Sì |
| className | Classe principale Java/Spark dell'applicazione | No | 
| arguments | Elenco di argomenti della riga di comando del programma Spark. | No | 
| proxyUser | Account utente da rappresentare per eseguire il programma Spark | No | 
| sparkConfig | Proprietà di configurazione di Spark. | No | 
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


