---
title: Aggiornare i modelli di Machine Learning con Azure Data Factory | Documentazione Microsoft
description: Illustra come creare pipeline predittive usando Data factory di Azure e Azure Machine Learning
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: ffe3e6c5f61f2debee70683990cf26ce094dc64b
ms.openlocfilehash: c06b8d6010ca6ddf562df99dcb7bf61ad9648f07
ms.lasthandoff: 02/17/2017


---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Aggiornamento dei modelli di Azure Machine Learning con Attività della risorsa di aggiornamento
Questo articolo integra la versione principale di Azure Data Factory: articolo di integrazione di Azure Machine Learning: [Creare pipeline predittive tramite Azure Machine Learning e Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Se ancora non è stato fatto, consultare l'articolo principale prima di leggere questo articolo. 

## <a name="overview"></a>Panoramica
Nel corso del tempo è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi di Azure ML usando nuovi set di dati di input. Una volta ripetuto il training, aggiornare il servizio Web di assegnazione dei punteggi con il modello ML di cui è stato ripetuto il training. Questa è la procedura tipica per abilitare la ripetizione del training e l'aggiornamento dei modelli di Azure ML tramite i servizi Web:

1. Creare un esperimento in [Azure ML Studio](https://studio.azureml.net).
2. Quando si è soddisfatti del modello, usare Azure ML Studio per pubblicare i servizi Web sia per l'**esperimento di training** che per l'**esperimento predittivo** o di assegnazione dei punteggi.

La tabella seguente descrive i servizi Web usati in questo esempio.  Per altre informazioni, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](../machine-learning/machine-learning-retrain-models-programmatically.md) .

- **Servizio Web di training**: riceve dati di training e produce modelli sottoposti a training. L'output della ripetizione del training è un file con estensione ilearner in un archivio BLOB di Azure. L' **endpoint predefinito** viene creato automaticamente quando si pubblica l'esperimento di training come servizio Web. È possibile creare altri endpoint ma l'esempio usa solo l'endpoint predefinito.
- **Servizio Web di assegnazione dei punteggi**: riceve esempi di dati non etichettati ed esegue previsioni. L'output della stima può avere diversi formati, ad esempio un file con estensione csv o righe in un database SQL di Azure, a seconda della configurazione dell'esperimento. L'endpoint predefinito viene creato automaticamente quando si pubblica l'esperimento predittivo come servizio Web. 

L'immagine seguente illustra la relazione tra gli endpoint di training e di assegnazione dei punteggi in Azure ML.

![SERVIZI WEB](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

È possibile richiamare il **training web service** tramite il **Attività di esecuzione batch di Azure ML**. Richiamare il servizio Web di training è la stessa operazione che si esegue per richiamare un servizio Web di Azure ML, il servizio Web di assegnazione dei punteggi, per la valutazione dei dati. Le sezioni precedenti descrivono in dettaglio come richiamare un servizio Web di Azure ML da una pipeline di Azure Data Factory. 

È possibile richiamare il **scoring web service** tramite il **Attività della risorsa di aggiornamento di Azure ML** per aggiornare il servizio Web con il nuovo modello sottoposto a training. Gli esempi seguenti forniscono definizioni dei servizi collegati: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Il servizio Web di assegnazione dei punteggi è un servizio Web classico
Se il servizio Web di assegnazione dei punteggi è un **servizio Web classico**, creare il secondo **endpoint non predefinito e aggiornabile** usando il [portale di Azure](https://manage.windowsazure.com). Per la procedura, vedere l'articolo [Creare endpoint](../machine-learning/machine-learning-create-endpoint.md) . Dopo aver creato l'endpoint aggiornabile non predefinito, seguire questa procedura:

* Fare clic su **ESECUZIONE BATCH** per ottenere il valore dell'URI per la proprietà JSON **mlEndpoint**.
* Fare clic su **AGGIORNA RISORSA** per ottenere il valore dell'URI per la proprietà JSON **updateResourceEndpoint**. La chiave API si trova nell'angolo in basso a destra della pagina dell'endpoint.

![Endpoint aggiornabile](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

L'esempio seguente fornisce una definizione JSON di esempio per il servizio collegato AzureML. Il servizio collegato usa il valore apiKey per l'autenticazione.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Il servizio di assegnazione dei punteggi è un servizio Web di Azure Resource Manager 
Se il servizio Web è il nuovo tipo di servizio Web che espone un endpoint di Azure Resource Manager, non è necessario aggiungere il secondo endpoint **non predefinito**. Il formato del valore **updateResourceEndpoint** nel servizio collegato è il seguente: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

È possibile ottenere i valori per i segnaposti nell'URL quando si eseguono query nel servizio Web nel portale di [Azure Machine Learning Web Services](https://services.azureml.net/) (Servizi Web Microsoft Azure Machine Learning). Il nuovo tipo di endpoint di risorse di aggiornamento richiede un token AAD (Azure Active Directory). Specificare **servicePrincipalId** e **servicePrincipalKey** nel servizio collegato AzureML. Vedere [Come creare un'entità servizio e assegnare autorizzazioni per gestire una risorsa di Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md). Ecco una definizione di esempio del servizio collegato AzureML: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Lo scenario seguente fornisce altri dettagli. Include un esempio per la ripetizione del training e l'aggiornamento dei modelli di Azure ML da una pipeline di Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenario: ripetizione del training e aggiornamento di un modello di Azure ML
Questa sezione include una pipeline di esempio che usa **Attività di esecuzione batch di Azure ML** per ripetere il training di un modello. La pipeline usa anche **Attività della risorsa di aggiornamento di Azure ML** per aggiornare il modello nel servizio Web di assegnazione dei punteggi. La sezione include anche frammenti di codice JSON per tutti i servizi collegati, i set di dati e la pipeline usati nell'esempio.

Ecco la vista diagramma della pipeline di esempio. Come si può vedere, Attività di esecuzione batch di Azure ML accetta l'input di training e genera un output di training (file iLearner). Attività della risorsa di aggiornamento di Azure ML accetta questo output di training e aggiorna il modello nell'endpoint di servizio Web di assegnazione dei punteggi. Attività della risorsa di aggiornamento non produce un output. placeholderBlob è solo un set di dati di output fittizio richiesto dal servizio Data factory di Azure per eseguire la pipeline.

![Diagramma della pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Servizio collegato di archiviazione BLOB di Azure:
Archiviazione di Azure include i dati seguenti:

* Dati di training. Dati di input per il servizio Web di training di Azure ML.  
* File iLearner. Output del servizio Web di training di Azure ML. È anche l'input per Attività della risorsa di aggiornamento.  

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

### <a name="training-input-dataset"></a>Set di dati di input di training:
Il set di dati seguente rappresenta i dati di training di input per il servizio Web di training di Azure ML. Attività di esecuzione batch di Azure ML accetta questo set di dati come input.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Set di dati di output di training:
Il set di dati seguente rappresenta il file iLearner di output del servizio Web di training di Azure ML. Attività di esecuzione batch di Azure ML genera questo set di dati. Questo set di dati è anche l'input per Attività della risorsa di aggiornamento.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Servizio collegato per l'endpoint di training di Azure ML
Il frammento di codice JSON seguente definisce un servizio collegato di Azure Machine Learning che punta all'endpoint predefinito del servizio Web di training.

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

In **Azure ML Studio** eseguire queste operazioni per ottenere i valori per **mlEndpoint** e **apiKey**:

1. Fare clic su **SERVIZI WEB** nel menu a sinistra.
2. Fare clic su **servizio Web di training** nell'elenco dei servizi Web.
3. Fare clic su Copia accanto alla casella di testo **Chiave API** . Incollare la chiave dagli Appunti nell'editor JSON di Data Factory.
4. In **Azure ML Studio** fare clic sul collegamento **ESECUZIONE BATCH**.
5. Copiare il valore di **Request URI** (URI della richiesta) dalla sezione **Richiesta** e incollarlo nell'editor JSON di Data Factory.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Servizio collegato per l'endpoint di assegnazione dei punteggi aggiornabile di Azure ML:
Il frammento di codice JSON seguente definisce un servizio collegato di Azure Machine Learning che punta all'endpoint aggiornabile non predefinito del servizio Web di assegnazione dei punteggi.  

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

### <a name="placeholder-output-dataset"></a>Set di dati di output del segnaposto:
Attività della risorsa di aggiornamento di Azure ML non produce un output. Tuttavia, Azure Data Factory richiede un set di dati di output per gestire la pianificazione di una pipeline, quindi in questo esempio viene usato un set di dati segnaposto fittizio.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
La pipeline include due attività: **AzureMLBatchExecution** e **AzureMLUpdateResource**. Attività di esecuzione batch di Azure ML accetta i dati di training come input e genera il file con estensione iLearner come output. L'attività richiama il servizio Web di training, l'esperimento di training esposto come servizio Web, con i dati di training di input e riceve il file iLearner dal servizio Web. placeholderBlob è solo un set di dati di output fittizio richiesto dal servizio Data factory di Azure per eseguire la pipeline.

![Diagramma della pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```

