---
title: Aggiornare i modelli di Machine Learning tramite Azure Data Factory
description: Viene descritto come creare pipeline predittive usando Azure Data Factory V1 e Azure Machine Learning Studio (classico)
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: c456c7eb31e1e8e66aa3276a0cb5f6f8b39efa9a
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631751"
---
# <a name="updating-azure-machine-learning-studio-classic-models-using-update-resource-activity"></a>Aggiornamento di modelli di Azure Machine Learning Studio (classico) utilizzando l'attività Aggiorna risorsa

> [!div class="op_single_selector" title1="Attività di trasformazione"]
> * [Attività hive](data-factory-hive-activity.md) 
> * [Attività Pig](data-factory-pig-activity.md)
> * [Attività MapReduce](data-factory-map-reduce.md)
> * [Attività di streaming di Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Attività Spark](data-factory-spark.md)
> * [Attività di esecuzione batch di Azure Machine Learning Studio (versione classica)](data-factory-azure-ml-batch-execution-activity.md)
> * [Attività di aggiornamento risorse di Azure Machine Learning Studio (versione classica)](data-factory-azure-ml-update-resource-activity.md)
> * [Attività stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Data Factory, vedere le informazioni sull'[aggiornamento dei modelli di Machine Learning in Data Factory](../update-machine-learning-models.md).

Questo articolo è complementare all'articolo principale sull'integrazione Azure Data Factory-Azure Machine Learning Studio (classica): [creare pipeline predittive usando Azure Machine Learning Studio (classico) e Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Se ancora non è stato fatto, consultare l'articolo principale prima di leggere questo articolo. 

## <a name="overview"></a>Panoramica
Nel corso del tempo, è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi Azure Machine Learning Studio (classico) usando nuovi set di dati di input. Una volta ripetuto il training, aggiornare il servizio Web di assegnazione dei punteggi con il modello ML di cui è stato ripetuto il training. I passaggi tipici per abilitare la ripetizione del training e l'aggiornamento dei modelli di studio (classico) tramite i servizi Web sono:

1. Creare un esperimento in [Azure Machine Learning Studio (classico)](https://studio.azureml.net).
2. Quando si è soddisfatti del modello, usare Azure Machine Learning Studio (classico) per pubblicare i servizi Web sia per l' **esperimento di training** che per l' **esperimento predittivo** o di valutazione.

La tabella seguente descrive i servizi Web usati in questo esempio.  Per informazioni dettagliate, vedere ripetere il [training dei modelli di Azure Machine Learning Studio (classico) a livello di codice](../../machine-learning/classic/retrain-machine-learning-model.md) .

- **Servizio Web di training** : riceve dati di training e produce modelli sottoposti a training. L'output della ripetizione del training è un file con estensione ilearner in un archivio BLOB di Azure. L' **endpoint predefinito** viene creato automaticamente quando si pubblica l'esperimento di training come servizio Web. È possibile creare altri endpoint ma l'esempio usa solo l'endpoint predefinito.
- **Servizio Web di assegnazione dei punteggi** : riceve esempi di dati non etichettati ed esegue previsioni. L'output della stima potrebbe avere diverse forme, ad esempio un file CSV o righe nel database SQL di Azure, a seconda della configurazione dell'esperimento. L'endpoint predefinito viene creato automaticamente quando si pubblica l'esperimento predittivo come servizio Web. 

Nell'immagine seguente viene illustrata la relazione tra gli endpoint di training e di assegnazione dei punteggi in Azure Machine Learning Studio (classico).

![Servizi Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

È possibile richiamare il **servizio Web di training** utilizzando l' **attività di esecuzione batch Azure Machine Learning Studio (classica)** . La chiamata di un servizio Web di training equivale alla chiamata di un servizio Web di Azure Machine Learning Studio (classico) (assegnazione di punteggi al servizio Web) per i dati di assegnazione dei punteggi. Le sezioni precedenti illustrano come richiamare in dettaglio un servizio Web di Azure Machine Learning Studio (classico) da una pipeline di Azure Data Factory. 

È possibile richiamare il **servizio Web** di assegnazione dei punteggi utilizzando l' **attività della risorsa di aggiornamento Azure Machine Learning Studio (classica)** per aggiornare il servizio Web con il nuovo modello sottoposto a training. Gli esempi seguenti forniscono definizioni dei servizi collegati: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Il servizio Web di assegnazione dei punteggi è un servizio Web classico
Se il servizio Web di assegnazione dei punteggi è un **servizio Web classico** , creare il secondo **endpoint non predefinito e aggiornabile** usando il portale di Azure. Per la procedura, vedere l'articolo [Creare endpoint](../../machine-learning/classic/create-endpoint.md) . Dopo aver creato l'endpoint aggiornabile non predefinito, seguire questa procedura:

* Fare clic su **ESECUZIONE BATCH** per ottenere il valore dell'URI per la proprietà JSON **mlEndpoint** .
* Fare clic su **AGGIORNA RISORSA** per ottenere il valore dell'URI per la proprietà JSON **updateResourceEndpoint** . La chiave API si trova nell'angolo in basso a destra della pagina dell'endpoint.

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
Se il servizio Web è il nuovo tipo di servizio Web che espone un endpoint di Azure Resource Manager, non è necessario aggiungere il secondo endpoint **non predefinito** . Il formato del valore **updateResourceEndpoint** nel servizio collegato è il seguente: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Quando si esegue una query sul servizio Web nel [portale dei servizi web Azure Machine Learning Studio (classico)](https://services.azureml.net/), è possibile ottenere i valori per i titolari di posizione nell'URL. Il nuovo tipo di endpoint di risorse di aggiornamento richiede un token AAD (Azure Active Directory). Specificare **servicePrincipalId** e **servicePrincipalKey** nel servizio collegato Studio (classico). Vedere [Come creare un'entità servizio e assegnare autorizzazioni per gestire una risorsa di Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Ecco una definizione di esempio del servizio collegato AzureML: 

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

Lo scenario seguente fornisce altri dettagli. Include un esempio per la ripetizione del training e l'aggiornamento dei modelli studio (classico) da una pipeline Azure Data Factory.

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>Scenario: ripetizione del training e aggiornamento di un modello Studio (classico)
In questa sezione viene fornita una pipeline di esempio che utilizza l' **attività di esecuzione Batch Azure Machine Learning Studio (classica)** per ripetere il training di un modello. La pipeline usa anche l' **attività di aggiornamento della risorsa di Azure Machine Learning Studio (classica)** per aggiornare il modello nel servizio Web di assegnazione dei punteggi. La sezione include anche frammenti di codice JSON per tutti i servizi collegati, i set di dati e la pipeline usati nell'esempio.

Ecco la vista diagramma della pipeline di esempio. Come si può notare, l'attività di esecuzione batch di studio (classica) accetta l'input di training e produce un output di training (file iLearner). L'attività di aggiornamento della risorsa di studio (classica) accetta questo output di training e aggiorna il modello nell'endpoint del servizio Web di assegnazione dei punteggi. Attività della risorsa di aggiornamento non produce un output. placeholderBlob è solo un set di dati di output fittizio richiesto dal servizio Data factory di Azure per eseguire la pipeline.

![Diagramma della pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Servizio collegato di archiviazione BLOB di Azure:
Archiviazione di Azure include i dati seguenti:

* Dati di training. Dati di input per il servizio Web di training di studio (classico).  
* File iLearner. Output del servizio Web di training di studio (classico). È anche l'input per Attività della risorsa di aggiornamento.  

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
Il set di dati seguente rappresenta i dati di training di input per il servizio Web di training di studio (classico). L'attività di esecuzione batch di studio (classica) accetta questo set di dati come input.

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
Il set di dati seguente rappresenta il file iLearner di output del servizio Web di training Azure Machine Learning Studio (classico). L'attività di esecuzione batch Azure Machine Learning Studio (classica) genera questo set di dati. Questo set di dati è anche l'input per l'attività di aggiornamento della risorsa di Azure Machine Learning Studio (classica).

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

### <a name="linked-service-for-studio-classic-training-endpoint"></a>Servizio collegato per l'endpoint di training di studio (classico)
Il frammento di codice JSON seguente definisce un servizio collegato di studio (classico) che punta all'endpoint predefinito del servizio Web di training.

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

In **Azure Machine Learning Studio (classico)** , eseguire le operazioni seguenti per ottenere i valori per **mlEndpoint** e **apiKey** :

1. Fare clic su **SERVIZI WEB** nel menu a sinistra.
2. Fare clic su **servizio Web di training** nell'elenco dei servizi Web.
3. Fare clic su Copia accanto alla casella di testo **Chiave API** . Incollare la chiave dagli Appunti nell'editor JSON di Data Factory.
4. Nel **Azure Machine Learning Studio (classico)** , fare clic sul collegamento **esecuzione batch** .
5. Copiare il valore di **Request URI** (URI della richiesta) dalla sezione **Richiesta** e incollarlo nell'editor JSON di Data Factory.   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>Servizio collegato per l'endpoint di Punteggio aggiornabile di studio (classico):
Il frammento di codice JSON seguente definisce un servizio collegato di studio (classico) che punta all'endpoint aggiornabile non predefinito del servizio Web di assegnazione dei punteggi.  

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
L'attività di aggiornamento delle risorse di studio (classico) non genera alcun output. Tuttavia, Azure Data Factory richiede un set di dati di output per gestire la pianificazione di una pipeline, quindi in questo esempio viene usato un set di dati segnaposto fittizio.  

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
La pipeline include due attività: **AzureMLBatchExecution** e **AzureMLUpdateResource** . L'attività di esecuzione batch Azure Machine Learning Studio (classica) accetta i dati di training come input e produce un file iLearner come output. L'attività richiama il servizio Web di training, l'esperimento di training esposto come servizio Web, con i dati di training di input e riceve il file iLearner dal servizio Web. placeholderBlob è solo un set di dati di output fittizio richiesto dal servizio Data factory di Azure per eseguire la pipeline.

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