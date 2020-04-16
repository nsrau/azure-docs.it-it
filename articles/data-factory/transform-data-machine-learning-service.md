---
title: Eseguire pipeline di Azure Machine Learning
description: Informazioni su come eseguire le pipeline di Azure Machine Learning nelle pipeline di Azure Data Factory.Learn how to run your Azure Machine Learning pipelines in your Azure Data Factory pipelines.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419001"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Eseguire pipeline di Azure Machine Learning in Azure Data FactoryExecute Azure Machine Learning pipelines in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Eseguire le pipeline di Azure Machine Learning come passaggio nelle pipeline di Azure Data Factory.Run your Azure Machine Learning pipelines as a step in your Azure Data Factory pipelines. L'attività Pipeline di esecuzione di Machine Learning consente scenari di stima batch, ad esempio l'identificazione di possibili valori predefiniti del prestito, la determinazione del sentiment e l'analisi dei modelli di comportamento dei clienti.

Il video seguente presenta un'introduzione di sei minuti e una dimostrazione di questa funzionalità.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Sintassi

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività nella pipeline | string | Sì
type | Il tipo di attività è 'AzureMLExecutePipeline'Type of activity is 'AzureMLExecutePipeline' | string | Sì
linkedServiceName | Servizio collegato ad Azure Machine Learning | Riferimento del servizio collegato | Sì
mlPipelineId (informazioni in due) | ID della pipeline di Azure Machine Learning pubblicata | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì
nome esperimento | Nome dell'esperimento della cronologia delle corse della pipeline di Machine LearningRun history experiment name of the Machine Learning pipeline run | Stringa (o un'espressione con l'elemento resultType della stringa) | No
mlPipelineParameters | Coppie chiave/valore da passare all'endpoint della pipeline di Azure Machine Learning pubblicato. Le chiavi devono corrispondere ai nomi dei parametri della pipeline definiti nella pipeline di Machine Learning pubblicataKeys must match the names of pipeline parameters defined in the published Machine Learning pipeline | Oggetto con coppie di valori chiave (o espressione con oggetto resultType) | No
mlParentRunId (informazioni in due) | ID di esecuzione della pipeline di Azure Machine Learning padreThe parent Azure Machine Learning pipeline run ID | Stringa (o un'espressione con l'elemento resultType della stringa) | No
continueOnStepFailure (continuasuStepFailure) | Se continuare l'esecuzione di altri passaggi nella pipeline di Machine Learning eseguita se un passaggio non riesce | boolean | No

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati:

* [Attività Esegui flusso di dati](control-flow-execute-data-flow-activity.md)
* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività suina](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Attività della stored procedure](transform-data-using-stored-procedure.md)
