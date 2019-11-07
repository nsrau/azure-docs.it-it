---
title: Schema di eventi di griglia di eventi di Azure Machine Learning
description: Descrive le proprietà fornite per area di lavoro di Machine Learning eventi con griglia di eventi di Azure
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 6c2a8fa57bf6e3a552da57588bdbe752ef0d22e2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609567"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Schema di eventi di griglia di eventi di Azure per Azure Machine Learning

Questo articolo fornisce le proprietà e lo schema per gli eventi dell'area di lavoro di machine learning. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

Per un elenco degli script e delle esercitazioni di esempio, vedere [origine evento AzureML](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Tipi di evento disponibili

Azure Machine Learning emette i tipi di evento seguenti:

| Tipo evento | Descrizione |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Generato quando una nuova versione del modello o del modello è stata registrata correttamente. |
| Microsoft. MachineLearningServices. ModelDeployed | Generato quando i modelli sono stati distribuiti correttamente in un endpoint. |
| Microsoft. MachineLearningServices. RunCompleted | Generato quando un'esecuzione è stata completata correttamente. |
| Microsoft. MachineLearningServices. DatasetDriftDetected | Generato quando il monitoraggio della deriva del set di dati rileva la deriva. |

## <a name="the-contents-of-an-event-response"></a>Contenuto di una risposta di evento

Quando viene attivato un evento, il servizio griglia di eventi invia i dati relativi all'evento all'endpoint di sottoscrizione.

Questa sezione contiene un esempio dell'aspetto dei dati per ogni evento.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Evento Microsoft. MachineLearningServices. ModelRegistered

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Evento Microsoft. MachineLearningServices. ModelDeployed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Evento Microsoft. MachineLearningServices. RunCompleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Evento Microsoft. MachineLearningServices. DatasetDriftDetected

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| argomento | stringa | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | stringa | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | stringa | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | stringa | Identificatore univoco dell'evento. |
| data | oggetto | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

L'oggetto dati presenta le proprietà seguenti per ogni tipo di evento:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| ModelName | stringa | Nome del modello registrato. |
| ModelVersion | int | Versione del modello registrato. |
| ModelTags | oggetto | Tag del modello registrato. |
| ModelProperties | oggetto | Proprietà del modello registrato. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. ModelDeployed

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| ServiceName | stringa | Nome del servizio distribuito. |
| ServiceComputeType | stringa | Tipo di calcolo (ad esempio, ACI, AKS) del servizio distribuito. |
| ModelIds | stringa | Elenco comune di ID di modello separati. ID dei modelli distribuiti nel servizio. |
| ServiceTags | oggetto | Tag del servizio distribuito. |
| ServiceProperties | oggetto | Proprietà del servizio distribuito. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| ExperimentId | stringa | ID dell'esperimento a cui appartiene l'esecuzione. |
| experimentname | stringa | Nome dell'esperimento a cui appartiene l'esecuzione. |
| RunId | stringa | ID dell'esecuzione completata. |
| RunType | stringa | Tipo di esecuzione dell'esecuzione completata. |
| RunTags | oggetto | Tag dell'esecuzione completata. |
| RunProperties | oggetto | Proprietà dell'esecuzione completata. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. DatasetDriftDetected

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| DataDriftId | stringa | ID del monitoraggio della deriva dati che ha attivato l'evento. |
| Datadriftname | stringa | Nome del monitoraggio della deriva dati che ha attivato l'evento. |
| RunId | stringa | ID dell'esecuzione che ha rilevato la deriva dei dati. |
| BaseDatasetId | stringa | ID del set di dati di base utilizzato per rilevare la deriva. |
| TargetDatasetId | stringa | ID del set di dati di destinazione utilizzato per rilevare la deriva. |
| DriftCoefficient | double | Risultato coefficiente che ha generato l'evento. |
| StartTime | datetime | Ora di inizio della serie temporale del set di dati di destinazione che ha provocato il rilevamento della deviazione.  |
| EndTime | datetime | Ora di fine della serie temporale del set di dati di destinazione che ha generato il rilevamento della deviazione. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di griglia di eventi di Azure, vedere [schema di sottoscrizione di griglia di eventi](subscription-creation-schema.md)
* Per un'introduzione all'uso di griglia di eventi di Azure con Azure Machine Learning, vedere [utilizzare eventi Azure Machine Learning](/azure/machine-learning/service/concept-event-grid-integration)
* Per un esempio dell'uso di griglia di eventi di Azure con Azure Machine Learning, vedere [creare flussi di lavoro di machine learning basati su eventi](/azure/machine-learning/service/event-schema-machine-learning)