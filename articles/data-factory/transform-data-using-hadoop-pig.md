---
title: "Trasformare dati tramite l'attività Hadoop Pig in Azure Data Factory | Microsoft Docs"
description: "Informazioni su come usare l'attività Pig in una data factory di Azure per eseguire query Pig in un cluster HDInsight su richiesta o nel proprio cluster HDInsight."
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7745f273283ff998b41fe59a10e705e7564a7bd3
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Trasformare dati tramite l'attività Hadoop Pig in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-pig-activity.md)
> * [Versione 2 - Anteprima](transform-data-using-hadoop-pig.md)

L'attività Hadoop Pig in una [pipeline](concepts-pipelines-activities.md) di Data Factory esegue query Pig nel cluster HDInsight [personale](compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività Pig nella versione 1](v1/data-factory-pig-activity.md).

Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo leggere l'[introduzione ad Azure Data Factory](introduction.md) ed eseguire [Tutorial: transform data](tutorial-transform-data-spark-powershell.md) (Esercitazione: Trasformare i dati). 

## <a name="syntax"></a>Sintassi

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
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

| Proprietà            | Descrizione                              | Obbligatorio |
| ------------------- | ---------------------------------------- | -------- |
| name                | Nome dell'attività                     | Sì      |
| Descrizione         | Testo descrittivo per lo scopo dell'attività | No       |
| type                | Per l'attività Hive, il tipo di attività è HDinsightPig | Sì      |
| linkedServiceName   | Riferimento al cluster HDInsight registrato come servizio collegato in Data Factory. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | Sì      |
| scriptLinkedService | Riferimento a un servizio collegato di Archiviazione di Azure usato per archiviare lo script Pig da eseguire. Se non si specifica questo servizio collegato, viene usato il servizio collegato di Archiviazione di Azure definito nel servizio collegato HDInsight. | No       |
| scriptPath          | Specificare il percorso del file di script archiviato nel servizio Archiviazione di Azure indicato da scriptLinkedService. Il nome del file distingue tra maiuscole e minuscole. | No       |
| getDebugInfo        | Specifica quando i file di log vengono copiati nel servizio Archiviazione di Azure usato dal cluster HDInsight o specificato da scriptLinkedService. Valori consentiti: None, Always o Failure. Valore predefinito: None. | No       |
| arguments           | Specifica una matrice di argomenti per un processo Hadoop. Gli argomenti vengono passati a ogni attività come argomenti della riga di comando. | No       |
| defines             | Specificare i parametri come coppie chiave/valore per i riferimenti all'interno dello script Pig. | No       |

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati: 

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)

