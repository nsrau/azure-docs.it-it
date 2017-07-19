---
title: "Trasformare dati usando l&quot;attività Pig in Azure Data Factory | Documentazione Microsoft"
description: "Informazioni su come usare l&quot;attività Pig in una data factory di Azure per eseguire query Pig in un cluster HDInsight su richiesta o nel proprio cluster HDInsight."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 182a637ab98955129d269e2afc3ba581aa1a7c03
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017


---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Trasformare dati usando l'attività Pig in Azure Data Factory
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

L'attività Pig di HDInsight in una [pipeline](data-factory-create-pipelines.md) di Data factory esegue query Pig sul cluster HDInsight [dell'utente](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o sul cluster HDInsight [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) basato su Windows o Linux. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

> [!NOTE] 
> Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo leggere [Introduzione ad Azure Data Factory](data-factory-introduction.md) ed eseguire l'esercitazione [Creare la prima pipeline di dati](data-factory-build-your-first-pipeline.md) . 

## <a name="syntax"></a>Sintassi

```JSON
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
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
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```
## <a name="syntax-details"></a>Dettagli sintassi
| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| name |Nome dell'attività |Sì |
| Descrizione |Testo descrittivo per lo scopo dell'attività |No |
| type |HDInsightPig |Sì |
| inputs |Uno o più input usati dall'attività Pig |No |
| outputs |Uno o più input prodotti dall'attività Pig |Sì |
| linkedServiceName |Riferimento al cluster HDInsight registrato come servizio collegato in Data factory |Sì |
| script |Specificare lo script Pig inline |No |
| script path |Archiviare lo script Pig in un archivio BLOB di Azure e immettere il percorso del file. Usare la proprietà "script" o "scriptPath". Non è possibile usare entrambe le proprietà. Il nome del file distingue tra maiuscole e minuscole. |No |
| defines |Specificare i parametri come coppie chiave/valore per fare riferimento ad essi nello script Pig |No |

## <a name="example"></a>Esempio
Si prenda ad esempio l'analisi di log di giochi, in cui si desidera verificare il tempo che i giocatori hanno dedicato a giocare alle partite avviate dalla società.

Il log del gioco di esempio seguente è un file separato da virgole (,). Il file contiene i campi ProfileID, SessionStart, Duration, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

Lo **script Pig** per elaborare tali dati sarà:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Per eseguire lo script Pig in una pipeline di Data Factory, eseguire la procedura seguente:

1. Creare un servizio collegato per registrare [il proprio cluster di elaborazione di HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) oppure configurare [un cluster di elaborazione di HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). In questo esempio il servizio collegato è denominato **HDInsightLinkedService**.
2. Creare un [servizio collegato](data-factory-azure-blob-connector.md) per configurare la connessione all'archivio BLOB di Azure che ospita i dati. In questo esempio il servizio collegato è denominato **StorageLinkedService**.
3. Creare [set di dati](data-factory-create-datasets.md) che puntano ai dati di input e output. In questo esempio il set di dati di input è denominato **PigSampleIn**, mentre il set di dati di output è denominato **PigSampleOut**.
4. Copiare la query Pig in un file di archiviazione BLOB di Azure configurato nel passaggio 2. Se l'archiviazione di Azure che ospita i dati è diversa da quella che ospita il file di query, creare un servizio collegato ad archiviazione di Azure separato. Fare riferimento al servizio collegato nella configurazione dell'attività. Usare **scriptPath ** per specificare il percorso del file di script Pig e **scriptLinkedService**. 
   
   > [!NOTE]
   > È anche possibile fornire lo script Pig inline nella definizione dell'attività tramite la proprietà **script** . Tuttavia, si sconsiglia questo approccio poiché è necessario eseguire l'escape di tutti i caratteri speciali dello script, con possibili problemi di debug. Si consiglia di seguire il passaggio 4.
   > 
   > 
5. Creare la pipeline riportata con l'attività HDInsightPig. L'attività elabora i dati di input eseguendo script Pig nel cluster HDInsight.

    ```JSON   
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
        ]
      }
    } 
    ```
6. Distribuire la pipeline. Per informazioni dettagliate, vedere l'argomento relativo alla [creazione di pipeline](data-factory-create-pipelines.md) . 
7. Monitorare la pipeline mediante le viste di monitoraggio e gestione delle pipeline di Data factory. Per informazioni dettagliate, vedere [Monitorare e gestire le pipeline di Data factory di Azure](data-factory-monitor-manage-pipelines.md) .

## <a name="specifying-parameters-for-a-pig-script"></a>Specifica dei parametri per uno script Pig
Si consideri l'esempio seguente: i log di giochi vengono inseriti giornalmente nel sistema di archiviazione BLOB di Azure e memorizzati in una cartella partizionata secondo data e ora. Si desidera impostare i parametri per lo script Pig e passare il percorso della cartella di input in modo dinamico durante il runtime, generando l'output partizionato con data e ora.

Per impostare i parametri per lo script Pig, seguire questa procedura:

* Definire i parametri in **defines**.

    ```JSON  
    {
        "name": "PigActivitySamplePipeline",
          "properties": {
        "activities": [
            {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                      {
                        "name": "PigSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "PigSampleOut"
                      }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      }
                },
                   "scheduler": {
                      "frequency": "Day",
                      "interval": 1
                }
              }
        ]
      }
    }
    ```  
* Nello Script Pig, fare riferimento ai parametri mediante '**$parameterName**' come illustrato nell'esempio seguente:

    ```  
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);    
    GroupProfile = Group PigSampleIn all;        
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);        
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```
## <a name="see-also"></a>Vedere anche
* [Attività Hive](data-factory-hive-activity.md)
* [Attività MapReduce](data-factory-map-reduce.md)
* [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
* [Chiamare i programmi Spark](data-factory-spark.md)
* [Chiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


