---
title: "Trasformare i dati usando l'attività di streaming di Hadoop in Azure Data Factory| Microsoft Docs"
description: "Informazioni su come usare l'attività di streaming di Hadoop in Azure Data Factory per trasformare i dati eseguendo i programmi di streaming di Hadoop in un cluster Hadoop."
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
ms.date: 09/19/2017
ms.author: shengc
ms.openlocfilehash: 0452dcaa039c23b9e41f78a43df88f61d13033be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Trasformare i dati usando l'attività di streaming di Hadoop in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - GA](v1/data-factory-hadoop-streaming-activity.md)
> * [Versione 2 - Anteprima](transform-data-using-hadoop-streaming.md)

L'attività di streaming HDInsight in una [pipeline](concepts-pipelines-activities.md) di Data Factory esegue i programmi di streaming di Hadoop nel cluster HDInsight [personalizzato](compute-linked-services.md#azure-hdinsight-linked-service) o [on demand](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale (GA), vedere [Attività di streaming di Hadoop nella versione 1](v1/data-factory-hadoop-streaming-activity.md).

Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo leggere l'[introduzione ad Azure Data Factory](introduction.md) ed eseguire [Tutorial: transform data](tutorial-transform-data-spark-powershell.md) (Esercitazione: Trasformare i dati). 

## <a name="json-sample"></a>Esempio JSON
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Dettagli sintassi

| Proprietà          | Descrizione                              | Obbligatorio |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nome dell'attività                     | Sì      |
| Descrizione       | Testo descrittivo per lo scopo dell'attività | No       |
| type              | Per l'attività di streaming di Hadoop, il tipo di attività è HDInsightStreaming | Sì      |
| linkedServiceName | Riferimento al cluster HDInsight registrato come servizio collegato in Data Factory. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Sì      |
| mapper            | Specifica il nome del mapper eseguibile | Sì      |
| reducer           | Specifica il nome del reducer eseguibile | Sì      |
| combiner          | Specifica il nome del combiner eseguibile | No       |
| fileLinkedService | Riferimento a un servizio collegato Archiviazione di Azure usato per memorizzare i programmi relativi a mapper, combiner e reducer da eseguire. Se non si specifica questo servizio collegato, viene usato il servizio collegato Archiviazione di Azure definito nel servizio collegato HDInsight. | No       |
| filePath          | Specificare una matrice di percorso per i programmi relativi a mapper, combiner e reducer memorizzati in Archiviazione di Azure a cui fa riferimento fileLinkedService. Il percorso fa distinzione tra maiuscole e minuscole. | Sì      |
| input             | Specifica il percorso WASB del file di input per il mapper. | Sì      |
| output            | Specifica il percorso WASB del file di output per il reducer. | Sì      |
| getDebugInfo      | Specifica quando i file di log vengono copiati nell'Archiviazione di Azure usata dal cluster HDInsight (o) indicata da scriptLinkedService. Valori consentiti: None, Always o Failure. Valore predefinito: None. | No       |
| arguments         | Specifica una matrice di argomenti per un processo Hadoop. Gli argomenti vengono passati a ogni attività come argomenti della riga di comando. | No       |
| defines           | Specificare i parametri come coppie chiave/valore per fare riferimento a essi nello script Hive. | No       | 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati: 

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
