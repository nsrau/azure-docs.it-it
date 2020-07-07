---
title: Pipeline di esecuzione Azure Machine Learning
description: Informazioni su come eseguire le pipeline di Azure Machine Learning nelle pipeline di Azure Data Factory.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81419001"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Eseguire pipeline di Azure Machine Learning in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Eseguire le pipeline di Azure Machine Learning come passaggio nelle pipeline di Azure Data Factory. L'attività Esegui pipeline Machine Learning consente scenari di stima in batch, ad esempio l'identificazione di possibili valori predefiniti di prestito, la determinazione del sentimento e l'analisi dei modelli di comportamento dei clienti.

Il video seguente offre un'introduzione a sei minuti e una dimostrazione di questa funzionalità.

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
type | Il tipo di attività è' AzureMLExecutePipeline ' | string | Sì
linkedServiceName | Servizio collegato da Azure Machine Learning | Riferimento del servizio collegato | Sì
mlPipelineId | ID della pipeline Azure Machine Learning pubblicata | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì
experimentname | Nome dell'esperimento di cronologia di esecuzione del Machine Learning esecuzione della pipeline | Stringa (o un'espressione con l'elemento resultType della stringa) | No
mlPipelineParameters | Coppie chiave-valore da passare all'endpoint della pipeline di Azure Machine Learning pubblicati. Le chiavi devono corrispondere ai nomi dei parametri della pipeline definiti nella pipeline Machine Learning pubblicata | Oggetto con coppie chiave-valore (o espressione con oggetto resultType) | No
mlParentRunId | ID esecuzione pipeline Azure Machine Learning padre | Stringa (o un'espressione con l'elemento resultType della stringa) | No
continueOnStepFailure | Indica se continuare l'esecuzione di altri passaggi nell'Machine Learning esecuzione della pipeline in caso di errore di un passaggio | boolean | No

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati:

* [Attività Esegui flusso di dati](control-flow-execute-data-flow-activity.md)
* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
