---
title: Usare AutoML per creare modelli & distribuire
titleSuffix: Azure Machine Learning
description: Creare, rivedere e distribuire modelli di Machine Learning automatizzato con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: aniththa
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/20/2020
ms.openlocfilehash: 9871d2ef46a4bbcaa0de7a2aee7d2c91f2bfefab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831914"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Creare, rivedere e distribuire modelli di Machine Learning automatizzato con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Questo articolo illustra come creare, esplorare e distribuire modelli di Machine Learning automatizzato senza una singola riga di codice nell'interfaccia di Azure Machine Learning Studio. Machine Learning automatizzato è un processo in cui viene selezionato il migliore algoritmo di Machine Learning da usare per i dati specifici. Questo processo consente di generare rapidamente modelli di Machine Learning. [Altre informazioni sulle funzionalità di Machine Learning automatizzato](concept-automated-ml.md).
 
Per un esempio end-to-end, provare l’[esercitazione per la creazione di un modello di classificazione con l’interfaccia di ML automatizzato di Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

Per un'esperienza basata su codice Python, [configurare gli esperimenti di Machine Learning automatizzato](how-to-configure-auto-train.md) con l'SDK di Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro di Azure Machine Learning con un tipo di **edizione Enterprise**. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).  Per aggiornare un'area di lavoro esistente all’edizione Enterprise, vedere [Eseguire l'aggiornamento all’edizione Enterprise](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Introduzione

1. Accedere ad Azure Machine Learning in https://ml.azure.com. 

1. Selezionare la sottoscrizione e l’area di lavoro. 

1. Passare al riquadro sinistro. Selezionare **ML automatizzato** nella sezione **Autore**.

[![Riquadro di spostamento di Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Poiché si tratta del primo esperimento, verranno visualizzati un elenco vuoto e collegamenti alla documentazione. 

Altrimenti, verrà visualizzato un elenco degli esperimenti recenti di Machine Learning automatizzato, inclusi quelli creati con l'SDK. 

## <a name="create-and-run-experiment"></a>Creare ed eseguire l'esperimento

1. Selezionare **+ Nuova esecuzione di ML automatizzato** e popolare il modulo.

1. Selezionare un set di dati dal contenitore di archiviazione o creare un nuovo set di dati. I set di dati possono essere creati da file locali, URL Web, archivi dati o set di dati aperti di Azure. 

    >[!Important]
    > Requisiti per i dati di training:
    >* I dati devono essere in formato tabulare.
    >* Il valore che si desidera prevedere (colonna di destinazione) deve essere presente nei dati.

    1. Per creare un nuovo set di dati da un file nel computer locale, selezionare **Sfoglia**, quindi selezionare il file. 

    1. Assegnare un nome univoco al set di dati e specificare una descrizione facoltativa. 

    1. Selezionare **Avanti** per aprire il **modulo di selezione file e archivio dati**. In questo modulo è possibile selezionare la posizione in cui caricare il set di dati, il contenitore di archiviazione predefinito creato automaticamente con l'area di lavoro o scegliere un contenitore di archiviazione da usare per l'esperimento. 

    1. Esaminare il modulo **Impostazioni e anteprima** per verificarne l'accuratezza. Il modulo viene popolato in modo intelligente in base al tipo di file. 

        Campo| Descrizione
        ----|----
        Formato file| Definisce il layout e il tipo di dati archiviati in un file.
        Delimitatore| Uno o più caratteri per specificare il limite tra aree distinte indipendenti in testo normale o altri flussi di dati.
        Codifica| Identifica la tabella dello schema bit-carattere da usare per leggere il set di dati.
        Intestazioni di colonna| Indica come verranno considerate le intestazioni del set di dati, se presenti.
        Ignora righe | Indica quante righe vengono eventualmente ignorate nel set di dati.
    
        Selezionare **Avanti**.

    1. Il modulo **Schema** viene popolato in modo intelligente in base alle selezioni nel modulo **Impostazioni e anteprima**. Qui è possibile configurare il tipo di dati per ogni colonna, esaminare i nomi delle colonne e selezionare le colonne da **non includere** per l'esperimento. 
            
        Selezionare **Avanti.**

    1. Il modulo **Conferma dettagli** è un riepilogo delle informazioni con cui sono stati precedentemente popolati i moduli **Info di base** e **Impostazioni e anteprima**. È inoltre possibile creare un profilo dati per il set di dati usando un ambiente di calcolo abilitato per la profilatura. Altre informazioni sulla [profilatura dei dati](#profile).

        Selezionare **Avanti**.
1. Selezionare il set di dati appena creato quando viene visualizzato. È anche possibile visualizzare un'anteprima del set di dati e delle statistiche di esempio. 

1. Nel modulo **Configura esecuzione** immettere un nome di esperimento univoco.

1. Selezionare una colonna di destinazione. Si tratta della colonna su cui si desidera eseguire le previsioni.

1. Selezionare un ambiente di calcolo per il processo di training e profilatura dei dati. Un elenco degli ambienti di calcolo esistenti è disponibile nell'elenco a discesa. Per creare un nuovo ambiente di calcolo, seguire le istruzioni riportate nel passaggio 7.

1. Selezionare **Crea un nuovo ambiente di calcolo** per configurare il contesto di calcolo per questo esperimento.

    Campo|Descrizione
    ---|---
    Nome del calcolo| Immettere un nome univoco che identifichi il contesto di calcolo.
    Dimensioni della macchina virtuale| Selezionare le dimensioni della macchina virtuale per il contesto di calcolo.
    Numero minimo/massimo di nodi (in Impostazioni avanzate)| Per profilare i dati, è necessario specificare almeno un nodo. Immettere il numero massimo di nodi per l’ambiente di calcolo. Il valore predefinito è 6 nodi per un ambiente di calcolo di AML.
    
    Selezionare **Create** (Crea). La creazione di un nuovo ambiente di calcolo può richiedere alcuni minuti.

    >[!NOTE]
    > Il nome dell’ambiente di calcolo indicherà se l’ambiente di calcolo selezionato/creato è *abilitato per la profilatura*. Per ulteriori informazioni, vedere la sezione [Profilatura dei dati](#profile).

    Selezionare **Avanti**.

1. Nel modulo **Tipo di attività e impostazioni** selezionare il tipo di attività: classificazione, regressione o previsione. Per ulteriori informazioni, vedere [tipi di attività supportati](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) .

    1. Per la classificazione, è anche possibile abilitare il Deep Learning usato per la definizione delle funzionalità di testo.

    1. Per la previsione:
        1. Seleziona una colonna data/ora: questa colonna contiene i dati relativi a data/ora da usare.

        1. Seleziona un orizzonte di previsione: indica il numero di unità di tempo (minuti/ore/giorni/settimane/mesi/anni) per cui il modello sarà in grado di effettuare previsioni nel futuro. Maggiore è il tempo per il quale il modello deve effettuare previsioni nel futuro, minore sarà il livello di precisione. [Altre informazioni sulla previsione e sull'orizzonte di previsione](how-to-auto-train-forecast.md).

1. (Facoltativo) Visualizzare le impostazioni di configurazione aggiuntive: altre impostazioni che è possibile usare per controllare meglio il processo di training. Altrimenti, vengono applicate le impostazioni predefinite in base alla selezione dell'esperimento e ai dati. 

    Configurazioni aggiuntive|Descrizione
    ------|------
    Primary metric (Metrica principale)| Metrica principale usata per assegnare un punteggio al modello. [Altre informazioni sulle metriche dei modelli](how-to-configure-auto-train.md#explore-model-metrics).
    Automatic featurization (Definizione automatica funzionalità)| Selezionare questa impostazione per abilitare o disabilitare la conteggi eseguita da Automatic Machine Learning. Conteggi automatici include la pulizia automatica dei dati, la preparazione e la trasformazione per generare funzionalità sintetiche. Non supportata per il tipo di attività di previsione delle serie temporali. [Altre informazioni su conteggi](how-to-configure-auto-features.md#featurization). 
    Modello esplicativo migliore | Selezionare per abilitare o disabilitare la visualizzazione del modello esplicativo migliore consigliato
    Algoritmo bloccato| Selezionare gli algoritmi da escludere dal processo di training.
    Exit criterion (Esci da criterio)| Quando uno di questi criteri viene soddisfatto, il processo di training viene arrestato. <br> *Durata del processo di training (ore)* : per quanto tempo consentire l'esecuzione del processo di training. <br> *Soglia di punteggio metrica*:  punteggio di metrica minimo per tutte le pipeline. In questo modo si garantisce che, se si dispone di una metrica di destinazione definita che si desidera raggiungere, non si dedica più tempo del necessario al processo di training.
    Convalida| Selezionare una delle opzioni di convalida incrociata da usare nel processo di training. [Altre informazioni sulla convalida incrociata](how-to-configure-cross-validation-data-splits.md#prerequisites).
    Concorrenza| *Numero massimo di iterazioni simultanee*: numero massimo di pipeline (iterazioni) da testare nel processo di training. Il processo non viene eseguito più volte del numero specificato di iterazioni.

1. Opzionale Visualizza impostazioni conteggi: se si sceglie di abilitare **conteggi automatici** nel modulo **impostazioni di configurazione aggiuntive** , vengono applicate le tecniche conteggi predefinite. Nelle **impostazioni di visualizzazione conteggi** è possibile modificare queste impostazioni predefinite e personalizzarle di conseguenza. Informazioni su come [personalizzare featurizations](#customize-featurization). 

    ![Form del tipo di attività di Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Statistiche di riepilogo e profilatura dei dati

È possibile ottenere un'ampia gamma di statistiche di riepilogo nel set di dati per verificare se il set di dati è idoneo per il Machine Learning. Per le colonne non numeriche, sono incluse solo statistiche di base come min, max e conteggio errori. Per le colonne numeriche, è anche possibile esaminare i momenti statistici e i quantili stimati. In particolare, il profilo dati include:

>[!NOTE]
> Per le funzionalità con tipi irrilevanti vengono visualizzate voci vuote.

Statistiche|Descrizione
------|------
Funzionalità| Nome della colonna sottoposta a riepilogo.
Profilo| Visualizzazione in linea basata sul tipo dedotto. Ad esempio, le stringhe, i valori booleani e le date avranno conteggi di valori, mentre i decimali (numerici) avranno istogrammi approssimati. In questo modo è possibile ottenere una visione immediata della distribuzione dei dati.
Distribuzione dei tipi| Conteggio dei valori in linea dei tipi all'interno di una colonna. I valori Null hanno un proprio tipo, quindi questa visualizzazione è utile per rilevare valori strani o mancanti.
Type|Tipo dedotto della colonna. I valori possibili includono: stringhe, valori booleani, date e decimali.
Min| Valore minimo della colonna. Vengono visualizzate voci vuote per le funzionalità il cui tipo non ha un ordinamento intrinseco, ad esempio i valori booleani.
Max| Valore massimo della colonna. 
Conteggio| Numero totale di voci mancanti e non mancanti nella colonna.
Totale non mancanti| Numero di voci nella colonna che non sono mancanti. Le stringhe vuote e gli errori vengono considerati come valori, quindi non contribuiscono al "totale non mancanti".
Quantili| Valori approssimati a ogni quantile per una visione generale della distribuzione dei dati.
Media| Media aritmetica o media della colonna.
Deviazione standard| Misura della quantità di dispersione o variazione dei dati di questa colonna.
Variance| Misura della distribuzione dei dati di questa colonna rispetto al relativo valore medio. 
Asimmetria| Misura della differenza dei dati di questa colonna rispetto a una distribuzione normale.
Curtosi| Misura della pesantezza delle code dei dati di questa colonna in relazione a una distribuzione normale.

## <a name="customize-featurization"></a>Personalizzare conteggi

Nel modulo **conteggi** è possibile abilitare o disabilitare conteggi automatici e personalizzare le impostazioni conteggi automatiche per l'esperimento. Per aprire questo modulo, vedere il passaggio 10 nella sezione [creare ed eseguire l'esperimento](#create-and-run-experiment) . 

Nella tabella seguente sono riepilogate le personalizzazioni attualmente disponibili tramite Studio. 

Colonna| Personalizzazione
---|---
Incluso | Specifica le colonne da includere per il training.
Tipo di funzionalità| Modificare il tipo di valore per la colonna selezionata.
Imputa con| Selezionare il valore in cui si desidera imputare i valori mancanti nei dati.

![Form del tipo di attività di Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Eseguire l'esperimento e visualizzare i risultati

Selezionare **Fine** per eseguire l'esperimento. L'esperimento di preparazione del processo può richiedere fino a 10 minuti. Per completare l'esecuzione di ogni pipeline, i processi di training possono richiedere altri 2-3 minuti.

### <a name="view-experiment-details"></a>Visualizzare i dettagli sull'esperimento

Viene visualizzata la schermata **Dettagli esecuzione** nella scheda **Dettagli**. Questa schermata mostra un riepilogo dell'esecuzione dell'esperimento, inclusa una barra di stato in alto accanto al numero di esecuzione. 

La scheda **Modelli** contiene un elenco dei modelli creati, ordinati in base al punteggio della metrica. Per impostazione predefinita, il modello che riceve il punteggio più alto in base alla metrica scelta si trova all'inizio dell'elenco. Man mano che il processo di training prova altri modelli, questi vengono aggiunti all'elenco. Usare questa procedura per ottenere un rapido confronto delle metriche per i modelli prodotti finora.

[![Dashboard dei dettagli di esecuzione](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Visualizzare i dettagli relativi all'esecuzione del training

Eseguire il drill-down su uno dei modelli completati per visualizzare i dettagli dell’esecuzione del training, ad esempio le metriche di esecuzione nella scheda **Dettagli del modello** o i grafici delle prestazioni nella scheda **Visualizzazioni**. [Altre informazioni sui grafici](how-to-understand-automated-ml.md).

[![Dettagli sull'iterazione](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Distribuire il modello

Una volta ottenuto il modello migliore, è necessario distribuirlo come servizio Web per eseguire previsioni sui nuovi dati.

ML automatizzato semplifica la distribuzione del modello senza scrivere codice:

1. Per la distribuzione sono disponibili due opzioni. 

    + Opzione 1: Per distribuire il modello migliore (in base ai criteri della metrica definiti), selezionare il pulsante **Distribuisci modello migliore** nella scheda **Dettagli**.

    + Opzione 2: Per distribuire un'iterazione del modello specifica da questo esperimento, eseguire il drill-down sul modello per aprire la scheda **Dettagli del modello** e selezionare **Distribuisci modello**.

1. Inserire i dati nel riquadro **Distribuisci modello**.

    Campo| valore
    ----|----
    Nome| Specificare un nome univoco per la distribuzione.
    Descrizione| Immettere una descrizione per identificare meglio le finalità della distribuzione.
    Tipo di calcolo| Selezionare il tipo di endpoint da distribuire: *Servizio Azure Kubernetes* o *Istanza di contenitore di Azure*.
    Nome del calcolo| *Solo per il servizio Azure Kubernetes:* Selezionare il nome del cluster del servizio Azure Kubernetes in cui si vuole eseguire la distribuzione.
    Abilita autenticazione | Selezionare questa impostazione per consentire l'autenticazione basata su token o basata su chiave.
    Usa asset di distribuzione personalizzati| Abilitare questa funzionalità se si desidera caricare lo script di punteggio e il file di ambiente. [Altre informazioni sugli script di punteggio](how-to-deploy-and-where.md#script).

    >[!Important]
    > I nomi file devono essere inferiori a 32 caratteri e iniziare e terminare con caratteri alfanumerici. Possono includere trattini, caratteri di sottolineatura, punti e caratteri alfanumerici. Gli spazi non sono consentiti.

    Il menu *Avanzata* offre funzionalità di distribuzione predefinite, quali la [raccolta dati](how-to-enable-app-insights.md) e le impostazioni di utilizzo delle risorse. Se si desidera eseguire l'override di queste impostazioni predefinite, effettuare questa operazione in questo menu.

1. Selezionare **Distribuisci**. Il completamento della distribuzione può richiedere circa 20 minuti.
    Una volta avviata la distribuzione, viene visualizzata la scheda **Dettagli modello** . Vedere lo stato di avanzamento della distribuzione nella sezione **stato** di distribuzione del riquadro **Proprietà** . 

A questo punto, è disponibile un servizio Web operativo per generare previsioni. Per eseguire il test delle previsioni, è possibile eseguire una query sul servizio dal [supporto Azure Machine Learning incorporato di Power BI](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull’utilizzo di un servizio Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Informazioni sui risultati di Machine Learning automatizzato](how-to-understand-automated-ml.md).
* [Altre informazioni su Machine Learning automatizzato](concept-automated-ml.md) e Azure Machine Learning.
