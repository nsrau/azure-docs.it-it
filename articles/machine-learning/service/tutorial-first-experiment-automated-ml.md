---
title: Creare il primo esperimento automatizzato di apprendimento automatico
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire il training e la distribuzione di un modello di classificazione con Machine Learning automatizzato nella pagina di destinazione dell'area di lavoro di Azure Machine Learning (anteprima).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 0dd4447736469644875dff914c6284b087be87d0
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910212"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Esercitazione: Creare il primo modello di classificazione con apprendimento automatico

In questa esercitazione viene descritto come creare il primo esperimento di Machine Learning automatizzato nella pagina di destinazione dell'area di lavoro (anteprima) senza scrivere una sola riga di codice. Questo esempio crea un modello di classificazione per stimare se un cliente sottoscriverà un deposito a termine fisso presso un istituto finanziario.

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

Un'area di lavoro di Machine Learning è una risorsa cloud fondamentale usata per eseguire gli esperimenti, il training e la distribuzione di modelli di Machine Learning. Collega la sottoscrizione e il gruppo di risorse di Azure a un oggetto di facile utilizzo nel servizio. 

Per creare un'area di lavoro è necessario usare il portale di Azure, una console basata sul Web per la gestione delle risorse di Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Prendere nota dell'**area di lavoro** e della **sottoscrizione**. Si tratta di informazioni necessarie per assicurarsi di creare l'esperimento nel posto giusto. 

## <a name="create-and-run-the-experiment"></a>Creare ed eseguire l'esperimento

Completare i passaggi seguenti di configurazione ed esecuzione dell'esperimento nella pagina di destinazione dell'area di lavoro, un'interfaccia consolidata che include strumenti di Machine Learning per l'esecuzione di scenari di data science per esperti di data science di qualsiasi livello di competenza.

1. Effettuare l'accesso alla [pagina di destinazione dell'area di lavoro](https://ml.azure.com/workspaceportal/).

1. Selezionare la sottoscrizione e l'area di lavoro create.

1. Selezionare **Attività iniziali**.

1.  Selezionare **Automated ML** (Machine Learning automatizzato) nella sezione **Creazione** nel riquadro sinistro.
Verrà visualizzata la schermata **Introduzione**, in quanto si tratta del primo esperimento con Machine Learning automatizzato.

    ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Selezionare **Create experiment** (Crea esperimento). 

1. Immettere **my-1st-automl-experiment** come nome dell'esperimento.

1. Selezionare **Create a new compute** (Crea nuovo contesto di calcolo). 

    1. Configurare il nuovo contesto di calcolo per questo esperimento.
        
        Campo | Valore
        ----|---
        Nome del calcolo |  Immettere un nome univoco che identifichi il contesto di calcolo. Per questo esempio usare **automl-compute**.
        Dimensioni della macchina virtuale| Selezionare le dimensioni della macchina virtuale per il contesto di calcolo. Usare il valore predefinito **Standard_DS12_V2**.
        Impostazioni aggiuntive| *Min node* (Nodi minimi): 1. Per abilitare la profilatura dei dati, è necessario che siano presenti uno o più nodi. <br> *Max node* (Nodi max): 6.
 
    1. Per creare il nuovo contesto di calcolo, selezionare **Crea**. Il completamento dell'operazione richiede alcuni minuti. 

    1. Al termine della creazione, selezionare il nuovo contesto di calcolo nell'elenco a discesa e quindi fare clic su **Avanti**.

    >[!NOTE]
    >Per questa esercitazione verranno usati l'account di archiviazione e il contenitore predefiniti creati con il nuovo contesto di calcolo. Questi vengono immessi automaticamente nel modulo.

1. Selezionare **Upload from local file** (Carica da file locale). Da qui viene creato un nuovo set di dati con il file **bankmarketing_train. csv** scaricato in precedenza per questa esercitazione. 

    1. Scegliere **Sfoglia** e quindi selezionare il file **bankmarketing_train.csv** nel computer locale. 

    1. Assegnare un nome univoco al set di dati e specificare una descrizione facoltativa. 

    1. Scegliere **Avanti** per caricarlo nel contenitore predefinito configurato automaticamente durante la creazione dell'area di lavoro. L'anteprima pubblica supporta solo i caricamenti di file locali. 

    1. Al termine del caricamento, il modulo **Settings and preview** (Impostazioni e anteprima) viene popolato in modo intelligente in base al tipo di file. Verificare che il modulo sia popolato come indicato di seguito.
        
        Campo|Valore
        ---|---
        Formato file| Delimitato
        Delimitatore| Virgola
        Codifica| UTF-8
        Intestazioni di colonna| Tutti i file hanno le stesse intestazioni            Skip rows (Ignora righe) | Nessuna

        >[!NOTE]
        > Se viene aggiornata una delle impostazioni di questo modulo, l'anteprima verrà aggiornata di conseguenza.

        Selezionare **Avanti**.
    

    1. Il modulo **Schema** consente di configurare ulteriormente i dati per questo esperimento. Per questo esempio, selezionare l'interruttore relativo alla caratteristica **day_of_week** in modo da non includerla per questo esperimento. Selezionare **Fine** per completare il caricamento del file e la creazione del set di dati per l'esperimento.

        ![Configurazione della scheda Anteprima](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

        
1. Selezionare **Classificazione** come attività di stima.

1. Selezionare **y** come colonna di destinazione, che indica le stime da eseguire. Questa colonna indica se il client ha sottoscritto o meno un deposito a termine.

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

   All'avvio dell'esperimento, viene visualizzata una schermata vuota con un messaggio di stato nella parte superiore.

Il processo di preparazione dell'esperimento richiede alcuni minuti. Al termine del processo, il messaggio di stato cambia in **Run is Running** (Esecuzione in corso).

##  <a name="view-experiment-details"></a>Visualizzare i dettagli sull'esperimento

Mentre l'esperimento continua, la schermata aggiorna il **grafico** e l'**elenco delle iterazioni** con le diverse iterazioni (modelli) eseguite. L'elenco delle iterazioni è in ordine in base al punteggio della metrica. Per impostazione predefinita, il modello che riceve il punteggio maggiore in base alla metrica **AUC_weighted** si trova all'inizio dell'elenco.

>[!WARNING]
> I processi di training richiedono alcuni minuti prima che venga completata l'esecuzione di ogni pipeline.

[![Dashboard dei dettagli di esecuzione](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Distribuire il modello

Usando Machine Learning automatizzato nella pagina di destinazione dell'area di lavoro, è possibile distribuire il modello migliore come servizio Web per eseguire stime sui nuovi dati e identificare le potenziali aree di opportunità. Per questo esperimento, attraverso la distribuzione l'istituto finanziario ha ora una soluzione iterativa e scalabile per l'identificazione dei potenziali clienti con deposito a termine fisso.

Nel contesto di questo esperimento **VotingEnsemble** viene considerato il modello migliore, in base alla metrica **AUC_weighted**.  Viene distribuito questo modello, ma tenere presente che il completamento della distribuzione richiede circa 20 minuti.

1. Nella pagina **Dettagli di esecuzione** scegliere il pulsante **Deploy Best Model** (Distribuisci modello migliore) nell'angolo in alto a destra.

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

1. Accedere al [portale di Azure](https://portal.azure.com//). Passare all'area di lavoro e nel riquadro di sinistra **Asset** selezionare **Distribuzioni**. 

1. Selezionare la distribuzione che si vuole eliminare e scegliere **Elimina**. 

1. Selezionare **Continua**.

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione di Machine Learning automatizzato è stata usata la pagina di destinazione dell'area di lavoro per creare e distribuire un modello di classificazione. Per altre informazioni e per i passaggi successivi, vedere questi articoli:

> [!div class="nextstepaction"]
> [Utilizzare un servizio Web](how-to-consume-web-service.md)

+ Altre informazioni sulla [pre-elaborazione](how-to-create-portal-experiments.md#preprocess).
+ Altre informazioni sulla [profilatura dei dati](how-to-create-portal-experiments.md#profile).
+ [Funzionalità automatizzate di Machine Learning](concept-automated-ml.md).

>[!NOTE]
> Questo set di dati di marketing bancario viene reso disponibile in [Creative Comm (CCO: Public Domain) License](https://creativecommons.org/publicdomain/zero/1.0/). Tutti i diritti per i singoli contenuti del database vengono concessi in licenza ai sensi della [licenza relativa ai contenuti del database](https://creativecommons.org/publicdomain/zero/1.0/) e resi disponibili in [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Questo set di dati era originariamente disponibile all'interno del [database di Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez e P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, giugno 2014.
