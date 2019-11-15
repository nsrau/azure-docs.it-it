---
title: Creare il primo esperimento di classificazione con Machine Learning automatizzato
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training e la distribuzione di un modello di classificazione con Machine Learning automatizzato in Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: ecad41097786a40f7c605a686f085136856c950a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581566"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Esercitazione: Creare il primo modello di classificazione con apprendimento automatico
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

In questa esercitazione viene descritto come creare il primo esperimento di Machine Learning automatizzato in Azure Machine Learning Studio senza scrivere una sola riga di codice. Questo esempio crea un modello di classificazione per stimare se un cliente sottoscriverà un deposito a termine fisso presso un istituto finanziario.

Con l'apprendimento automatico automatizzato, è possibile automatizzare le attività a elevato utilizzo di tempo. L'apprendimento automatico automatizzato esegue rapidamente l'iterazione su numerose combinazioni di algoritmi e iperparametri per aiutare a trovare il modello migliore in base a una metrica di riuscita di propria scelta.

In questa esercitazione si apprenderà come eseguire le attività seguenti:

> [!div class="checklist"]
> * Creare un'area di lavoro di Azure Machine Learning.
> * Eseguire un esperimento di Machine Learning automatizzato.
> * Visualizzare i dettagli sull'esperimento.
> * Distribuire il modello.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).

* Scaricare il file di dati [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv). La colonna **y** indica se un cliente ha effettuato la sottoscrizione di un deposito a termine fisso, che in seguito viene identificata come colonna di destinazione per le stime in questa esercitazione. 

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Un'area di lavoro di Machine Learning è una risorsa cloud fondamentale usata per eseguire gli esperimenti, il training e la distribuzione di modelli di Machine Learning. Collega la sottoscrizione e il gruppo di risorse di Azure a un oggetto di facile utilizzo nel servizio. 

Per creare un'area di lavoro tramite Azure Machine Learning Studio, è necessario usare una console basata sul Web per la gestione delle risorse di Azure.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Prendere nota dell'**area di lavoro** e della **sottoscrizione**. Si tratta di informazioni necessarie per assicurarsi di creare l'esperimento nel posto giusto. 

## <a name="create-and-run-the-experiment"></a>Creare ed eseguire l'esperimento

Completare i passaggi seguenti di configurazione ed esecuzione dell'esperimento in Azure Machine Learning Studio, un'interfaccia consolidata che include strumenti di Machine Learning per l'esecuzione di scenari di data science per esperti di data science di qualsiasi livello di competenza. Studio non è supportato nei browser Internet Explorer.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. Selezionare la sottoscrizione e l'area di lavoro create.

1. Selezionare **Attività iniziali**.

1. Nel riquadro sinistro selezionare **Automated ML** (Machine Learning automatizzato) nella sezione **Creazione**.

   Poiché si tratta del primo esperimento di Machine Learning automatizzato, verranno visualizzati un elenco vuoto e collegamenti alla documentazione.

   ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Selezionare **New automated ML run** (Nuova esecuzione di ML automatizzato). 

1. Creare un nuovo set di dati selezionando **From local files** (Da file locali) dall'elenco a discesa **+Create dataset** (Crea set di dati). 

    1. Selezionare **Esplora**.
    
    1. Scegliere il file **bankmarketing_train.csv** nel computer locale. Si tratta del file scaricato come [prerequisito](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Selezionare **Tabular** (Tabulare) come tipo di set di dati. 

    1. Assegnare un nome univoco al set di dati e specificare una descrizione facoltativa. 

    1. Selezionare **Avanti** nell'angolo in basso a sinistra per caricarlo nel contenitore predefinito configurato automaticamente durante la creazione dell'area di lavoro.  
    
       Al termine del caricamento, il modulo Settings and preview (Impostazioni e anteprima) viene prepopolato in base al tipo di file. 
       
    1. Verificare che il modulo **Settings and preview** (Impostazioni e anteprima) venga popolato come segue, quindi selezionare **Avanti**.
        
        Campo|DESCRIZIONE| Valore per l'esercitazione
        ---|---|---
        Formato file|Definisce il layout e il tipo di dati archiviati in un file.| Delimitato
        Delimitatore|Uno o più caratteri per specificare il limite tra&nbsp; aree distinte indipendenti in testo normale o altri flussi di dati. |Virgola
        Codifica|Identifica la tabella dello schema bit-carattere da usare per leggere il set di dati.| UTF-8
        Intestazioni di colonna| Indica come verranno considerate le intestazioni del set di dati, se presenti.| Tutti i file hanno le stesse intestazioni
        Ignora righe | Indica quante righe vengono eventualmente ignorate nel set di dati.| Nessuna

    1. Il modulo **Schema** consente di configurare ulteriormente i dati per questo esperimento. Per questo esempio, selezionare l'interruttore relativo alla caratteristica **day_of_week** in modo da non includerla per questo esperimento. Selezionare **Avanti**.

        ![Configurazione della scheda Anteprima](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

    1. Nel modulo **Confirm details** (Conferma dettagli) verificare che le informazioni corrispondano a quelle con cui erano stati precedentemente popolati i moduli **Basic info** (informazioni di base) e **Settings and preview** (Impostazioni e anteprima).
    1. Selezionare **Crea** per completare la creazione del set di dati.
    1. Selezionare il set di dati quando viene visualizzato nell'elenco.
    1. Esaminare l'**anteprima dati** per assicurarsi di non avere incluso **day_of_week** e quindi scegliere **OK**.

    1. Selezionare **Avanti**.

1. Popolare il modulo **Configure Run** (Configura esecuzione) come segue:
    1. Immettere questo nome di esperimento: `my-1st-automl-experiment`

    1. Selezionare **y** come colonna di destinazione, che indica le stime da eseguire. Questa colonna indica se il client ha sottoscritto o meno un deposito a termine.
    1. Selezionare **Create a new compute** (Crea nuovo calcolo) e configurare la destinazione di calcolo. Una destinazione di calcolo è un ambiente di risorse locale o basato sul cloud usato per eseguire lo script di training o per ospitare la distribuzione del servizio. Per questo esperimento viene usato un calcolo basato sul cloud. 

        Campo | DESCRIZIONE | Valore per l'esercitazione
        ----|---|---
        Nome del calcolo |Un nome univoco che identifica il contesto di calcolo.|automl-compute
        Dimensioni&nbsp;della macchina&nbsp;virtuale| Selezionare le dimensioni della macchina virtuale per il contesto di calcolo.|Standard_DS12_V2
        Numero minimo/massimo di nodi (in Impostazioni avanzate)| Per profilare i dati, è necessario specificare almeno un nodo.|Numero minimo di nodi: 1<br>Numero massimo di nodi: 6
  
        1. Selezionare **Crea** per ottenere la destinazione di calcolo. 

            **Il completamento dell'operazione richiede alcuni minuti.** 

        1. Al termine della creazione, selezionare la nuova destinazione di calcolo dall'elenco a discesa.

    1. Selezionare **Avanti**.

1. Nel modulo **Task type and settings** (Tipo di attività e impostazioni) selezionare **Classificazione** come tipo di attività di Machine Learning.

    1. Selezionare **View additional configuration settings** (Visualizza altre impostazioni di configurazione) e popolare i campi come indicato di seguito. Queste impostazioni consentono un maggior controllo del processo di training. Altrimenti, vengono applicate le impostazioni predefinite in base alla selezione dell'esperimento e ai dati.

        >[!NOTE]
        > In questa esercitazione non viene impostato un punteggio di metrica o un numero massimo di core come soglia per le iterazioni. E non vengono neanche bloccati i test degli algoritmi.
   
        Configurazioni&nbsp;aggiuntive|DESCRIZIONE|Valore&nbsp;per&nbsp;l'esercitazione
        ------|---------|---
        Primary metric (Metrica principale)| Metrica di valutazione in base a cui verrà misurato l'algoritmo di Machine Learning.|AUC_weighted
        Automatic featurization (Definizione automatica funzionalità)| Consente la pre-elaborazione dei dati. Sono incluse la pulizia automatica dei dati, la preparazione e la trasformazione per generare funzionalità sintetiche.| Abilita
        Blocked algorithms (Algoritmi bloccati) | Algoritmi da escludere dal processo di training| Nessuna
        Exit criterion (Esci da criterio)| Se viene soddisfatto un criterio, il processo di training viene arrestato. |Durata del&nbsp;processo&nbsp;di training (ore): 1 <br> Soglia&nbsp;punteggio&nbsp;metrica: Nessuna
        Convalida | Scegliere un tipo di convalida incrociata e un numero di test.|Tipo di convalida:<br>Convalida incrociata &nbsp;k-fold&nbsp; <br> <br> Numero di convalide: 2
        Concorrenza| Numero massimo di iterazioni parallele eseguite e di core usati per iterazione| Numero massimo di&nbsp;iterazioni&nbsp;simultanee: 5<br> Numero massimo di&nbsp;core&nbsp;per&nbsp;iterazione: Nessuna
        
        Selezionare **OK**.

1. Selezionare **Crea** per eseguire l'esperimento. Verrà visualizzata la schermata **Dettagli esecuzione** con **Stato dell'esecuzione** quando inizia la preparazione dell'esperimento.

>[!IMPORTANT]
> La preparazione dell'esecuzione dell'esperimento richiede **10-15 minuti**.
> Dopo l'avvio, **ogni iterazione richiede almeno 2-3 minuti**.  
> Selezionare a intervalli regolari **Aggiorna** per visualizzare lo stato dell'esecuzione durante l'avanzamento dell'esperimento.
>
> In produzione, è probabile che nell'attesa ci si allontani. Per questa esercitazione però è consigliabile iniziare a esplorare gli algoritmi testati nella scheda Modelli non appena vengono completati mentre gli altri sono ancora in esecuzione. 

##  <a name="explore-models"></a>Esplorare i modelli

Passare alla scheda **Modelli** per visualizzare gli algoritmi (modelli) testati. Per impostazione predefinita, i modelli sono ordinati in base al punteggio della metrica man mano che vengono completati. Per questa esercitazione, il modello che riceve il punteggio più alto in base alla metrica **AUC_weighted** scelta si trova all'inizio dell'elenco.

Mentre si aspetta il completamento di tutti i modelli dell'esperimento, selezionare il **nome di algoritmo** di un modello completato per esplorare i dettagli delle relative prestazioni. 

Di seguito vengono esaminate le schede **Dettagli del modello** e **Visualizzazioni** per visualizzare le proprietà, le metriche e i grafici delle prestazioni del modello selezionato. 

![Dettagli sull'esecuzione delle iterazioni](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Distribuire il modello

Machine Learning automatizzato in Azure Machine Learning Studio consente di distribuire il modello migliore come servizio Web con pochi passaggi. La distribuzione è l'integrazione del modello per consentire la stima su nuovi dati e identificare le potenziali aree di opportunità. 

Per questo esperimento, attraverso la distribuzione a un servizio Web l'istituto finanziario ha ora una soluzione Web iterativa e scalabile per l'identificazione dei potenziali clienti con deposito a termine fisso. 

Al termine dell'esecuzione, tornare alla pagina **Dettagli esecuzione** e selezionare la scheda **Modelli**. Selezionare **Aggiorna**. 

Nel contesto di questo esperimento **VotingEnsemble** viene considerato il modello migliore, in base alla metrica **AUC_weighted**.  Viene distribuito questo modello, ma tenere presente che il completamento della distribuzione richiede circa 20 minuti. Il processo di distribuzione comporta diversi passaggi, tra cui la registrazione del modello, la generazione delle risorse e la relativa configurazione per il servizio Web.

1. Selezionare il pulsante **Deploy Best Model** (Distribuisci modello migliore) nell'angolo in basso a sinistra.

1. Immettere i dati nel riquadro **Deploy a model** (Distribuisci un modello) in questo modo:

    Campo| Valore
    ----|----
    Nome distribuzione| my-automl-deploy
    Descrizione della distribuzione| Distribuzione del primo esperimento automatizzato di apprendimento automatico
    Tipo di calcolo | Selezionare un'istanza di calcolo di Azure
    Abilita autenticazione| Disabilitare. 
    Use custom deployments (Usa distribuzioni personalizzate)| Disabilitare. Consente di generare automaticamente il file di driver predefinito (script di assegnazione dei punteggi) e il file dell'ambiente. 
    
    Per questo esempio, vengono usate le impostazioni predefinite disponibili nel menu *Avanzate*. 

1. Selezionare **Distribuisci**.  

    Nella parte superiore della schermata **Esegui** viene visualizzato un messaggio verde di operazione riuscita e nel riquadro **Recommended model** (Modello consigliato) viene visualizzato un messaggio di stato in **Deploy status** (Stato distribuzione). Selezionare a intervalli regolari **Aggiorna** per controllare lo stato della distribuzione.
    
A questo punto, è disponibile un servizio Web operativo per generare stime. 

Andare a [**Passaggi successivi**](#next-steps) per altre informazioni su come usare il nuovo servizio Web e testare le stime mediante il supporto di Azure Machine Learning incorporato in Power BI.

## <a name="clean-up-resources"></a>Pulire le risorse

I file di distribuzione sono più grandi dei file di dati e di esperimento e di conseguenza più costosi da archiviare. Eliminare solo i file di distribuzione per ridurre al minimo i costi per l'account o se si vuole conservare i file dell'area di lavoro e degli esperimenti. In caso contrario, eliminare l'intero gruppo di risorse, se non si prevede di usare alcun file.  

### <a name="delete-the-deployment-instance"></a>Eliminare l'istanza di distribuzione

Eliminare solo l'istanza di distribuzione da Azure Machine Learning Studio se si intende mantenere il gruppo di risorse e l'area di lavoro per altre esercitazioni e attività di esplorazione. 

1. Passare ad [Azure Machine Learning Studio](https://ml.azure.com/). Passare all'area di lavoro e nel riquadro di sinistra **Risorse** selezionare **Endpoint**. 

1. Selezionare la distribuzione che si vuole eliminare e scegliere **Elimina**. 

1. Selezionare **Continua**.

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione di Machine Learning automatizzato è stato usato Azure Machine Learning Studio per creare e distribuire un modello di classificazione. Per altre informazioni e per i passaggi successivi, vedere questi articoli:

> [!div class="nextstepaction"]
> [Utilizzare un servizio Web](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Altre informazioni sulla [pre-elaborazione](how-to-create-portal-experiments.md#preprocess).
+ Altre informazioni sulla [profilatura dei dati](how-to-create-portal-experiments.md#profile).
+ [Funzionalità automatizzate di Machine Learning](concept-automated-ml.md).
+ Per altre informazioni sulle metriche e sui grafici di classificazione, vedere l'articolo [Informazioni sui risultati di Machine Learning automatizzato](how-to-understand-automated-ml.md#classification).

>[!NOTE]
> Questo set di dati di marketing bancario viene reso disponibile in [Creative Comm (CCO: Public Domain) License](https://creativecommons.org/publicdomain/zero/1.0/). Tutti i diritti per i singoli contenuti del database vengono concessi in licenza ai sensi della [licenza relativa ai contenuti del database](https://creativecommons.org/publicdomain/zero/1.0/) e resi disponibili in [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Questo set di dati era originariamente disponibile all'interno del [database di Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez e P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, giugno 2014.
