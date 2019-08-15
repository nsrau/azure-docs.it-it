---
title: Creare il primo esperimento di Machine Learning automatizzato
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire il training e distribuire un modello di classificazione con Machine Learning automatizzato nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 7ef19db472b30d82f14a5dd650cb8f4cb1f3ed3a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990068"
---
# <a name="tutorial-use-automated-machine-learning-to-train-and-deploy-your-first-classification-model-preview"></a>Esercitazione: Usare Machine Learning automatizzato per eseguire il training e distribuire il primo modello di classificazione (anteprima)

Questa esercitazione illustra come creare il primo esperimento di Machine Learning automatizzato nel portale di Azure. In questo esempio viene creato un modello di classificazione per stimare se un client sottoscriverà un termine deposit con la banca.

Usando le funzionalità automatiche di Machine Learning del servizio e il portale di Azure, si inizia il processo di Machine Learning automatizzato. La selezione dell'algoritmo e l'ottimizzazione degli iperparametri vengono eseguite per l'utente. La tecnica di Machine Learning automatizzata scorre molte combinazioni di algoritmi e iperparametri fino a quando non trova il modello migliore in base al criterio, tutto senza dover scrivere una sola riga di codice.

In questa esercitazione si apprenderanno informazioni sulle attività seguenti:

> [!div class="checklist"]
> * Creare un'area di lavoro del servizio Azure Machine Learning.
> * Creare un esperimento.
> * Eseguire il training automatico di un modello di classificazione.
> * Visualizza i dettagli dell'esecuzione di training.
> * Distribuire il modello.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).

* File di dati **bankmarketing_train. csv** . [Scaricarlo](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Creare un esperimento

1. Passare al riquadro sinistro dell'area di lavoro. Selezionare **Machine Learning automatico** nella sezione **creazione e modifica (anteprima)** .

    ![Riquadro di spostamento portale di Azure](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    Poiché si tratta del primo esperimento con la Machine Learning automatizzata, verrà visualizzata la schermata **iniziale Machine Learning automatizzata** . 

1. Selezionare **Crea esperimento**. Quindi immettere **My-1st-automl-Experiment** come nome dell'esperimento.

1. Selezionare **Crea un nuovo calcolo** e configurare il contesto di calcolo per questo esperimento.

    Campo| Value
    ---|---
    Nome dell'ambiente di calcolo| Immettere un nome univoco che identifichi il contesto di calcolo. Per questo esempio viene usato **automl-Compute**.
    Dimensioni delle macchine virtuali| Selezionare le dimensioni della macchina virtuale per il calcolo. Viene usato **Standard_DS12_V2**.
    Impostazioni aggiuntive| *Nodo minimo*: 1. Per abilitare la profilatura dei dati, è necessario disporre di uno o più nodi. <br> *Nodo massimo*: 6. 

    Per creare il nuovo calcolo, fare clic su **Crea**. Questa operazione richiede alcuni minuti. 

    Al termine della creazione, selezionare il nuovo calcolo nell'elenco a discesa, quindi fare clic su **Avanti**.

1. Per questa esercitazione viene usato l'account di archiviazione e il contenitore predefiniti creati con il nuovo calcolo. Vengono inseriti automaticamente nel form.

1. Selezionare **carica** e scegliere il file **bankmarketing_train. csv** dal computer locale per caricarlo nel contenitore predefinito. L'anteprima pubblica supporta solo i caricamenti di file locali e gli account di archiviazione BLOB di Azure. Al termine del caricamento, selezionare il file dall'elenco. 

    [![Seleziona file di dati](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. La scheda **Anteprima** consente di configurare ulteriormente i dati per questo esperimento.

    Nella scheda **Anteprima** indicare che i dati includono le intestazioni. Per impostazione predefinita, il servizio include tutte le funzionalità (colonne) per il training. Per questo esempio, scorrere verso destra e **ignorare** la funzionalità **DAY_OF_WEEK** .

    ![Configurazione della scheda Anteprima](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > Il profiling dei dati non è disponibile con i calcoli che contengono almeno zero nodi.

1. Selezionare **classificazione** come attività di stima.

1. Selezionare **y** come colonna di destinazione, dove si desidera eseguire stime. Questa colonna indica se il client ha sottoscritto o meno un termine di deposito.

1. Espandere **Impostazioni avanzate** e popolare i campi come indicato di seguito.

    Impostazioni avanzate|Value
    ------|------
    Metrica primaria| AUC_weighted 
    Criteri di uscita| Quando uno di questi criteri viene soddisfatto, il processo di training termina prima del completamento completo: <br> *Tempo del processo di training (minuti)* : 5  <br> *Numero massimo di iterazioni*: 10 
    Preelaborazione| Consente la pre-elaborazione eseguita da Machine Learning automatizzato. Sono incluse la pulizia automatica dei dati, la preparazione e la trasformazione per generare funzionalità sintetiche.
    Convalida| Selezionare convalida incrociata K-fold e **2** per il numero di convalide incrociate. 
    Concorrenza| Selezionare **5** per il numero massimo di iterazioni simultanee.

   >[!NOTE]
   > Per questo esperimento, non viene impostata una metrica o un numero massimo di core per iterazioni. Non è inoltre possibile bloccare la verifica degli algoritmi.

1. Selezionare **Start (avvia** ) per eseguire l'esperimento.

   Quando l'esperimento viene avviato, viene visualizzata una schermata dei **Dettagli di esecuzione** vuota con lo stato seguente nella parte superiore. 

      ![Esecuzione preparazione](media/tutorial-1st-experiment-automated-ml/run-preparing.png)
      
Il processo di preparazione dell'esperimento richiede un paio di minuti. Al termine del processo, il messaggio di stato viene modificato in **esecuzione**.

##  <a name="view-experiment-details"></a>Visualizza i dettagli dell'esperimento

Con l'avanzamento dell'esperimento, la schermata **Esegui dettagli** aggiorna il grafico di iterazione e l'elenco con le diverse iterazioni (modelli) che vengono eseguite. L'elenco delle iterazioni è in ordine in base al punteggio della metrica. Per impostazione predefinita, il modello che assegna un punteggio al più alto in base alla metrica **AUC_weighted** si trova nella parte superiore dell'elenco.

>[!TIP]
> Per completare l'esecuzione di ogni pipeline, i processi di training sono necessari alcuni minuti.

[![Dashboard Dettagli esecuzione](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Distribuire il modello

Per questo esperimento, **VotingEnsemble** viene considerato il modello migliore, in base alla metrica **AUC_weighted** . Utilizzando Machine Learning automatizzato nella portale di Azure, è possibile distribuire questo modello come servizio Web per prevedere i nuovi dati. 

1. Nella pagina **Esegui dettaglio** selezionare il pulsante **Distribuisci modello migliore** .

1. Popolare il riquadro **Distribuisci modello migliore** come segue:

    Campo| Value
    ----|----
    Nome distribuzione| My-automl-deploy
    Descrizione della distribuzione| Prima distribuzione dell'esperimento di Machine Learning automatizzato
    Script di assegnazione dei punteggi| AutoGenerate
    Script dell'ambiente| AutoGenerate
    
1. Selezionare **Distribuisci**. Il completamento della distribuzione richiede circa 20 minuti.

    Quando la distribuzione viene completata correttamente, viene visualizzato il messaggio seguente:

    ![Distribuzione completata](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    La procedura è terminata. Per generare stime è necessario un servizio Web operativo.

## <a name="clean-up-resources"></a>Pulire le risorse

I file di distribuzione sono più grandi dei file di dati e di esperimento, quindi sono più costosi da archiviare. Eliminare solo i file di distribuzione per ridurre al minimo i costi per l'account o se si desidera tenere i file dell'area di lavoro e degli esperimenti. In caso contrario, eliminare l'intero gruppo di risorse, se non si prevede di usare alcun file.  

### <a name="delete-the-deployment-instance"></a>Eliminare l'istanza di distribuzione

Eliminare solo l'istanza di distribuzione dalla portale di Azure, se si desidera lasciare il gruppo di risorse e l'area di lavoro per altre esercitazioni ed esplorazione. 

1. Passare al riquadro **Asset** a sinistra e selezionare distribuzioni. 

1. Selezionare la distribuzione che si desidera eliminare e selezionare **Elimina**. 

1. Selezionare **continua**.

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione di Machine Learning automatizzata è stato usato il portale di Azure per creare e distribuire un modello di classificazione. Per ulteriori informazioni e per i passaggi successivi, vedere questi articoli:

+ Informazioni su come [utilizzare un servizio Web](how-to-consume-web-service.md).
+ Altre informazioni sulla [pre-elaborazione](how-to-create-portal-experiments.md#preprocess).
+ Altre informazioni sul [profiling dei dati](how-to-create-portal-experiments.md#profile).
+ Altre informazioni su [Machine Learning automatizzato](concept-automated-ml.md).

>[!NOTE]
> Questo set di dati di [marketing bancario viene reso disponibile in Creative Commons (UCC: Licenza](https://creativecommons.org/publicdomain/zero/1.0/)di dominio pubblico). Tutti i diritti per i singoli contenuti del database sono concessi in licenza con la licenza relativa ai [contenuti del database](https://creativecommons.org/publicdomain/zero/1.0/) e sono disponibili in [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Questo set di dati era originariamente disponibile all'interno del [database UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> Per citare il lavoro seguente: <br> [Moro et al., 2014] S. Moro, P. Cortez e P. Rita. Approccio basato sui dati per prevedere la riuscita del telemarketing bancario. Sistemi di supporto decisionale, Elsevier, 62:22-31, giugno 2014.
