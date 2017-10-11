---
title: "Trasformare dati usando l'attività Hive - Azure | Documentazione Microsoft"
description: "Informazioni su come usare l'attività Hive in una data factory di Azure per eseguire query Hive in un cluster HDInsight su richiesta o nel proprio cluster HDInsight."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0b2d6580f30044b703acb3e3f53066a45f4f7b18
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Trasformare dati usando l'attività Hive in Azure Data Factory 
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

L'attività Hive di HDInsight in una [pipeline](data-factory-create-pipelines.md) di Data Factory esegue query Hive sul [proprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight o sul cluster HDInsight [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) basato su Windows o Linux. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

> [!NOTE] 
> Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo leggere [Introduzione ad Azure Data Factory](data-factory-introduction.md) ed eseguire l'esercitazione [Creare la prima pipeline di dati](data-factory-build-your-first-pipeline.md) . 

## <a name="syntax"></a>Sintassi

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Dettagli sintassi
| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| name |Nome dell'attività |Sì |
| Descrizione |Testo descrittivo per lo scopo dell'attività |No |
| type |HDinsightHive |Sì |
| inputs |Input utilizzati dall'attività Hive |No |
| outputs |Output generati dall'attività Hive |Sì |
| linkedServiceName |Riferimento al cluster HDInsight registrato come servizio collegato in Data factory |Sì |
| script |Specificare lo script Hive inline |No |
| script path |Archiviare lo script Hive in un archivio BLOB di Azure e immettere il percorso del file. Usare la proprietà "script" o "scriptPath". Non è possibile usare entrambe le proprietà. Il nome del file distingue tra maiuscole e minuscole. |No |
| defines |Specificare i parametri come coppie chiave/valore per fare riferimento ad essi nello script Hive usando "hiveconf" |No |

## <a name="example"></a>Esempio
Si prenda ad esempio l'analisi di log di giochi, in cui si desidera verificare il tempo che gli utenti hanno dedicato a giocare alle partite avviate dalla società. 

Il log seguente è un log di esempio di un gioco in cui i valori sono separati da virgola (`,`) e che contiene i campi ProfileID, SessionStart, Duration, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Lo **script Hive** per elaborare tali dati sarà:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Per eseguire lo script Hive in una pipeline di Data factory, è necessario seguire questa procedura:

1. Creare un servizio collegato per registrare [il proprio cluster di elaborazione di HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) oppure configurare [un cluster di elaborazione di HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). In questo esempio il servizio collegato è denominato "HDInsightLinkedService".
2. Creare un [servizio collegato](data-factory-azure-blob-connector.md) per configurare la connessione all'archivio BLOB di Azure che ospita i dati. In questo esempio il servizio collegato è denominato "StorageLinkedService".
3. Creare [set di dati](data-factory-create-datasets.md) che puntano ai dati di input e output. In questo esempio il set di dati di input è denominato "HiveSampleIn", mentre il set di dati di output è denominato "HiveSampleOut".
4. Copiare la query Hive come file nell'archivio BLOB di Azure configurato nel passaggio 2. Se la risorsa di archiviazione che deve ospitare i dati è diverso da quello che ospita il file di query, creare un altro servizio collegato di Archiviazione di Azure e fare riferimento a quest'ultimo nell'attività. Usare **scriptPath ** per specificare il percorso del file di query Hive e **scriptLinkedService** per specificare la risorsa di archiviazione di Azure contenente il file di script. 
   
   > [!NOTE]
   > È anche possibile fornire lo script Hive inline nella definizione dell'attività tramite la proprietà **script** . Si sconsiglia tuttavia questo approccio perché è necessario eseguire l'escape di tutti i caratteri speciali dello script nel documento JSON, con possibili problemi di debug. Si consiglia di seguire il passaggio 4.
   > 
   > 
5. Creare la pipeline riportata con l'attività HDInsightHive. L'attività elabora/trasforma i dati.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Distribuire la pipeline. Per informazioni dettagliate, vedere l'argomento relativo alla [creazione di pipeline](data-factory-create-pipelines.md) . 
7. Monitorare la pipeline mediante le viste di monitoraggio e gestione delle pipeline di Data factory. Per informazioni dettagliate, vedere [Monitorare e gestire le pipeline di Data factory di Azure](data-factory-monitor-manage-pipelines.md) . 

## <a name="specifying-parameters-for-a-hive-script"></a>Specifica dei parametri per uno script Hive
In questo esempio i log di giochi vengono inseriti giornalmente nel sistema di archiviazione BLOB di Azure e memorizzati in una cartella partizionata con data e ora. Si desidera impostare i parametri per lo script Hive e passare il percorso della cartella di input in modo dinamico durante il runtime, generando l'output partizionato con data e ora.

Per usare lo script con parametri Hive, eseguire le operazioni seguenti:

* Definire i parametri in **defines**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* Nello script Hive fare riferimento al parametro tramite **${hiveconf:parameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
## <a name="see-also"></a>Vedere anche
* [Attività di Pig](data-factory-pig-activity.md)
* [Attività MapReduce](data-factory-map-reduce.md)
* [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
* [Chiamare i programmi Spark](data-factory-spark.md)
* [Chiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


