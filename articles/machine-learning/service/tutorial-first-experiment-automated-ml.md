---
title: Creare il primo esperimento automatizzato di apprendimento automatico
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire il training e la distribuzione di un modello di classificazione con apprendimento automatico nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 08/14/2019
ms.openlocfilehash: 01228dc01b8006a0a2476ddbbd6fa8ff430e280a
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982750"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Esercitazione: Creare il primo modello di classificazione con apprendimento automatico

In questa esercitazione viene descritto come creare il primo esperimento automatizzato di apprendimento automatico nel portale di Azure (anteprima) senza scrivere una sola riga di codice. Questo esempio crea un modello di classificazione per stimare se un cliente sottoscriverà un deposito a termine fisso presso un istituto finanziario.

Con l'apprendimento automatico automatizzato, è possibile automatizzare le attività a elevato utilizzo di tempo. L'apprendimento automatico automatizzato esegue rapidamente l'iterazione su numerose combinazioni di algoritmi e iperparametri per aiutare a trovare il modello migliore in base a una metrica di riuscita di propria scelta.

In questa esercitazione si apprenderà come eseguire le attività seguenti:

> [!div class="checklist"]
> * Creare un'area di lavoro del servizio Azure Machine Learning.
> * Eseguire un esperimento di Machine Learning automatizzato.
> * Visualizzare i dettagli sull'esperimento.
> * Distribuire il modello.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).

* Scaricare il file di dati [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv). La colonna **y** indica se un cliente ha effettuato la sottoscrizione di un deposito a termine fisso, che in seguito viene identificata come colonna di destinazione per le stime in questa esercitazione. 

## <a name="create-a-workspace"></a>Creare un'area di lavoro

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-and-run-the-experiment"></a>Creare ed eseguire l'esperimento

Questi passaggi descrivono la configurazione dell'esperimento, dalla selezione dei dati alla scelta della metrica principale e del tipo di modello. 

1. Passare al riquadro di sinistra dell'area di lavoro. Selezionare **Machine Learning automatizzato** nella sezione **Creazione (anteprima)** .
Verrà visualizzata la schermata di benvenuto di **Machine Learning automatizzato**, in quanto si tratta del primo esperimento con questo servizio.

    ![Riquadro di spostamento del portale di Azure](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

1. Selezionare **Create experiment** (Crea esperimento). Immettere quindi **my-1st-automl-experiment** come nome dell'esperimento.

1. Selezionare **Create a new compute** (Crea nuovo contesto di calcolo) e configurare il contesto di calcolo per questo esperimento.

    Campo| Valore
    ---|---
    Nome del calcolo| Immettere un nome univoco che identifichi il contesto di calcolo. Questo esempio usa **automl-compute**.
    Dimensioni della macchina virtuale| Selezionare le dimensioni della macchina virtuale per il contesto di calcolo. Qui viene usato **Standard_DS12_V2**.
    Impostazioni aggiuntive| *Min node* (Nodi minimi): 1. Per abilitare la profilatura dei dati, è necessario che siano presenti uno o più nodi. <br> *Max node* (Nodi max): 6. 

    Per creare il nuovo contesto di calcolo, selezionare **Crea**. Questa operazione richiede alcuni istanti. 

    Al termine della creazione, selezionare il nuovo contesto di calcolo nell'elenco a discesa e quindi fare clic su **Avanti**.

    >[!NOTE]
    >Per questa esercitazione vengono usati l'account di archiviazione e il contenitore predefiniti creati con il nuovo contesto di calcolo. Questi vengono immessi automaticamente nel modulo.

1. Selezionare **Carica** e scegliere il file **bankmarketing_train.csv** dal computer locale per caricarlo nel contenitore predefinito. L'anteprima pubblica supporta solo i caricamenti di file locali e gli account di archiviazione BLOB di Azure. Al termine del caricamento, selezionare il file dall'elenco. 

1. La scheda **Anteprima** permette di configurare ulteriormente i dati per questo esperimento.

    Nella scheda **Anteprima** indicare che i dati includono intestazioni. Per impostazione predefinita, il servizio include tutte le funzionalità (colonne) per il training. Per questo esempio, scorrere verso destra e scegliere **Ignora** per la funzionalità **day_of_week**.

    ![Configurazione della scheda Anteprima](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)

    >[!NOTE]
    > La profilatura dei dati non è disponibile con contesti di calcolo che includono zero nodi minimi.

1. Selezionare **Classificazione** come attività di stima.

1. Selezionare **y** come colonna di destinazione, quella in cui devono essere eseguite le stime. Questa colonna indica se il client ha sottoscritto o meno un deposito a termine.

1. Espandere **Impostazioni avanzate** e completare i campi come indicato di seguito.

    Impostazioni avanzate|Valore
    ------|------
    Primary metric (Metrica principale)| AUC_weighted 
    Criteri uscita| Quando uno di questi criteri viene soddisfatto, il processo di training termina prima del completamento: <br> *Training job time (minutes)* (Durata processo di training - minuti): 5  <br> *Max number of iterations* (Numero max di iterazioni): 10 
    Pre-elaborazione| Permette la pre-elaborazione tramite le funzionalità automatizzate di apprendimento automatico. Sono incluse la pulizia automatica dei dati, la preparazione e la trasformazione per generare funzionalità sintetiche.
    Convalida| Selezionare la convalida incrociata di K sezioni e **2** come numero di convalide incrociate. 
    Concorrenza| Selezionare **5** per il numero massimo di iterazioni simultanee.

   >[!NOTE]
   > Per questo esperimento, non viene impostata una metrica o un numero massimo di core come soglia delle iterazioni. Non vengono inoltre bloccati i test degli algoritmi.

1. Selezionare **Avvia** per eseguire l'esperimento.

   All'avvio dell'esperimento, viene visualizzata una schermata **Dettagli esecuzione** vuota con lo stato seguente nella parte superiore.
      
Il processo di preparazione dell'esperimento richiede un paio di minuti. Al termine del processo, il messaggio di stato diventa **Run is Running** (Esecuzione in corso).

##  <a name="view-experiment-details"></a>Visualizzare i dettagli sull'esperimento

Mentre l'esperimento continua, la schermata **Dettagli esecuzione** aggiorna il grafico e l'elenco delle iterazioni con le diverse iterazioni (modelli) eseguite. L'elenco delle iterazioni è in ordine in base al punteggio della metrica. Per impostazione predefinita, il modello che riceve il punteggio maggiore in base alla metrica **AUC_weighted** si trova all'inizio dell'elenco.

>[!TIP]
> I processi di training richiedono alcuni minuti prima che venga completata l'esecuzione di ogni pipeline.

[![Dashboard dei dettagli di esecuzione](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Distribuire il modello

Utilizzando le funzionalità automatizzate di apprendimento automatico nel portale di Azure, è possibile distribuire il modello migliore come servizio Web per eseguire stime sui nuovi dati e identificare le potenziali aree di opportunità. Per questo esperimento, attraverso la distribuzione l'istituto finanziario ha ora una soluzione iterativa e scalabile per l'identificazione dei potenziali clienti con deposito a termine fisso.

Nel contesto di questo esperimento **VotingEnsemble** viene considerato il modello migliore, in base alla metrica **AUC_weighted**.  Viene distribuito questo modello, ma tenere presente che il completamento della distribuzione richiede circa 20 minuti.

1. Nella pagina **Dettagli di esecuzione** selezionare il pulsante **Deploy Best Model** (Distribuisci modello migliore).

1. Immettere i dati nel riquadro **Deploy Best Model** (Distribuisci modello migliore) in questo modo:

    Campo| Valore
    ----|----
    Nome distribuzione| my-automl-deploy
    Descrizione della distribuzione| Distribuzione del primo esperimento automatizzato di apprendimento automatico
    Scoring script (Script di assegnazione punteggi)| Genera automaticamente
    Environment script (Script ambiente)| Genera automaticamente
    
1. Selezionare **Distribuisci**.

    Al termine della distribuzione, viene visualizzato un messaggio di completamento della distribuzione.
    
A questo punto, è disponibile un servizio Web operativo per generare stime.

## <a name="clean-up-resources"></a>Pulire le risorse

I file di distribuzione sono più grandi dei file di dati e di esperimento e di conseguenza più costosi da archiviare. Eliminare solo i file di distribuzione per ridurre al minimo i costi per l'account o se si vuole conservare i file dell'area di lavoro e degli esperimenti. In caso contrario, eliminare l'intero gruppo di risorse, se non si prevede di usare alcun file.  

### <a name="delete-the-deployment-instance"></a>Eliminare l'istanza di distribuzione

Eliminare solo l'istanza di distribuzione dal portale di Azure, se si vuole mantenere il gruppo di risorse e l'area di lavoro per altre esercitazioni o attività di esplorazione. 

1. Passare al riquadro **Asset** a sinistra e selezionare **Distribuzioni**. 

1. Selezionare la distribuzione che si vuole eliminare e scegliere **Elimina**. 

1. Selezionare **Continua**.

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione di apprendimento automatico con automazione è stato usato il portale di Azure per creare e distribuire un modello di classificazione. Per altre informazioni e per i passaggi successivi, vedere questi articoli:

> [!div class="nextstepaction"]
> [Utilizzare un servizio Web](how-to-consume-web-service.md)

+ Altre informazioni sulla [pre-elaborazione](how-to-create-portal-experiments.md#preprocess).
+ Altre informazioni sulla [profilatura dei dati](how-to-create-portal-experiments.md#profile).
+ [Funzionalità automatizzate di Machine Learning](concept-automated-ml.md).

>[!NOTE]
> Questo set di dati di marketing bancario viene reso disponibile in [Creative Comm (CCO: Public Domain) License](https://creativecommons.org/publicdomain/zero/1.0/). Tutti i diritti per i singoli contenuti del database vengono concessi in licenza ai sensi della [licenza relativa ai contenuti del database](https://creativecommons.org/publicdomain/zero/1.0/) e resi disponibili in [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Questo set di dati era originariamente disponibile all'interno del [database di Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> Tenere presenti le risorse seguenti: <br> [Moro et al., 2014] S. Moro, P. Cortez e P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, giugno 2014.
