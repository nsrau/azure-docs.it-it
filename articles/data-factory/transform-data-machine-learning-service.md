---
title: Eseguire pipeline di Azure Machine Learning nelle pipeline Azure Data Factory
description: Informazioni su come eseguire le pipeline di Azure Machine Learning nelle pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: a257c7d3f288f9a2e1998bf0679b7146b089cb50
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683881"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Eseguire pipeline di Azure Machine Learning in Azure Data Factory

Eseguire le pipeline di Azure Machine Learning come passaggio nelle pipeline di Azure Data Factory. L'attività Esegui pipeline Machine Learning consente scenari di stima in batch, ad esempio l'identificazione di possibili valori predefiniti di prestito, la determinazione del sentimento e l'analisi dei modelli di comportamento dei clienti.

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

Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | --------
name | Nome dell'attività nella pipeline | String | Sì
type | Il tipo di attività è' AzureMLExecutePipeline ' | String | Sì
linkedServiceName | Servizio collegato da Azure Machine Learning | Riferimento del servizio collegato | Sì
mlPipelineId | ID della pipeline Azure Machine Learning pubblicata | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì
experimentname | Nome dell'esperimento di cronologia di esecuzione del Machine Learning esecuzione della pipeline | Stringa (o un'espressione con l'elemento resultType della stringa) | No
mlPipelineParameters | Coppie chiave-valore da passare all'endpoint della pipeline di Azure Machine Learning pubblicati. Le chiavi devono corrispondere ai nomi dei parametri della pipeline definiti nella pipeline Machine Learning pubblicata | Oggetto con coppie chiave-valore (o espressione con oggetto resultType) | No
mlParentRunId | ID esecuzione pipeline Azure Machine Learning padre | Stringa (o un'espressione con l'elemento resultType della stringa) | No
continueOnStepFailure | Indica se continuare l'esecuzione di altri passaggi nell'Machine Learning esecuzione della pipeline in caso di errore di un passaggio | boolean | No

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti che illustrano come trasformare i dati in altri modi:

* [Attività Esegui flusso di dati](control-flow-execute-data-flow-activity.md)
* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
