---
title: Eseguire stime batch tramite la finestra di progettazione di Azure Machine Learning (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training di un modello e configurare una pipeline di stime batch usando la finestra di progettazione. Distribuire la pipeline come servizio Web con parametri, che può essere attivato da qualsiasi libreria HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477222"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Eseguire stime batch tramite la finestra di progettazione di Azure Machine Learning (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In questo articolo viene illustrato come utilizzare la finestra di progettazione per creare una pipeline di stima batch. La stima batch consente di assegnare continuamente un punteggio a set di dati di grandi dimensioni su richiesta usando un servizio Web che può essere attivato da qualsiasi libreria HTTP.

In questa procedura si apprenderà a eseguire le attività seguenti:In this how-to, you learn to do the following tasks:

> [!div class="checklist"]
> * Creare e pubblicare una pipeline di inferenza batchCreate and publish a batch inference pipeline
> * Usare un endpoint della pipelineConsume a pipeline endpoint
> * Gestire le versioni degli endpointManage endpoint versions

Per informazioni su come configurare i servizi di assegnazione dei batch con l'SDK, vedere le [relative modalità](how-to-run-batch-predictions.md)di utilizzo.

## <a name="prerequisites"></a>Prerequisiti

In questo modo si presuppone che si disponga già di una pipeline di training. Per un'introduzione guidata alla finestra di progettazione, completare [la prima parte dell'esercitazione sulla finestra di progettazione.](tutorial-designer-automobile-price-train-score.md) 

## <a name="create-a-batch-inference-pipeline"></a>Creare una pipeline di inferenza batch

La pipeline di training deve essere eseguita almeno una volta per poter creare una pipeline di inferenza.

1. Passare alla scheda **Designer** nell'area di lavoro.

1. Selezionare la pipeline di training che esegue il training del modello da usare per eseguire la stima.

1. **Inviare** la pipeline.

    ![Inviare la pipeline](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Ora che la pipeline di training è stata eseguita, è possibile creare una pipeline di inferenza batch.

1. Accanto a **Invia**, selezionare il nuovo elenco a discesa Crea pipeline di **inferenza**.

1. Selezionare **Pipeline di inferenza batch**.

    ![Creare una pipeline di inferenza batch](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Il risultato è una pipeline di inferenza batch predefinita 

### <a name="add-a-pipeline-parameter"></a>Aggiungere un parametro della pipelineAdd a pipeline parameter

Per creare stime sui nuovi dati, è possibile connettere manualmente un set di dati diverso in questa visualizzazione bozza della pipeline o creare un parametro per il set di dati. I parametri consentono di modificare il comportamento del processo di inferenza batch in fase di esecuzione.

In questa sezione viene creato un parametro del set di dati per specificare un set di dati diverso in base al quali eseguire le stime.

1. Selezionare il modulo del set di dati.

1. Verrà visualizzato un riquadro a destra dell'area di disegno. Nella parte inferiore del riquadro selezionare **Imposta come parametro della pipeline**.
   
    Immettere un nome per il parametro o accettare il valore predefinito.

## <a name="publish-your-batch-inferencing-pipeline"></a>Pubblicare la pipeline di inferenza del batchPublish your batch inferencing pipeline

A questo punto si è pronti per distribuire la pipeline di inferenza. In questo modo verrà distribuita la pipeline e disponibile per l'utilizzo da parte di altri utenti.

1. Fare clic sul pulsante **Pubblica**.

1. Nella finestra di dialogo visualizzata espandere l'elenco a discesa **PipelineEndpoint**e selezionare **New PipelineEndpoint**.

1. Specificare un nome di endpoint e una descrizione facoltativa.

    Nella parte inferiore della finestra di dialogo è possibile visualizzare il parametro configurato con un valore predefinito dell'ID del set di dati usato durante il training.

1. Selezionare **Pubblica**.

![Pubblicare una pipeline](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Utilizzo di un endpointConsume an endpoint

A questo punto, si dispone di una pipeline pubblicata con un parametro del set di dati. La pipeline utilizzerà il modello sottoposto a training creato nella pipeline di training per assegnare un punteggio al set di dati fornito come parametro.

### <a name="submit-a-pipeline-run"></a>Inviare un'esecuzione della pipelineSubmit a pipeline run 

In questa sezione verrà impostata un'esecuzione manuale della pipeline e verrà modificato il parametro della pipeline per assegnare un punteggio ai nuovi dati. 

1. Al termine della distribuzione, passare alla sezione Endpoint.After the deployment is complete, go to the **Endpoints** section.

1. Selezionare **Endpoint pipeline**.

1. Selezionare il nome dell'endpoint creato.

![Collegamento dell'endpoint](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Selezionare **Pipeline pubblicate**.

    Questa schermata mostra tutte le pipeline pubblicate in questo endpoint.

1. Selezionare la pipeline pubblicata.

    La pagina dei dettagli della pipeline mostra una cronologia dettagliata dell'esecuzione e informazioni sulla stringa di connessione per la pipeline. 
    
1. Selezionare **Invia** per creare un'esecuzione manuale della pipeline.

    ![Dettagli della pipeline](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Modificare il parametro per utilizzare un set di dati diverso.
    
1. Selezionare **Invia** per eseguire la pipeline.

### <a name="use-the-rest-endpoint"></a>Usare l'endpoint RESTUse the REST endpoint

È possibile trovare informazioni su come utilizzare gli endpoint della pipeline e la pipeline pubblicata nella sezione Endpoint.You can find information on how to consume pipeline endpoints and published pipeline in the **Endpoints** section.

È possibile trovare l'endpoint REST di un endpoint della pipeline nel riquadro panoramica dell'esecuzione. Chiamando l'endpoint, si utilizza la pipeline pubblicata predefinita.

È anche possibile usare una pipeline pubblicata nella pagina **Pipeline pubblicate.** Selezionare una pipeline pubblicata e individuarne l'endpoint REST. 

![Dettagli dell'endpoint di riposoRest endpoint details](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

Per effettuare una chiamata REST, è necessaria un'intestazione di autenticazione di tipo portatore OAuth 2.0.To make a REST call, you will need an OAuth 2.0 bearer-type authentication header. Vedere la [sezione dell'esercitazione](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) seguente per informazioni più dettagliate sulla configurazione dell'autenticazione per l'area di lavoro e l'esecuzione di una chiamata REST con parametri.

## <a name="versioning-endpoints"></a>Endpoint di controllo delle versioniVersioning endpoints

La finestra di progettazione assegna una versione a ogni pipeline successiva pubblicata in un endpoint. È possibile specificare la versione della pipeline che si desidera eseguire come parametro nella chiamata REST. Se non si specifica un numero di versione, la finestra di progettazione utilizzerà la pipeline predefinita.

Quando si pubblica una pipeline, è possibile scegliere di impostarla come nuova pipeline predefinita per l'endpoint.

![Imposta pipeline predefinita](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

È inoltre possibile impostare una nuova pipeline predefinita nella scheda **Pipeline pubblicate** dell'endpoint.

![Imposta pipeline predefinita](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Passaggi successivi

Seguire [l'esercitazione](tutorial-designer-automobile-price-train-score.md) sulla finestra di progettazione per eseguire il training e distribuire un modello di regressione.
''