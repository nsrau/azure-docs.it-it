---
title: Creare il primo esperimento di Machine Learning automatizzato
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire il training e distribuire un modello di classificazione con Machine Learning automatizzato nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 7c7d90d4ca1625edecc9d84e1ff7beec50032884
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444760"
---
# <a name="tutorial-train-and-deploy-a-classification-model-with-automated-machine-learning-in-the-azure-portal-preview"></a>Esercitazione: Eseguire il training e distribuire un modello di classificazione con Machine Learning automatizzato nel portale di Azure (anteprima)

Questa esercitazione illustra come creare il primo esperimento di Machine Learning automatizzato nel portale di Azure. In questo esempio viene creato un modello di classificazione per stimare se un client sottoscriverà un deposito di termini con la banca. 

Usando le funzionalità automatiche di Machine Learning del servizio e il portale di Azure, viene avviato il processo di Machine Learning automatico e la selezione dell'algoritmo e l'ottimizzazione degli iperparametri vengono eseguite automaticamente. La tecnica di Machine Learning automatizzata scorre molte combinazioni di algoritmi e iperparametri fino a trovare il modello migliore in base al criterio, tutto senza dover scrivere una sola riga di codice.

In questa esercitazione si apprenderanno informazioni sulle attività seguenti:

> [!div class="checklist"]
> * Creare un'area di lavoro del servizio Azure Machine Learning.
> * Creare un esperimento.
> * Eseguire automaticamente il training di un modello di classificazione.
> * Visualizza i dettagli dell'esecuzione di training.
> * Distribuire il modello.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* File di dati **bankmarketing_train. csv** . Il download è disponibile [qui](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Creare un esperimento

1. Passare al riquadro sinistro dell'area di lavoro. Selezionare **Machine Learning automatizzati** nella sezione **creazione e modifica (anteprima)** .

    ![Riquadro di spostamento portale di Azure](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    Poiché si tratta del primo esperimento con la Machine Learning automatizzata, verrà visualizzata la schermata **iniziale Machine Learning automatizzata** . 

1.  Selezionare **Crea esperimento**. Quindi immettere **My-1st-automl-Experiment** come nome dell'esperimento.

1. Selezionare **Crea un nuovo calcolo** e configurare il contesto di calcolo per questo esperimento.

    Campo| Valore
    ---|---
    Nome dell'ambiente di calcolo| Immettere un nome univoco che identifichi il contesto di calcolo. Per questo esempio viene usato **automl-Compute**.
    Dimensioni delle macchine virtuali| Selezionare le dimensioni della macchina virtuale per il calcolo. Viene usato **Standard_DS12_V2**.
    Impostazioni aggiuntive| *Nodo minimo*: 1. Per abilitare la profilatura dei dati, è necessario disporre di uno o più nodi. <br> *Nodo massimo*: 6. 

    Per avviare la creazione del nuovo calcolo, fare clic su **Crea**. Questa operazione può richiedere alcuni minuti. 

    Al termine della creazione, selezionare il nuovo calcolo nell'elenco a discesa, quindi fare clic su **Avanti**.

1. Per questa esercitazione viene usato l'account di archiviazione e il contenitore predefiniti creati con il nuovo calcolo. Questa operazione viene popolata automaticamente nel form.

1. Selezionare **carica** per scegliere il file **bankmarketing_train. csv** dal computer locale e caricarlo nel contenitore predefinito. L'anteprima pubblica supporta solo i caricamenti di file locali e gli account di archiviazione BLOB di Azure. Al termine del caricamento, selezionare il file dall'elenco. 

    [![Seleziona file di dati](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. La scheda **Anteprima** consente di configurare ulteriormente i dati per questo esperimento.

    Nella scheda Anteprima indicare che i dati includono le intestazioni. Per impostazione predefinita, il servizio include tutte le funzionalità (colonne) per il training. Per questo esempio, scorrere verso destra e **ignorare** la funzionalità **DAY_OF_WEEK** .

    ![Configurazione scheda Anteprima](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > Il profiling dei dati non è disponibile con i calcoli che dispongono di 0 nodi minimi.

1. Selezionare **classificazione** come attività di stima.

1. Selezionare **y** come colonna di destinazione, ovvero la colonna su cui si desidera eseguire stime. Questa colonna indica se il client ha sottoscritto o meno un termine di deposito.

1. Espandere le **Impostazioni avanzate** e popolare i campi come indicato di seguito.

    Impostazioni avanzate|Valore
    ------|------
    Metrica primaria| AUC_weighted 
    Criteri di uscita| Quando uno di questi criteri viene soddisfatto, il processo di training termina prima del completamento completo. <br> *Tempo del processo di training (minuti)* : 5  <br> *Numero massimo di iterazioni*: 10 
    Preelaborazione| Abilitare la pre-elaborazione eseguita da Machine Learning automatizzato. Sono incluse la pulizia automatica dei dati, la preparazione e la trasformazione per generare funzionalità sintetiche.
    Convalida| Selezionare convalida incrociata K-fold e 2 per il numero di convalide incrociate. 
    Concorrenza| Selezionare 5 per il numero massimo di iterazioni simultanee.

   >[!NOTE]
   > Per questo esperimento non viene impostata una soglia per le iterazioni o la metrica Max e non è necessario che gli algoritmi vengano sottoposti a test.

1. Fare clic su **Avvia** per eseguire l'esperimento.

   Una volta avviato l'esperimento, viene visualizzata una schermata dei **Dettagli di esecuzione** vuota con lo stato seguente nella parte superiore. L'esperimento di preparazione del processo richiede alcuni minuti. Al termine del processo di preparazione, il messaggio di stato viene modificato in **esecuzione**.

      ![Esecuzione preparazione](media/tutorial-1st-experiment-automated-ml/run-preparing.png)

##  <a name="view-experiment-details"></a>Visualizza i dettagli dell'esperimento

Con l'avanzamento dell'esperimento, la schermata **Esegui dettagli** aggiorna il grafico di iterazione e l'elenco con le diverse iterazioni (modelli) che vengono eseguite. L'elenco di iterazioni è in base al punteggio della metrica e, per impostazione predefinita, il modello che assegna un punteggio al più alto in base alla metrica **AUC_weighted** si trova nella parte superiore dell'elenco.

>[!TIP]
> Per completare l'esecuzione di ogni pipeline, i processi di training possono richiedere diversi minuti.

[![Dashboard Dettagli esecuzione](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-model"></a>Distribuzione del modello

Per questo esperimento, **VotingEnsemble** viene considerato il modello migliore in base alla metrica **AUC_weighted** . Con Machine Learning automatizzato nella portale di Azure, è possibile distribuire questo modello come servizio Web per prevedere i nuovi dati con un solo clic. 

1. Nella pagina **Esegui dettaglio** selezionare il pulsante **Distribuisci modello migliore** .

1. Compilare il riquadro **Distribuisci modello migliore** come indicato di seguito.

    Campo| Value
    ----|----
    Nome distribuzione| My-automl-deploy
    Descrizione della distribuzione| Prima distribuzione dell'esperimento di Machine Learning automatizzato
    Script di assegnazione dei punteggi| AutoGenerate
    Script dell'ambiente| AutoGenerate
    
1. Selezionare **Distribuisci**. Il completamento della distribuzione può richiedere circa 20 minuti.

    Quando la distribuzione viene completata correttamente, viene visualizzato il messaggio seguente.

    ![Distribuzione completata](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    La procedura è terminata. Per generare stime è necessario un servizio Web operativo.

## <a name="clean-up-resources"></a>Pulire le risorse

I file di distribuzione sono più grandi dei file di dati e di esperimento, quindi sono più costosi da archiviare. Eliminare solo i file di distribuzione per ridurre al minimo i costi per l'account e se si desidera tenere i file dell'area di lavoro e degli esperimenti. In caso contrario, eliminare l'intero gruppo di risorse, se non si prevede di usare alcun file.  

### <a name="delete-deployment-instance"></a>Elimina istanza di distribuzione

Eliminare solo l'istanza di distribuzione dalla portale di Azure, se si desidera lasciare il gruppo di risorse e l'area di lavoro per altre esercitazioni ed esplorazione. 

1. Passare al riquadro **Asset** a sinistra e selezionare distribuzioni . 

1. Selezionare la distribuzione che si desidera eliminare e selezionare **Elimina**. 

1. Selezionare **continua**.

### <a name="delete-resource-group"></a>Elimina gruppo di risorse

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione di Machine Learning automatizzata è stato usato il portale di Azure per creare e distribuire un modello di classificazione. Per ulteriori informazioni e passaggi successivi, vedere questi articoli.

+ Modalità di [utilizzo di un servizio Web](how-to-consume-web-service.md).
+ Altre informazioni sulla [pre-elaborazione](how-to-create-portal-experiments.md#preprocess).
+ Altre informazioni sul [profiling dei dati](how-to-create-portal-experiments.md#profile).
+ Altre informazioni su [Machine Learning automatizzato](concept-automated-ml.md).

>[!NOTE]
> Questo set di dati di [marketing bancario viene reso disponibile in Creative Commons (UCC: Licenza](https://creativecommons.org/publicdomain/zero/1.0/)di dominio pubblico). Tutti i diritti del singolo contenuto del database sono concessi in licenza con la licenza relativa ai [contenuti del database](https://creativecommons.org/publicdomain/zero/1.0/) ed è disponibile in [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Questo set di dati è originariamente disponibile all'interno del [database UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
>  Per citare il lavoro seguente: <br> [Moro et al., 2014] S. Moro, P. Cortez e P. Rita. Approccio basato sui dati per prevedere la riuscita del telemarketing bancario. Sistemi di supporto decisionale, Elsevier, 62:22-31, giugno 2014.