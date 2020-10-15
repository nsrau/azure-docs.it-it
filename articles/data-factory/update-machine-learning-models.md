---
title: Aggiornare i modelli Azure Machine Learning Studio (classico) utilizzando Azure Data Factory
description: Viene descritto come creare pipeline predittive usando Azure Data Factory e Azure Machine Learning Studio (classico)
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 83950c2d3c5439886ff787b69d9da4d0c214de31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092542"
---
# <a name="update-azure-machine-learning-studio-classic-models-by-using-update-resource-activity"></a>Aggiornare i modelli di Azure Machine Learning Studio (classico) usando l'attività Aggiorna risorsa

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo è complementare all'articolo principale sull'integrazione Azure Data Factory-Azure Machine Learning Studio (classica): [creare pipeline predittive usando Azure Machine Learning Studio (classico) e Azure Data Factory](transform-data-using-machine-learning.md). Se ancora non è stato fatto, consultare l'articolo principale prima di leggere questo articolo.

## <a name="overview"></a>Panoramica
Come parte del processo di operatività Azure Machine Learning Studio (classico), il modello viene sottoposto a training e salvato. Lo si userà quindi per creare un servizio Web predicativo. Il servizio Web potrà quindi essere utilizzato in siti Web, dashboard e app per dispositivi mobili.

I modelli creati con Azure Machine Learning Studio (classico) non sono in genere statici. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello. 

La ripetizione del training può avvenire di frequente. Con l'attività di esecuzione di batch e l'attività di aggiornamento delle risorse, è possibile rendere operativo la ripetizione del training del modello di Azure Machine Learning Studio (classico) e l'aggiornamento del servizio Web predittivo usando Data Factory.

L'immagine seguente illustra la relazione tra training e servizi Web predittivi.

![Servizi Web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Attività della risorsa di aggiornamento Azure Machine Learning Studio (classica)

Il frammento di codice JSON seguente definisce un'attività di esecuzione batch Azure Machine Learning Studio (classica).

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Proprietà                      | Descrizione                              | Obbligatoria |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Nome dell'attività nella pipeline     | Sì      |
| description                   | Testo che descrive l'attività.  | No       |
| type                          | Per l'attività di aggiornamento della risorsa Azure Machine Learning Studio (classica), il tipo di attività è  **AzureMLUpdateResource**. | Sì      |
| linkedServiceName             | Servizio collegato Azure Machine Learning Studio (classico) che contiene la proprietà updateResourceEndpoint. | Sì      |
| trainedModelName              | Nome del modulo di modello con training nell'esperimento del servizio Web da aggiornare | Sì      |
| trainedModelLinkedServiceName | Nome del servizio collegato di Archiviazione di Azure che viene caricato dall'operazione di aggiornamento | Sì      |
| trainedModelFilePath          | Il percorso file relativo in trainedModelLinkedService per rappresentare il file con estensione iLearner caricato dall'operazione di aggiornamento | Sì      |

## <a name="end-to-end-workflow"></a>Flusso di lavoro end-to-end

L'intero processo di attivazione della ripetizione del training di un modello e dell'aggiornamento dei servizi Web predittivi prevede la procedura seguente:

- Richiamare il **servizio Web di training ** tramite l'**attività Esecuzione batch**. La chiamata di un servizio Web di training equivale alla chiamata di un servizio Web predittivo descritto in [creare pipeline predittive usando Azure Machine Learning Studio (classico) e data factory attività di esecuzione batch](transform-data-using-machine-learning.md). L'output del servizio Web di training è un file iLearner che è possibile usare per aggiornare il servizio Web predittivo.
- Richiamare l'**endpoint di aggiornamento risorse** del **servizio Web predittivo** tramite l'**attività Aggiorna risorsa** per aggiornare il servizio Web con il nuovo modello con training.

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Servizio collegato Azure Machine Learning Studio (classico)

Per il corretto funzionamento del flusso di lavoro end-to-end indicato in precedenza, è necessario creare due servizi collegati Azure Machine Learning Studio (classico):

1. Un servizio collegato Azure Machine Learning Studio (classico) al servizio Web di training, questo servizio collegato viene usato dall'attività di esecuzione batch in modo analogo a quanto illustrato in [creare pipeline predittive usando Azure Machine Learning Studio (classico) e data factory attività di esecuzione batch](transform-data-using-machine-learning.md). La differenza è che l'output del servizio Web di training è un file iLearner, che viene quindi usato dall'attività Aggiorna risorsa per aggiornare il servizio Web predittivo.
2. Un servizio collegato Azure Machine Learning Studio (classico) all'endpoint della risorsa di aggiornamento del servizio Web predittivo. Questo servizio collegato viene usato dall'attività Aggiorna risorsa per aggiornare il servizio Web predittivo tramite il file iLearner restituito dal passaggio precedente.

Per il secondo servizio collegato di Azure Machine Learning Studio (classico), la configurazione è diversa quando il servizio Web di Azure Machine Learning Studio (classico) è un servizio Web classico o un nuovo servizio Web. Le differenze sono illustrate separatamente nelle sezioni seguenti.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Il servizio Web è un nuovo servizio Web di Azure Resource Manager

Se il servizio Web è il nuovo tipo di servizio Web che espone un endpoint di Azure Resource Manager, non è necessario aggiungere il secondo endpoint **non predefinito**. Il formato del valore **updateResourceEndpoint** nel servizio collegato è il seguente:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Quando si esegue una query sul servizio Web nel [portale dei servizi web Azure Machine Learning Studio (classico)](https://services.azureml.net/), è possibile ottenere i valori per i titolari di posizione nell'URL.

Il nuovo tipo di endpoint di aggiornamento delle risorse richiede l'autenticazione dell'entità servizio. Per usare l'autenticazione dell'entità servizio, registrare un'entità applicativa in Azure Active Directory (Azure AD) e concedere a questa il ruolo **Collaboratore** o **Proprietario** della sottoscrizione o del gruppo di risorse a cui appartiene il servizio Web. Vedere [come creare un'entità servizio e assegnare autorizzazioni per la gestione di risorse di Azure](../active-directory/develop/howto-create-service-principal-portal.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

- ID applicazione
- Chiave applicazione
- ID tenant

Ecco una definizione di esempio del servizio collegato:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Lo scenario seguente fornisce altri dettagli. Include un esempio per la ripetizione del training e l'aggiornamento dei modelli di Azure Machine Learning Studio (classico) da una pipeline di Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-studio-classic-model"></a>Esempio: ripetizione del training e aggiornamento di un modello di Azure Machine Learning Studio (classico)

In questa sezione viene fornita una pipeline di esempio che utilizza l' **attività di esecuzione Batch Azure Machine Learning Studio (classica)** per ripetere il training di un modello. La pipeline usa anche l' **attività di aggiornamento della risorsa di Azure Machine Learning Studio (classica)** per aggiornare il modello nel servizio Web di assegnazione dei punteggi. La sezione include anche frammenti di codice JSON per tutti i servizi collegati, i set di dati e la pipeline usati nell'esempio.

### <a name="azure-blob-storage-linked-service"></a>Servizio collegato di archiviazione BLOB di Azure:
Archiviazione di Azure include i dati seguenti:

* Dati di training. Dati di input per il servizio Web di training Azure Machine Learning Studio (classico).
* File iLearner. Output del servizio Web di training Azure Machine Learning Studio (classico). È anche l'input per Attività della risorsa di aggiornamento.

Ecco la definizione JSON di esempio del servizio collegato:

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="linked-service-for-azure-machine-learning-studio-classic-training-endpoint"></a>Servizio collegato per l'endpoint di training Azure Machine Learning Studio (classico)
Il frammento di codice JSON seguente definisce un servizio collegato Azure Machine Learning Studio (classico) che punta all'endpoint predefinito del servizio Web di training.

```JSON
{
    "name": "trainingEndpoint",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
            "apiKey": "myKey"
        }
    }
}
```

In **Azure Machine Learning Studio (classico)**, eseguire le operazioni seguenti per ottenere i valori per **mlEndpoint** e **apiKey**:

1. Fare clic su **SERVIZI WEB** nel menu a sinistra.
2. Fare clic su **servizio Web di training** nell'elenco dei servizi Web.
3. Fare clic su Copia accanto alla casella di testo **Chiave API** . Incollare la chiave dagli Appunti nell'editor JSON di Data Factory.
4. Nel **Azure Machine Learning Studio (classico)**, fare clic sul collegamento **esecuzione batch** .
5. Copiare il valore di **Request URI** (URI della richiesta) dalla sezione **Richiesta** e incollarlo nell'editor JSON di Data Factory.

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Servizio collegato per l'endpoint di assegnazione dei punteggi Azure Machine Learning Studio (classico):
Il frammento di codice JSON seguente definisce un servizio collegato Azure Machine Learning Studio (classico) che punta a un endpoint aggiornabile del servizio Web di assegnazione dei punteggi.

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
La pipeline include due attività: **AzureMLBatchExecution** e **AzureMLUpdateResource**. L'attività Esecuzione batch di Azure ML accetta i dati di training come input e genera un file iLearner come output. L'attività Aggiorna risorsa riceve quindi il file iLearner e lo usa per aggiornare il servizio Web predittivo.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati:

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
