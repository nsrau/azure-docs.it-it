---
title: Aggiornare i modelli di Machine Learning con Azure Data Factory | Microsoft Docs
description: Illustra come creare pipeline predittive tramite Azure Data Factory e Machine Learning
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: d4f7df5ceadf8c6219901af6e516453a88aeece7
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Aggiornare i modelli di Azure Machine Learning tramite l'attività Aggiorna risorsa
Questo articolo integra la versione principale di Azure Data Factory: articolo di integrazione di Azure Machine Learning: [Creare pipeline predittive tramite Azure Machine Learning e Azure Data Factory](transform-data-using-machine-learning.md). Se ancora non è stato fatto, consultare l'articolo principale prima di leggere questo articolo. 

## <a name="overview"></a>Panoramica
Nell'ambito del processo di messa in funzione dei modelli di Azure Machine Learning, viene eseguito il training del modello, che viene quindi salvato. Lo si userà quindi per creare un servizio Web predicativo. Il servizio Web potrà quindi essere utilizzato in siti Web, dashboard e app per dispositivi mobili.

I modelli creati con Machine Learning in genere non sono statici. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello. Per informazioni dettagliate su come ripetere il training di un modello in Azure Machine Learning, fare riferimento a [Ripetere il training di un modello di Machine Learning](../machine-learning/machine-learning-retrain-machine-learning-model.md). 

La ripetizione del training può avvenire di frequente. Con le attività Esecuzione batch e Aggiorna risorsa è possibile rendere operativo il modello di Azure Machine Learning ripetendo il training del servizio Web predittivo e aggiornando tale servizio tramite Data Factory. 

L'immagine seguente illustra la relazione tra training e servizi Web predittivi. 

![SERVIZI WEB](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Attività Aggiorna risorsa di Azure Machine Learning 

Il frammento JSON seguente definisce un'attività Esecuzione batch di Azure Machine Learning.

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




| Proprietà                      | DESCRIZIONE                              | Obbligatoria |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Nome dell'attività nella pipeline     | Sì      |
| description                   | Testo che descrive l'attività.  | No        |
| type                          | Per l'attività Aggiorna risorsa di Azure Machine Learning il tipo corrisponde ad **AzureMLUpdateResource**. | Sì      |
| linkedServiceName             | Servizio collegato di Azure Machine Learning che contiene la proprietà updateResourceEndpoint. | Sì      |
| trainedModelName              | Nome del modulo di modello con training nell'esperimento del servizio Web da aggiornare | Sì      |
| trainedModelLinkedServiceName | Nome del servizio collegato di Archiviazione di Azure che viene caricato dall'operazione di aggiornamento | Sì      |
| trainedModelFilePath          | Il percorso file relativo in trainedModelLinkedService per rappresentare il file con estensione iLearner caricato dall'operazione di aggiornamento | Sì      |


## <a name="end-to-end-workflow"></a>Flusso di lavoro end-to-end

L'intero processo di attivazione della ripetizione del training di un modello e dell'aggiornamento dei servizi Web predittivi prevede la procedura seguente: 

- Richiamare il **servizio Web di training**  tramite l'**attività Esecuzione batch**. Richiamare un servizio Web di training equivale a richiamare un servizio Web predittivo descritto in [Create predictive pipelines using Azure Machine Learning and Data Factory Batch Execution activity](transform-data-using-machine-learning.md) (Creare pipeline predittive tramite Azure Machine Learning e l'attività Esecuzione Batch di Data Factory). L'output del servizio Web di training è un file iLearner che è possibile usare per aggiornare il servizio Web predittivo. 
- Richiamare l'**endpoint di aggiornamento risorse** del **servizio Web predittivo** tramite l'**attività Aggiorna risorsa** per aggiornare il servizio Web con il nuovo modello con training. 

## <a name="azure-machine-learning-linked-service"></a>Servizio collegato di Azure Machine Learning

Perché il flusso di lavoro end-to-end citato in precedenza funzioni, è necessario creare due servizi collegati di Azure Machine Learning: 

1. Uno per il servizio Web di training. Questo servizio collegato viene usato dall'attività Esecuzione batch in modo analogo a quello descritto in [Create predictive pipelines using Azure Machine Learning and Data Factory Batch Execution activity](transform-data-using-machine-learning.md) (Creare pipeline predittive tramite Azure Machine Learning e l'attività Esecuzione Batch di Data Factory). La differenza consiste nel fatto che l'output del servizio Web di training è un file iLearner che viene quindi usato dall'attività Aggiorna risorsa per aggiornare il servizio Web predittivo. 
2. Uno per l'endpoint di aggiornamento delle risorse del servizio Web predittivo. Questo servizio collegato viene usato dall'attività Aggiorna risorsa per aggiornare il servizio Web predittivo tramite il file iLearner restituito dal passaggio precedente. 

Per il secondo servizio collegato di Azure Machine Learning la configurazione è diversa a seconda che il servizio Web di Azure Machine Learning in uso sia un servizio Web classico o nuovo. Le differenze sono illustrate separatamente nelle sezioni seguenti. 

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Il servizio Web è un nuovo servizio Web di Azure Resource Manager 

Se il servizio Web è il nuovo tipo di servizio Web che espone un endpoint di Azure Resource Manager, non è necessario aggiungere il secondo endpoint **non predefinito**. Il formato del valore **updateResourceEndpoint** nel servizio collegato è il seguente: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

È possibile ottenere i valori per i segnaposti nell'URL quando si eseguono query nel servizio Web nel portale di [Azure Machine Learning Web Services](https://services.azureml.net/) (Servizi Web Microsoft Azure Machine Learning). 

Il nuovo tipo di endpoint di aggiornamento delle risorse richiede l'autenticazione dell'entità servizio. Per usare l'autenticazione dell'entità servizio, registrare un'entità applicativa in Azure Active Directory (Azure AD) e concedere a questa il ruolo **Collaboratore** o **Proprietario** della sottoscrizione o del gruppo di risorse a cui appartiene il servizio Web. Vedere [come creare un'entità servizio e assegnare autorizzazioni per la gestione di risorse di Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

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
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. ",
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

Lo scenario seguente fornisce altri dettagli. Include un esempio per la ripetizione del training e l'aggiornamento dei modelli di Azure ML da una pipeline di Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Esempio: Ripetere il training di un modello di Azure Machine Learning e aggiornare il modello

Questa sezione include una pipeline di esempio che usa **Attività di esecuzione batch di Azure ML** per ripetere il training di un modello. La pipeline usa anche **Attività della risorsa di aggiornamento di Azure ML** per aggiornare il modello nel servizio Web di assegnazione dei punteggi. La sezione include anche frammenti di codice JSON per tutti i servizi collegati, i set di dati e la pipeline usati nell'esempio.

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
Il frammento di codice JSON seguente definisce un servizio collegato di Azure Machine Learning che punta all'endpoint aggiornabile del servizio Web di assegnazione dei punteggi.  

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
