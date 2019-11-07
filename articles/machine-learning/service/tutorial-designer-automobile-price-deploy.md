---
title: 'Esercitazione: Distribuire un modello di Machine Learning con la finestra di progettazione'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare una soluzione di analisi predittiva nella finestra di progettazione di Azure Machine Learning (anteprima). Eseguire il training, assegnare punteggi e distribuire un modello di Machine Learning usando moduli con trascinamento della selezione.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 69fba508eac4b778dcd72371fd1471625ecb8c1a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509595"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Esercitazione: Distribuire un modello di Machine Learning con la finestra di progettazione (anteprima)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Per offrire ad altri utenti la possibilità di usare il modello predittivo sviluppato nella [prima parte dell'esercitazione](tutorial-designer-automobile-price-train-score.md), è possibile distribuirlo come endpoint in tempo reale. Nella prima parte è stato eseguito il training del modello. Ora è il momento di generare nuove stime in base all'input dell'utente. In questa parte dell'esercitazione verranno eseguite queste operazioni:

> [!div class="checklist"]
> * Distribuire un endpoint in tempo reale
> * Creare un cluster di inferenza
> * Testare un endpoint in tempo reale

## <a name="prerequisites"></a>Prerequisiti

Completare [la prima parte dell'esercitazione](tutorial-designer-automobile-price-train-score.md) per apprendere come eseguire il training e assegnare un punteggio a un modello di Machine Learning nella finestra di progettazione.

## <a name="deploy-a-real-time-endpoint"></a>Distribuire un endpoint in tempo reale

Per distribuire la pipeline è necessario:

1. Convertire la pipeline di training in una pipeline di inferenza in tempo reale, che rimuove i moduli di training e aggiunge gli input e gli output per le richieste di inferenza.
1. Distribuire la pipeline di inferenza.

### <a name="create-a-real-time-inference-pipeline"></a>Creare una pipeline di inferenza in tempo reale

1. Nella parte superiore del canvas della pipeline selezionare **Create inference pipeline** > **Real-time inference pipeline** (Crea pipeline di inferenza > Pipeline di inferenza in tempo reale)

    Quando si seleziona **Create inference pipeline** accadono diverse cose:
    
    * Il modello sottoposto a training viene archiviato come modulo **Dataset** (Set di dati) nel riquadro dei moduli. È possibile trovarlo in **My Datasets** (Set di dati personali).
    * I moduli usati per il training, come **Train Model** (Training modello) e **Split Data** (Divisione dati), vengono rimossi.
    * Il modello con training salvato viene aggiunto nuovamente alla pipeline.
    * Vengono aggiunti i moduli **Web Service Input** (Input servizio Web) e **Web Service Output** (Output servizio Web). Questi moduli identificano il punto in cui i dati dell'utente verranno immessi nel modello e il punto in cui verranno restituiti.

    > [!Note]
    > La **pipeline di training** viene salvata nella nuova scheda nella parte superiore del canvas della pipeline. È possibile trovarla anche come pipeline pubblicata nella finestra di progettazione.
    >

    La pipeline dovrebbe ora avere un aspetto simile al seguente:  

   ![Screenshot della configurazione prevista della pipeline dopo la preparazione per la distribuzione](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

1. Selezionare **Run** (Esegui) e usare la stessa destinazione di calcolo e lo stesso esperimento usati nella prima parte.

1. Selezionare il modulo **Score Model** (Punteggio modello).

1. Nel riquadro delle proprietà selezionare **Outputs** > **Visualize** (Output > Visualizza) per verificare che il modello funzioni ancora. Come si può notare, vengono visualizzati i dati originali insieme al prezzo stimato ("Scored Labels", Etichette con punteggio).

1. Selezionare **Distribuisci**.

### <a name="create-an-inferencing-cluster"></a>Creare un cluster di inferenza

Nella finestra di dialogo visualizzata è possibile selezionare uno dei cluster del servizio Azure Kubernetes esistenti nell'area di lavoro per distribuire il modello. Se non si ha un cluster del servizio Azure Kubernetes, seguire questa procedura per crearne uno.

1. Selezionare **Compute** (Calcolo) nella finestra di dialogo per passare alla pagina **Compute** (Calcolo).

1. Nella barra multifunzione di spostamento selezionare **Inference Clusters** >  **+ New** (Cluster di inferenza > Nuovo).

    ![Screenshot che mostra come passare al riquadro del nuovo cluster di inferenza](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. Nel riquadro del cluster di inferenza configurare un nuovo servizio Kubernetes.

1. Immettere "aks-compute" in **Compute name** (Nome del calcolo).
    
1. In **Region** (Area) selezionare un'area disponibile nelle vicinanze.

1. Selezionare **Create** (Crea).

    > [!Note]
    > La creazione di un nuovo servizio Azure Kubernetes richiede circa 15 minuti. È possibile controllare lo stato del provisioning nella pagina **Inference Clusters** (Cluster di inferenza)
    >

### <a name="deploy-the-real-time-endpoint"></a>Distribuire l'endpoint in tempo reale

Al termine del provisioning del servizio Azure Kubernetes, tornare alla pipeline di inferenza in tempo reale per completare la distribuzione.

1. Selezionare **Deploy** (Distribuisci) sopra il canvas.

1. Selezionare **Deploy new real-time endpoint** (Distribuisci nuovo endpoint in tempo reale). 

1. Selezionare il cluster del servizio Azure Kubernetes creato.

1. Selezionare **Distribuisci**.

    ![Screenshot che mostra come configurare un nuovo endpoint in tempo reale](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Al termine della distribuzione, che può impiegare alcuni minuti, verrà visualizzata una notifica di operazione riuscita sopra il canvas.

## <a name="test-the-real-time-endpoint"></a>Testare l'endpoint in tempo reale

È possibile testare l'endpoint in tempo reale passando alla pagina **Endpoints** nel riquadro di spostamento dell'area di lavoro a sinistra.

1. Nella pagina **Endpoints** selezionare l'endpoint appena distribuito.

    ![Screenshot che mostra la scheda degli endpoint in tempo reale con l'endpoint appena creato evidenziato](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. Selezionare **Test**.

1. Immettere i dati di test oppure usare i dati di esempio compilati automaticamente e selezionare **Test**.

    La richiesta di test viene inviata all'endpoint e i risultati vengono visualizzati nella pagina. Anche se generato, il valore di prezzo per i dati di input non viene usato per generare il valore della stima.

    ![Screenshot che mostra come testare l'endpoint in tempo reale con l'etichetta con punteggio del prezzo evidenziata](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati i principali passaggi per creare, distribuire e usare un modello di Machine Learning nella finestra di progettazione. Per altre informazioni su come usare la finestra di progettazione per risolvere altri tipi di problemi, vedere le altre pipeline di esempio.

> [!div class="nextstepaction"]
> [Esempio di classificazione del rischio di credito](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
