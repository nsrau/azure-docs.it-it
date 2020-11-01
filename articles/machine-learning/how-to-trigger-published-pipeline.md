---
title: Attiva la pipeline di ML per i nuovi dati
titleSuffix: Azure Machine Learning
description: Informazioni su come attivare l'esecuzione di una pipeline di ML usando app per la logica di Azure.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4
ms.openlocfilehash: 64f1c83a570e936759d674f40db201fb2f2cd0e5
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146263"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Attivare un'esecuzione di una pipeline di Machine Learning da un'app per la logica

Attiva l'esecuzione della pipeline di Azure Machine Learning quando vengono visualizzati nuovi dati. Ad esempio, potrebbe essere necessario attivare la pipeline per eseguire il training di un nuovo modello quando vengono visualizzati nuovi dati nell'account di archiviazione BLOB. Configurare il trigger con app per la [logica di Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* Endpoint REST per una pipeline Machine Learning pubblicata. [Creare e pubblicare la pipeline](how-to-create-your-first-pipeline.md). Individuare quindi l'endpoint REST della PublishedPipeline usando l'ID della pipeline:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-overview.md) per archiviare i dati.
* [Archivio](how-to-access-data.md) dati nell'area di lavoro che contiene i dettagli dell'account di archiviazione BLOB.

## <a name="create-a-logic-app"></a>Creare un'app per la logica

Creare ora un'istanza di app per la [logica di Azure](../logic-apps/logic-apps-overview.md) . Se si vuole, [usare un ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) e [configurare una chiave gestita dal cliente](../logic-apps/customer-managed-keys-integration-service-environment.md) per l'uso da parte dell'app per la logica.

Una volta eseguito il provisioning dell'app per la logica, seguire questa procedura per configurare un trigger per la pipeline:

1. [Creare un'identità gestita assegnata dal sistema](../logic-apps/create-managed-service-identity.md) per concedere all'app l'accesso all'area di lavoro di Azure Machine Learning.

1. Passare alla visualizzazione progettazione app per la logica e selezionare il modello app per la logica vuota. 
    > [!div class="mx-imgBorder"]
    > ![Modello Vuoto](media/how-to-trigger-published-pipeline/blank-template.png)

1. Nella finestra di progettazione cercare **BLOB** . Selezionare il trigger **quando un BLOB viene aggiunto o modificato (solo proprietà)** e aggiungere questo trigger all'app per la logica.
    > [!div class="mx-imgBorder"]
    > ![Aggiunta di trigger](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Immettere le informazioni di connessione per l'account di archiviazione BLOB da monitorare per le aggiunte o le modifiche ai BLOB. Consente di selezionare il contenitore da monitorare. 
 
    Scegliere l' **intervallo** e la **frequenza** di polling per gli aggiornamenti che funzionano.  

    > [!NOTE]
    > Questo trigger eseguirà il monitoraggio del contenitore selezionato, ma non eseguirà il monitoraggio delle sottocartelle.

1. Aggiungere un'azione HTTP che verrà eseguita quando viene rilevato un BLOB nuovo o modificato. Selezionare **+ nuovo passaggio** , quindi cercare e selezionare l'azione http.

  > [!div class="mx-imgBorder"]
  > ![Cerca azione HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Usare le impostazioni seguenti per configurare l'azione:

  | Impostazione | Valore | 
  |---|---|
  | Azione HTTP | POST |
  | URI |endpoint della pipeline pubblicata che è stato trovato come [prerequisito](#prerequisites) |
  | Modalità di autenticazione | Identità gestita |

1. Configurare la pianificazione per impostare il valore di qualsiasi [percorso di DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) è possibile:

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

    Usare l' `DataStoreName` aggiunta all'area di lavoro come [prerequisito](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Impostazioni HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Selezionare **Save (Salva** ). la pianificazione è ora pronta.

> [!IMPORTANT]
> Se si usa il controllo degli accessi in base al ruolo (RBAC) per gestire l'accesso alla pipeline, [impostare le autorizzazioni per lo scenario della pipeline (training o assegnazione dei punteggi)](how-to-assign-roles.md#q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

> [!div class="nextstepaction"]
> [Usare le pipeline di Azure Machine Learning per l'assegnazione del punteggio batch](tutorial-pipeline-batch-scoring-classification.md)

* Altre informazioni sulle [pipeline](concept-ml-pipelines.md)
* Scopri di più sull' [esplorazione Azure Machine Learning con Jupyter](samples-notebooks.md)

