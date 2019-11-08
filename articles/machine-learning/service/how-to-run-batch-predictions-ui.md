---
title: Eseguire stime batch tramite la finestra di progettazione di Azure Machine Learning (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training di un modello e configurare una pipeline di stime batch usando la finestra di progettazione. Distribuire la pipeline come servizio Web con parametri, che può essere attivato da qualsiasi libreria HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: c496e57ea5f4b5b7fcda4f9c43c511488ef3c246
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509435"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Eseguire stime batch tramite la finestra di progettazione di Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa guida pratica viene illustrato come usare la finestra di progettazione per eseguire il training di un modello e configurare una pipeline di stime batch e un servizio Web. La stima batch consente l'assegnazione di un punteggio continua e su richiesta per modelli sottoposti a training in set di dati di grandi dimensioni, facoltativamente configurata come servizio Web che può essere attivato a partire da qualsiasi libreria HTTP. 

Per la configurazione di servizi di assegnazione punteggio batch con l'SDK, vedere la [guida pratica](how-to-run-batch-predictions.md) associata.

In questa guida pratica si apprenderà come eseguire queste attività:

> [!div class="checklist"]
> * Creare un esperimento ML di base in una pipeline
> * Creare una pipeline di inferenza batch con parametri
> * Gestire ed eseguire pipeline manualmente o da un endpoint REST

## <a name="prerequisites"></a>Prerequisiti

1. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

1. Creare un'[area di lavoro](tutorial-1st-experiment-sdk-setup.md).

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/).

Questa guida pratica presuppone una conoscenza di base della procedura di creazione di una pipeline semplice nella finestra di progettazione. Per un'introduzione guidata alla finestra di progettazione, completare l'[esercitazione](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-pipeline"></a>Creare una pipeline

Per creare una pipeline di inferenza batch, è necessario prima di tutto un esperimento di Machine Learning. Per crearne uno, passare alla scheda **Finestra di progettazione** nell'area di lavoro e creare una nuova pipeline selezionando l'opzione **Easy-to-use prebuilt modules** (Moduli predefiniti facili da usare).

![Home page della finestra di progettazione](media/how-to-run-batch-predictions-ui/ui-batch-scoring-1.png)

Di seguito è riportato un modello di Machine Learning semplice a scopo dimostrativo. I dati sono un set di dati registrato creato a partire da dati sul diabete in set di dati aperti di Azure. Vedere la [sezione della guida pratica](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) per la registrazione di set di dati a partire da set di dati aperti di Azure. I dati vengono suddivisi in set di training e di convalida e viene eseguito il training e l'assegnazione di un punteggio per un albero delle decisioni con boosting scalabile. La pipeline deve essere eseguita almeno una volta per poter creare una pipeline di inferenza. Fare clic sul pulsante **Esegui** per eseguire la pipeline.

![Creare un esperimento semplice](media/how-to-run-batch-predictions-ui/ui-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Creare una pipeline di inferenza batch

Ora che la pipeline è stata eseguita, è disponibile una nuova opzione accanto a **Esegui** e **Pubblica** denominata **Create inference pipeline** (Crea pipeline inferenza). Fare clic sull'elenco a discesa e selezionare **Batch inference pipeline** (Pipeline inferenza batch).

![Creare una pipeline di inferenza batch](media/how-to-run-batch-predictions-ui/ui-batch-scoring-5.png)

Il risultato è una pipeline di inferenza batch predefinita che include un nodo per la configurazione dell'esperimento della pipeline originale, un nodo per i dati non elaborati per l'assegnazione di un punteggio e un nodo per assegnare un punteggio ai dati non elaborati rispetto alla pipeline originale.

![Pipeline di inferenza batch predefinita](media/how-to-run-batch-predictions-ui/ui-batch-scoring-6.png)

È possibile aggiungere altri nodi per modificare il comportamento del processo di inferenza batch. In questo esempio si aggiunge un nodo per il campionamento casuale dai dati di input prima dell'assegnazione del punteggio. Creare un nodo **Partition and Sample** e posizionarlo tra i nodi di dati non elaborati e di punteggio. Fare quindi clic sul nodo **Partition and Sample** per accedere alle impostazioni e ai parametri.

![Nuovo nodo](media/how-to-run-batch-predictions-ui/ui-batch-scoring-7.png)

Il parametro *Rate of sampling* (Frequenza di campionamento) controlla la percentuale del set di dati originale da cui prendere un campione casuale. Si tratta di un parametro che sarà utile adattare frequentemente, quindi abilitarlo come parametro della pipeline. I parametri della pipeline possono essere modificati in fase di esecuzione e possono essere specificati in un oggetto payload quando si riesegue la pipeline da un endpoint REST. 

Per abilitare questo campo come parametro della pipeline, fare clic sui puntini di sospensione sopra il campo e quindi su **Add to pipeline parameter** (Aggiungi a parametro pipeline). 

![Impostazioni del campione](media/how-to-run-batch-predictions-ui/ui-batch-scoring-8.png)

Assegnare quindi al parametro un nome e un valore predefinito. Il nome verrà usato per identificare il parametro e specificarlo in una chiamata REST.

![Parametro della pipeline](media/how-to-run-batch-predictions-ui/ui-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Distribuire pipeline di inferenza batch

È ora possibile distribuire la pipeline. Fare clic sul pulsante **Distribuisci**, che apre l'interfaccia per la configurazione di un endpoint. Fare clic sull'elenco a discesa e selezionare **New PipelineEndpoint** (Nuovo PipelineEndpoint).

![Distribuzione della pipeline](media/how-to-run-batch-predictions-ui/ui-batch-scoring-10.png)

Specificare un nome e una descrizione facoltativa per l'endpoint. In prossimità della parte inferiore è visibile il parametro `sample-rate` configurato con un valore predefinito pari a 0,8. Quando si è pronti, fare clic su **Distribuisci**.

![Configurare l'endpoint](media/how-to-run-batch-predictions-ui/ui-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Gestire gli endpoint 

Al termine della distribuzione, passare alla scheda **Endpoint** e fare clic sul nome dell'endpoint appena creato.

![Collegamento dell'endpoint](media/how-to-run-batch-predictions-ui/ui-batch-scoring-12.png)

Questa schermata mostra tutte le pipeline pubblicate nell'endpoint specifico. Fare clic sulla pipeline di inferenza.

![Pipeline di inferenza](media/how-to-run-batch-predictions-ui/ui-batch-scoring-13.png)

La pagina dei dettagli della pipeline mostra la cronologia delle esecuzioni dettagliata e le informazioni della stringa di connessione per la pipeline. Fare clic sul pulsante **Esegui** per creare un'esecuzione manuale della pipeline.

![Dettagli della pipeline](media/how-to-run-batch-predictions-ui/ui-batch-scoring-14.png)

Nella configurazione dell'esecuzione è possibile specificare una descrizione per l'esecuzione e modificare il valore dei parametri della pipeline. Questa volta, eseguire di nuovo la pipeline di inferenza con una frequenza di campionamento pari a 0,9. Fare clic su **Esegui** per eseguire la pipeline.

![Esecuzione della pipeline](media/how-to-run-batch-predictions-ui/ui-batch-scoring-15.png)

La scheda **Consume** (Uso) contiene l'endpoint REST per la riesecuzione della pipeline. Per eseguire una chiamata REST, è necessaria un'intestazione di autenticazione di tipo bearer token OAuth 2.0. Vedere la [sezione dell'esercitazione](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) seguente per informazioni più dettagliate sulla configurazione dell'autenticazione per l'area di lavoro e l'esecuzione di una chiamata REST con parametri.

## <a name="next-steps"></a>Passaggi successivi

Seguire l'[esercitazione](tutorial-designer-automobile-price-train-score.md) relativa alla finestra di progettazione per eseguire il training e la distribuzione di un modello di regressione.