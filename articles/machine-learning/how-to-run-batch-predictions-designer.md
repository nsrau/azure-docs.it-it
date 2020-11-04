---
title: Eseguire stime batch tramite la finestra di progettazione di Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training di un modello e configurare una pipeline di stime batch usando la finestra di progettazione. Distribuire la pipeline come servizio Web con parametri, che può essere attivato da qualsiasi libreria HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: f070cfc1fd9b4326c5a80ce31f6263aadbe8e8bc
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325446"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Eseguire stime batch tramite la finestra di progettazione di Azure Machine Learning


In questo articolo viene descritto come usare la finestra di progettazione per creare una pipeline di stima in batch. La stima in batch consente di assegnare in modo continuo un punteggio a set di dati di grandi dimensioni su richiesta usando un servizio Web che può essere attivato da qualsiasi libreria HTTP.

In questa guida pratica si apprenderà come eseguire queste attività:

> [!div class="checklist"]
> * Creare e pubblicare una pipeline di inferenza batch
> * Utilizzo di un endpoint della pipeline
> * Gestire le versioni degli endpoint

Per informazioni su come configurare i servizi di assegnazione del punteggio batch con l'SDK, vedere la [guida pratica](./tutorial-pipeline-batch-scoring-classification.md) associata.

## <a name="prerequisites"></a>Prerequisiti

In questa guida pratica si presuppone che sia già presente una pipeline di training. Per un'introduzione guidata alla finestra di progettazione, eseguire la [prima parte dell'esercitazione relativa alla finestra di progettazione](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-batch-inference-pipeline"></a>Creare una pipeline di inferenza batch

La pipeline di training deve essere eseguita almeno una volta per poter creare una pipeline di inferenza.

1. Passare alla scheda **Finestra di progettazione** nell'area di lavoro.

1. Selezionare la pipeline di training che esegue il training del modello che si desidera usare per la stima.

1. **Inviare** la pipeline.

    ![Inviare la pipeline](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Ora che la pipeline di training è stata eseguita, è possibile creare una pipeline di inferenza batch.

1. Accanto a **Submit** (Invia), selezionare il nuovo elenco a discesa **Create inference pipeline** (Crea pipeline inferenza).

1. Selezionare **Batch inference pipeline** (Pipeline inferenza batch).

    ![Creare una pipeline di inferenza batch](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Il risultato è una pipeline di inferenza batch predefinita 

### <a name="add-a-pipeline-parameter"></a>Aggiungere un parametro della pipeline

Per creare stime sui nuovi dati, è possibile connettere manualmente un set di dati diverso nella visualizzazione bozza della pipeline o creare un parametro per il set di dati. I parametri consentono di modificare il comportamento del processo di inferenza batch durante il runtime.

In questa sezione viene creato un parametro del set di dati per specificare un set di dati diverso per cui eseguire stime.

1. Selezionare il modulo del set di dati.

1. Viene visualizzato un riquadro a destra dell'area di disegno. Nella parte inferiore del riquadro selezionare **Set as pipeline parameter** (Imposta come parametro pipeline).
   
    Immettere un nome per il parametro o accettare il valore predefinito.

    > [!div class="mx-imgBorder"]
    > ![Imposta set di dati come parametro della pipeline](./media/how-to-run-batch-predictions-designer/set-dataset-as-pipeline-parameter.png)

## <a name="publish-your-batch-inference-pipeline"></a>Pubblicare la pipeline di inferenza batch

A questo punto si è pronti per distribuire la pipeline di inferenza. La pipeline verrà distribuita e resa disponibile per l'uso da parte di altri utenti.

1. Fare clic sul pulsante **Pubblica**.

1. Nella finestra di dialogo visualizzata espandere l'elenco a discesa di **PipelineEndpoint** e selezionare **New PipelineEndpoint** (Nuovo PipelineEndpoint).

1. Specificare un nome e una descrizione facoltativa per l'endpoint.

    Nella parte inferiore della finestra di dialogo è possibile visualizzare il parametro configurato con un valore predefinito dell'ID set di dati usato durante il training.

1. Selezionare **Pubblica**.

![Pubblicare una pipeline](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Utilizzare un endpoint

A questo punto è disponibile una pipeline pubblicata con un parametro del set di dati. La pipeline userà il modello di cui è stato eseguito il training creato nella pipeline di training per assegnare un punteggio al set di dati specificato come parametro.

### <a name="submit-a-pipeline-run"></a>Avviare un'esecuzione della pipeline 

In questa sezione verrà configurata un'esecuzione manuale della pipeline e verrà modificato il parametro della pipeline per assegnare un punteggio ai nuovi dati. 

1. Al termine della distribuzione, passare alla sezione **Endpoints** (Endpoint).

1. Selezionare **Pipeline endpoints** (Endpoint pipeline).

1. Selezionare il nome dell'endpoint creato.

![Collegamento dell'endpoint](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Selezionare **Published pipelines** (Pipeline pubblicate).

    Questa schermata mostra tutte le pipeline pubblicate nell'endpoint.

1. Selezionare la pipeline pubblicata.

    La pagina dei dettagli della pipeline mostra una cronologia delle esecuzioni dettagliata e le informazioni della stringa di connessione per la pipeline. 
    
1. Selezionare **Submit** (Invia) per creare un'esecuzione manuale della pipeline.

    ![Dettagli della pipeline](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Modificare il parametro per usare un set di dati diverso.
    
1. Selezionare **Submit** (Invia) per eseguire la pipeline.

### <a name="use-the-rest-endpoint"></a>Usare l'endpoint REST

Per informazioni su come utilizzare gli endpoint della pipeline e la pipeline pubblicata, vedere la sezione **Endpoints** (Endpoint).

È possibile trovare l'endpoint REST di un endpoint della pipeline nel pannello di panoramica dell'esecuzione. Chiamando l'endpoint, si utilizza la pipeline pubblicata predefinita.

È anche possibile utilizzare una pipeline pubblicata nella pagina **Published pipelines** (Pipeline pubblicate). Selezionare una pipeline pubblicata ed è possibile trovare l'endpoint REST nel pannello panoramica della **pipeline pubblicata** a destra del grafo. 

Per eseguire una chiamata REST è necessaria un'intestazione di autenticazione di tipo bearer token OAuth 2.0. Vedere la [sezione dell'esercitazione](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) seguente per informazioni più dettagliate sulla configurazione dell'autenticazione per l'area di lavoro e l'esecuzione di una chiamata REST con parametri.

## <a name="versioning-endpoints"></a>Controllo delle versioni degli endpoint

La finestra di progettazione assegna una versione a ogni pipeline successiva pubblicata in un endpoint. È possibile specificare la versione della pipeline che si vuole eseguire come parametro nella chiamata REST. Se non si specifica un numero di versione, la finestra di progettazione userà la pipeline predefinita.

Quando si pubblica una pipeline, è possibile scegliere di impostarla come nuova pipeline predefinita per l'endpoint.

![Impostare la pipeline predefinita](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

È anche possibile impostare una nuova pipeline predefinita nella scheda **Published pipelines** (Pipeline pubblicate) dell'endpoint.

![Imposta pipeline predefinita nella pagina della pipeline pubblicata](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Passaggi successivi

Seguire l'[esercitazione](tutorial-designer-automobile-price-train-score.md) relativa alla finestra di progettazione per eseguire il training e la distribuzione di un modello di regressione.
''