---
title: Compilare & distribuire modelli di Machine Learning automatici
titleSuffix: Azure Machine Learning
description: Creazione, gestione e distribuzione di esperimenti automatici di Machine Learning in Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 25e775cf7bfd415768144b28ab2ca6989f360edd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818498"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learning-studio"></a>Crea, Esplora e Distribuisci esperimenti di Machine Learning automatici con Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

 Questo articolo illustra come creare, esplorare e distribuire esperimenti automatici di Machine Learning in Azure Machine Learning Studio senza una singola riga di codice. Automatizzato Machine Learning consente di automatizzare il processo di selezione dell'algoritmo migliore da usare per i dati specifici, in modo da poter generare rapidamente un modello di machine learning. [Altre informazioni su Machine Learning automatizzato](concept-automated-ml.md).

 Se si preferisce un'esperienza più basata sul codice, è anche possibile [configurare gli esperimenti di Machine Learning automatici in Python](how-to-configure-auto-train.md) con l' [SDK di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro di Azure Machine Learning con un tipo di **Enterprise Edition**. Vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).  Per aggiornare un'area di lavoro esistente a Enterprise Edition, vedere [eseguire l'aggiornamento a Enterprise Edition](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Introduzione

1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com). 

1. Selezionare la sottoscrizione e l'area di lavoro. 

1. Passare al riquadro sinistro. Selezionare **automatizzato ml** nella sezione **autore** .

[riquadro di spostamento ![Azure Machine Learning Studio](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Se è la prima volta che si esegue un esperimento, verrà visualizzato un elenco vuoto e i collegamenti alla documentazione. 

In caso contrario, verrà visualizzato un elenco dei recenti esperimenti automatici di Machine Learning, inclusi quelli creati con l'SDK. 

## <a name="create-and-run-experiment"></a>Creazione ed esecuzione dell'esperimento

1. Selezionare **+ Crea esperimento** e popolare il modulo.

1. Selezionare un set di dati dal contenitore di archiviazione o creare un nuovo set di dati. I set di dati possono essere creati da file locali, URL Web, archivi dati o set di dati aperti di Azure. 

    >[!Important]
    > Requisiti per i dati di training:
    >* I dati devono essere in formato tabulare.
    >* Il valore che si desidera stimare (colonna di destinazione) deve essere presente nei dati.

    1. Per creare un nuovo set di dati da un file nel computer locale, selezionare **Sfoglia** e quindi selezionare il file. 

    1. Assegnare un nome univoco al set di dati e specificare una descrizione facoltativa. 

    1. Selezionare **Avanti**per caricarlo nel contenitore di archiviazione predefinito creato automaticamente con l'area di lavoro oppure scegliere un contenitore di archiviazione da usare per l'esperimento. 

    1. Esaminare le **Impostazioni e** il modulo di anteprima per verificarne l'accuratezza. Il modulo viene popolato in modo intelligente in base al tipo di file. 

        Campo| Descrizione
        ----|----
        Formato file| Definisce il layout e il tipo di dati archiviati in un file.
        Delimitatore| Uno o più caratteri per specificare il limite tra aree separate indipendenti in testo normale o altri flussi di dati.
        Codifica| Identifica la tabella dello schema da utilizzare per la lettura del set di dati.
        Intestazioni di colonna| Indica il modo in cui verranno gestite le intestazioni del set di dati.
        Ignora righe | Indica il numero di righe, se presenti, ignorate nel set di dati.
    
        Selezionare **Avanti**.

    1. Il form **dello schema** viene popolato in modo intelligente in base alle selezioni nelle **Impostazioni e** nel modulo di anteprima. Qui configurare il tipo di dati per ogni colonna, rivedere i nomi delle colonne e selezionare le colonne da **non includere** per l'esperimento. 
            
        Selezionare **Avanti.**

    1. Il modulo **Confirm Details** è un riepilogo delle informazioni popolate in precedenza nelle **informazioni di base** e nelle **Impostazioni e** nei moduli di anteprima. È anche possibile profilare il set di dati usando un calcolo abilitato per la profilatura. Altre informazioni sulla [profilatura dei dati](#profile).

        Selezionare **Avanti**.
1. Selezionare il set di dati appena creato dopo che è stato visualizzato. È anche possibile visualizzare un'anteprima del set di dati e delle statistiche di esempio. 

1. Nel modulo **Configura esecuzione** , immettere un nome di esperimento univoco.

1. Selezionare una colonna di destinazione. si tratta della colonna su cui si desidera eseguire stime.

1. Selezionare un calcolo per il processo di profiling dei dati e di training. Un elenco di calcoli esistenti è disponibile nell'elenco a discesa. Per creare un nuovo calcolo, seguire le istruzioni riportate nel passaggio 7.

1. Selezionare **Crea nuovo calcolo** per configurare il contesto di calcolo per questo esperimento.

    Campo|Descrizione
    ---|---
    Nome del calcolo| Immettere un nome univoco che identifichi il contesto di calcolo.
    Dimensioni della macchina virtuale| Selezionare le dimensioni della macchina virtuale per il contesto di calcolo.
    Numero minimo/massimo di nodi (in Impostazioni avanzate)| Per profilare i dati, è necessario specificare almeno un nodo. Immettere il numero massimo di nodi per il calcolo. Il valore predefinito è 6 nodi per un calcolo AML.
    
    Selezionare **Crea**. La creazione di un nuovo calcolo può richiedere alcuni minuti.

    >[!NOTE]
    > Il nome di calcolo indicherà se la *profilatura*del calcolo selezionato/creato è abilitata. Per altri dettagli, vedere la sezione [profiling dei dati](#profile) .

    Selezionare **Avanti**.

1. Nel modulo **tipo di attività e impostazioni** selezionare il tipo di attività, ovvero classificazione, regressione o previsione. Per ulteriori informazioni [, vedere come definire i tipi di attività](how-to-define-task-type.md) .

    1. Per la classificazione, è anche possibile abilitare l'apprendimento avanzato usato per featurizations di testo.

    1. Per la previsione:
        1. Seleziona colonna temporale: questa colonna contiene i dati relativi all'ora da usare.

        1. Select Forecast Horizon: indica il numero di unità di tempo (minuti/ore/giorni/settimane/mesi/anni) che il modello sarà in grado di stimare in futuro. Maggiore è il modello necessario per prevedere il futuro, minore sarà il livello di precisione. [Altre informazioni sulla previsione e sull'orizzonte di previsione](how-to-auto-train-forecast.md).

1. Opzionale Configurazioni di aggiunta: impostazioni aggiuntive che è possibile usare per controllare meglio il processo di training. In caso contrario, vengono applicati i valori predefiniti in base alla selezione dell'esperimento e ai dati. 

    Configurazioni aggiuntive|Descrizione
    ------|------
    Primary metric (Metrica principale)| Metrica principale usata per assegnare un punteggio al modello. [Altre informazioni sulle metriche del modello](how-to-configure-auto-train.md#explore-model-metrics).
    Conteggi automatico| Selezionare questa impostazione per abilitare o disabilitare la pre-elaborazione eseguita da Automatic Machine Learning. La pre-elaborazione include la pulizia automatica dei dati, la preparazione e la trasformazione per generare funzionalità sintetiche. [Altre informazioni sulla pre-elaborazione](#preprocess).
    Algoritmo bloccato| Selezionare gli algoritmi che si desidera escludere dal processo di training.
    Criterio di uscita| Quando uno di questi criteri viene soddisfatto, il processo di training viene arrestato. <br> *Tempo del processo di training (ore)* : per quanto tempo consentire l'esecuzione del processo di training. <br> *Soglia Punteggio metrica*: Punteggio metrica minimo per tutte le pipeline. In questo modo si garantisce che, se si dispone di una metrica di destinazione definita che si desidera raggiungere, non si dedica più tempo al processo di training del necessario.
    Convalida| Selezionare una delle opzioni di convalida incrociata da usare nel processo di training. [Altre informazioni sulla convalida incrociata](how-to-configure-auto-train.md).
    Concorrenza| Numero massimo di *iterazioni simultanee*: numero massimo di pipeline (iterazioni) da testare nel processo di training. Il processo non viene eseguito più del numero specificato di iterazioni. <br> *Numero massimo di core per iterazione*: selezionare i limiti multicore da usare quando si usa il calcolo multicore.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Statistiche di riepilogo & Profiling dati

È possibile ottenere un'ampia gamma di statistiche di riepilogo nel set di dati per verificare se il set di dati è pronto per ML. Per le colonne non numeriche, includono solo le statistiche di base come min, Max e il numero di errori. Per le colonne numeriche, è anche possibile esaminare i relativi momenti statistici e stimare quantili. In particolare, il profilo dati include:

>[!NOTE]
> Vengono visualizzate voci vuote per le funzionalità con tipi irrilevanti.

Statistica|Descrizione
------|------
Funzionalità| Nome della colonna riepilogata.
Profilo| Visualizzazione inline basata sul tipo dedotto. Ad esempio, le stringhe, i valori booleani e le date avranno conteggi dei valori, mentre i decimali (numerici) hanno istogrammi approssimati. In questo modo è possibile acquisire una rapida conoscenza della distribuzione dei dati.
Distribuzione del tipo| Conteggio dei valori in linea di tipi all'interno di una colonna. I valori null sono di tipo, quindi questa visualizzazione è utile per rilevare valori dispari o mancanti.
Tipo|Tipo dedotto della colonna. I valori possibili sono: stringhe, valori booleani, date e decimali.
Min| Valore minimo della colonna. Vengono visualizzate voci vuote per le funzionalità il cui tipo non ha un ordinamento intrinseco, ad esempio i valori booleani.
Max| Valore massimo della colonna. 
Numero| Numero totale di voci mancanti e non mancanti nella colonna.
Totale non mancanti| Numero di voci nella colonna mancanti. Le stringhe e gli errori vuoti vengono considerati come valori, quindi non contribuiscono al "conteggio mancante".
Quantili| Valori approssimati in ogni quantile per fornire un senso della distribuzione dei dati.
Media| Media aritmetica o media della colonna.
Deviazione standard| Misura della quantità di dispersione o variazione dei dati di questa colonna.
Variance| Misura della diffusione dei dati di questa colonna dal relativo valore medio. 
Asimmetria| Misura del modo in cui i dati della colonna sono diversi da una distribuzione normale.
Curtosi| Misura della quantità di dati di questa colonna rispetto a una distribuzione normale.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Opzioni di pre-elaborazione avanzate

Quando si configurano gli esperimenti, è possibile abilitare l'impostazione avanzata `Preprocess`. In questo modo, vengono eseguiti automaticamente i passaggi di pre-elaborazione e di conteggi dei dati seguenti.

|Pre-elaborazione di&nbsp;passaggi| Descrizione |
| ------------- | ------------- |
|Eliminazione delle caratteristiche con elevata cardinalità o senza varianza|Eliminarli dai set di training e di convalida, incluse le funzionalità con tutti i valori mancanti, lo stesso valore in tutte le righe o con cardinalità estremamente elevata (ad esempio, hash, ID o GUID).|
|Attribuire i valori mancanti|Per le funzionalità numeriche, imputare alla media dei valori nella colonna.<br/><br/>Per le funzionalità categoriche, imputare il valore più frequente.|
|Generazione di caratteristiche aggiuntive|Per le funzionalità DateTime: anno, mese, giorno, giorno della settimana, giorno dell'anno, trimestre, settimana dell'anno, ora, minuto, secondo.<br/><br/>Per le funzionalità di testo: frequenza dei termini basata su unigrammi, bi-grammi e Tri-caratteri-grammi.|
|Trasformare e codificare |Le funzionalità numeriche con pochi valori univoci vengono trasformate in funzionalità categoriche.<br/><br/>La codifica One-Hot viene eseguita per una cardinalità categorica bassa; per la cardinalità elevata, codifica One-Hot-hash.|
|Incorporamenti di Word|Featurizer di testo che converte i vettori di token di testo in vettori di frase usando un modello con training preliminare. Il vettore di incorporamento di ogni parola in un documento viene aggregato insieme per produrre un vettore di funzionalità del documento.|
|Codifiche di destinazione|Per le funzionalità categoriche, esegue il mapping di ogni categoria con il valore di destinazione medio per i problemi di regressione e la probabilità della classe per ogni classe per i problemi di classificazione. Viene applicata la ponderazione basata sulla frequenza e la convalida incrociata k-fold per ridurre l'adattamento del mapping e del rumore causato da categorie di dati di tipo sparse.|
|Codifica della destinazione del testo|Per l'input di testo, viene usato un modello lineare in pila con il contenitore di parole per generare la probabilità di ogni classe.|
|Peso delle evidenze (guai)|Calcola il dolore come misura della correlazione delle colonne categoriche alla colonna di destinazione. Viene calcolato come log del rapporto tra le probabilità out-of-Class di Visual Studio. Questo passaggio restituisce una colonna di funzionalità numerica per classe e rimuove la necessità di imputare in modo esplicito i valori mancanti e il trattamento degli outlier.|
|Distanza del cluster|Addestra un modello di clustering k-means su tutte le colonne numeriche.  Restituisce k nuove funzionalità, una nuova funzionalità numerica per ogni cluster, che contiene la distanza di ogni campione al centro di ogni cluster.|

## <a name="run-experiment-and-view-results"></a>Eseguire l'esperimento e visualizzare i risultati

Selezionare **Start (avvia** ) per eseguire l'esperimento. L'esperimento di preparazione del processo può richiedere fino a 10 minuti. Per completare l'esecuzione di ogni pipeline, i processi di training possono richiedere altri 2-3 minuti.

### <a name="view-experiment-details"></a>Visualizzare i dettagli sull'esperimento

>[!NOTE]
> Selezionare **Aggiorna** periodicamente per visualizzare lo stato dell'esecuzione. 

Verrà visualizzata la schermata **Esegui dettagli** nella scheda **Dettagli** . Questa schermata mostra un riepilogo dell'esecuzione dell'esperimento, incluso lo **stato dell'esecuzione**. 

La scheda **modelli** contiene un elenco dei modelli creati in base al punteggio della metrica. Per impostazione predefinita, il modello che assegna un punteggio al più alto in base alla metrica scelta si trova nella parte superiore dell'elenco. Quando il processo di training tenta un maggior numero di modelli, questi vengono aggiunti all'elenco. Usare questa procedura per ottenere un rapido confronto delle metriche per i modelli prodotti finora.

[![Dashboard dei dettagli di esecuzione](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Visualizza i dettagli dell'esecuzione di training

Eseguire il drill-down su uno dei modelli completati per visualizzare i dettagli delle esecuzioni di training, ad esempio le metriche di esecuzione nella scheda **Dettagli modello** o i grafici delle prestazioni nella scheda **visualizzazioni** . [altre informazioni sui grafici](how-to-understand-automated-ml.md).

[Dettagli iterazione ![](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Distribuire il modello

Una volta ottenuto il modello migliore, è necessario distribuirlo come servizio Web per prevedere i nuovi dati.

Automatizzato ML semplifica la distribuzione del modello senza scrivere codice:

1. Sono disponibili due opzioni per la distribuzione. 

    + Opzione 1: per distribuire il modello migliore (in base ai criteri della metrica definiti), selezionare Distribuisci modello migliore dalla scheda Dettagli.

    + Opzione 2: per distribuire un'iterazione del modello specifica da questo esperimento, eseguire il drill-down sul modello per aprire la scheda Dettagli modello e selezionare Distribuisci modello.

1. Popolare il riquadro **Distribuisci modello** .

    Campo| Valore
    ----|----
    Name| Immettere un nome univoco per la distribuzione.
    Descrizione| Immettere una descrizione per identificare meglio le finalità della distribuzione.
    Tipo di calcolo| Selezionare il tipo di endpoint che si vuole distribuire: *Azure Kubernetes Service (AKS)* o *Azure container instance (ACI)* .
    Name| *Si applica solo a AKS:* Selezionare il nome del cluster AKS in cui si vuole eseguire la distribuzione.
    Abilitare l'autenticazione | Selezionare questa impostazione per consentire l'autenticazione basata su token o basata su chiave.
    Usare asset di distribuzione personalizzati| Abilitare questa funzionalità se si desidera caricare lo script di assegnazione dei punteggi e il file dell'ambiente. [Altre informazioni sugli script per il Punteggio](how-to-deploy-and-where.md#script).

    >[!Important]
    > Il nome file deve essere inferiore a 32 caratteri e deve iniziare e terminare con caratteri alfanumerici. Può includere trattini, caratteri di sottolineatura, punti e caratteri alfanumerici tra. Gli spazi non sono consentiti.

    Il menu *Avanzate* offre funzionalità di distribuzione predefinite, ad esempio le impostazioni di [raccolta dei dati](how-to-enable-app-insights.md) e di utilizzo delle risorse. Se si desidera eseguire l'override di queste impostazioni predefinite, effettuare questa operazione in questo menu.

1. Selezionare **Distribuisci**. Il completamento della distribuzione può richiedere circa 20 minuti.

A questo punto si dispone di un servizio Web operativo per generare stime. È possibile testare le stime eseguendo una query sul servizio dal [supporto Azure Machine Learning incorporato Power bi](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Passaggi successivi

* Provare l'esercitazione end-to-end [per creare il primo esperimento di Machine Learning automatizzato con Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 
* [Scopri di più su Machine Learning e Azure Machine Learning automatizzati](concept-automated-ml.md) .
* [Informazioni sui risultati automatici di Machine Learning](how-to-understand-automated-ml.md).
* [Informazioni su come utilizzare un servizio Web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
