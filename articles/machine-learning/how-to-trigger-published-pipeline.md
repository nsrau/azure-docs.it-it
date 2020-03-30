---
title: Attivare l'esecuzione di una pipeline di ML da un'app per la logicaTrigger the run of an ML pipeline from a Logic App
titleSuffix: Azure Machine Learning
description: Informazioni su come attivare l'esecuzione di una pipeline di ML usando le app per la logica di Azure.Learn how to trigger the run of an ML pipeline by using Azure Logic Apps.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122857"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Attivare un'esecuzione di una pipeline di Machine Learning da un'app per la logicaTrigger a run of a Machine Learning pipeline from a Logic App

Attivare l'esecuzione della pipeline di Azure Machine Learning quando vengono visualizzati nuovi dati. Ad esempio, è possibile attivare la pipeline per eseguire il training di un nuovo modello quando vengono visualizzati nuovi dati nell'account di archiviazione BLOB. Configurare il trigger con App per la logica di [Azure.](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area](how-to-manage-workspace.md)di lavoro di Azure Machine Learning.For more information, see Create an Azure Machine Learning workspace .

* Endpoint REST per una pipeline di Machine Learning pubblicata. [Creare e pubblicare la pipeline](how-to-create-your-first-pipeline.md). Individuare quindi l'endpoint REST di PublishedPipeline usando l'ID della pipeline:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-overview.md) per archiviare i dati.
* [Archivio dati](how-to-access-data.md) nell'area di lavoro che contiene i dettagli dell'account di archiviazione BLOB.

## <a name="create-a-logic-app"></a>Creare un'app per la logica

Creare ora un'istanza [dell'app per la logica di Azure.Now](../logic-apps/logic-apps-overview.md) create an Azure Logic App instance. Se lo si desidera, utilizzare un ambiente del servizio di [integrazione (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) e [configurare una chiave gestita dal cliente](../logic-apps/customer-managed-keys-integration-service-environment.md) per l'utilizzo da parte dell'app per la logica.

Dopo aver eseguito il provisioning dell'app per la logica, usare questi passaggi per configurare un trigger per la pipeline:Once your Logic App has been provisioned, use these steps to configure a trigger for your pipeline:

1. [Creare un'identità gestita assegnata dal sistema](../logic-apps/create-managed-service-identity.md) per concedere all'app l'accesso all'area di lavoro di Azure Machine Learning.Create a system-assigned managed identity to give the app access to your Azure Machine Learning Workspace.

1. Passare alla visualizzazione Progettazione app per la logica e selezionare il modello Applicazione per la logica vuota. 
    > [!div class="mx-imgBorder"]
    > ![Modello Vuoto](media/how-to-trigger-published-pipeline/blank-template.png)

1. Nella finestra di progettazione cercare **BLOB**. Selezionare il trigger **Quando un BLOB viene aggiunto o modificato (solo proprietà)** e aggiungere il trigger all'app per la logica.
    > [!div class="mx-imgBorder"]
    > ![Aggiunta di trigger](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Immettere le informazioni di connessione per l'account di archiviazione BLOB che si vuole monitorare per le aggiunte o le modifiche del BLOB. Selezionare il contenitore da monitorare. 
 
    Scegliere **l'intervallo** e la **frequenza** per eseguire il polling degli aggiornamenti più adatti alle persone.  

    > [!NOTE]
    > Questo trigger monitorerà il contenitore selezionato ma non monitorerà le sottocartelle.

1. Aggiungere un'azione HTTP che verrà eseguita quando viene rilevato un BLOB nuovo o modificato. Selezionare **Nuovo passaggio**, quindi cercare e selezionare l'azione HTTP.

  > [!div class="mx-imgBorder"]
  > ![Cerca azione HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Utilizzare le impostazioni seguenti per configurare l'azione:

  | Impostazione | valore | 
  |---|---|
  | Azione HTTP | POST |
  | URI |l'endpoint alla pipeline pubblicata trovata come [prerequisito](#prerequisites) |
  | Modalità di autenticazione | Identità gestita |

1. Impostare la pianificazione per impostare il valore di qualsiasi [DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) si può avere:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Utilizzare `DataStoreName` l'oggetto aggiunto all'area di lavoro come [prerequisito](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Impostazioni HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Selezionare **Salva** e la pianificazione è pronta.